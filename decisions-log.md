# Decisions log · cleaning and reconciliation · Brief A · Group <N>

> Assembled on 24 October 2026 from the analysis. Every number below was re-run against the source files on that date. The work is recorded as a team effort, so no individual attributions are given.

## What this log is, in one paragraph

This log explains how we moved from the raw exports to the final counts. It records each cleaning rule, what it changed, and the checks around every join. The final section follows the investigation from one question to the next. Where a conclusion depends on an assumption, we say so.

## 1. Reconciliation

Our headline is booked test lines on eligible visits with a slot date in January–June. Physical rows, test lines and visits are different units, so the table keeps them separate. Rules are applied in the order shown; the header rows are removed first so that they are not silently dropped as unparseable dates.

| Step | Table | Rows in | Rows kept | Rows rejected | In = kept + rejected? | Headline after this step |
| --- | --- | --- | --- | --- | --- | --- |
| 0 | Both systems before cleaning | 1,315,225 (legacy 1,252,786; Nova flattened 62,439) | — | — | — | Not yet established |
| 1 | Remove embedded legacy header rows (`booking_id == "booking_id"`) | 1,315,225 | 1,315,204 | 21 | Yes | Not yet established |
| 2 | Map city "Bangalore" to "Bengaluru" | 1,315,204 | 1,315,204 | 0 (193,558 rows relabelled across all legacy months; 100,070 of them in January–June) | Yes: relabelling removes nothing | Company total unchanged; Bengaluru city figures corrected |
| 3 | Keep slot dates January–June 2025 and 2026 | 1,315,204 | 897,846 (legacy 835,514; Nova 62,332) | 417,358 (legacy 417,251 July–December 2025 rows; Nova 107 July 2026 rows) | Yes | H1 2025: 436,302 test lines. H1 2026: 461,523 test lines (legacy 399,212 + Nova 62,311). Growth +5.78% |
| 4 | Remove Nova rescheduled originals (`status_at_export == "rescheduled"`) | 897,846 rows | 894,304 rows | 3,542 rows (1,500 bookings; 3,541 test lines + 1 empty-list row) | Yes | H1 2026: 457,982 test lines. Growth **+4.97%** |

**Row-to-test check.** The 894,304 eligible rows hold 894,284 test lines (436,302 + 457,982). The difference is 20 Nova bookings with an empty test list, kept as visits with zero tests (decision 8).

**Reschedule rule.** The originals were removed using `status_at_export`. All 3,542 original rows have January–June slot dates, so applying this rule before or after step 3 gives the same result. Identifying originals through `rescheduled_from` on a date-filtered table does not work: it finds only 1,456 of the 1,500, because 44 replacement bookings are slotted in July.

The final comparison is **436,302 → 457,982 booked test lines: +4.97%**. The plan is **515,740**, implying **+18.21%**. The gap is **57,758 tests**, or **13.24 percentage points** of growth.

Binning by export-file month instead of slot date gives 458,089 tests in 2026 (the 107 July-slotted Nova lines sit in the April–June export files), or +4.99%. That answers a different period question; it is not a tolerance for the slot-date calculation.

## 2. Decisions

| # | Table | What we found | Decision | Reason | Rows affected | Effect on the headline | Reversible? |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Bookings | Chennai and Jaipur moved to Nova in April 2026. | Include both systems. | Legacy alone omits those cities' April–June bookings. | All Nova files | Legacy alone: 399,212 tests in 2026, −8.50% company growth; Jaipur 45,149 → 20,486, −54.6%. | Yes |
| 2 | Bookings | Slot date and export month place 107 Nova lines in different periods. | Use slot date. | It defines the visit-period comparison we chose. | 107 Nova test lines slotted in July 2026 | Slot date +4.97%; export month +4.99%. | Yes |
| 3 | Legacy | The header line is repeated inside the export. | Remove embedded header rows. | They are text, not bookings. | 21 rows | Prevents text entering counts and numeric fields (they become blank when `test_price` is converted to numbers). | Yes |
| 4 | Bookings and plan | One city appears under two names. The plan file also uses "Bangalore". | Map Bangalore to Bengaluru in bookings and plan. | The city must match across years and against the plan. | 193,558 legacy rows; 1 plan row | City grouping corrected; company volume unchanged. | Yes |
| 5 | Nova | The original and the replacement of a rescheduled booking both appear. | Remove originals (`status_at_export == "rescheduled"`); keep replacements. | Count each scheduled visit once. This does not establish attendance. | 1,500 bookings; 3,542 rows; 3,541 test lines | 5.78% → 4.97%. | Yes |
| 6 | Nova | Native records hold a nested test list; the flattened file has one row per test. | Count test entries (flattened rows with a test code), not records. | A native record is a booking, not a test. | All Nova records | Prevents comparing Nova bookings with legacy test lines. | Yes |
| 7 | Legacy | Some priced lines have a blank `test_code`. | Keep in booked volume; leave the test type unknown. | A blank legacy code is still a booked line; it is not the same as an empty Nova test list. | 865 eligible lines (422 in 2025; 443 in 2026), all with a price | Retains them in each year's volume. Dropping them understates both years (435,880 → 457,539); the growth rate stays at 4.97%. | Yes |
| 8 | Nova | 21 bookings have an empty test list (blank `test_code` and `test_price`). | Keep as visits with zero tests. | Test count and booking count stay distinct. | 21 rows; 20 remain after decision 5 | No test lines added; adds 20 visits in 2026. | Yes |
| 9 | Legacy | Some bookings were created after their slot time. | Flag and inspect; never use `created_at` for the headline. | The analysis uses slot date throughout. We do not infer the reason. | 688 bookings with January–June slots (1,030 across all months) | None on the slot-date headline. | Yes |
| 10 | Bookings | Legacy dates are day-first (`%d/%m/%Y %H:%M`); Nova dates are ISO with a +05:30 suffix. | Parse with explicit formats; for Nova keep the first 16 characters. | Prevents day/month swaps and date-boundary shifts. Raw timestamps preserved. | All rows | 0 legacy slot dates failed to parse. Protects period assignment. | Yes |
| 11 | Bookings | Nova uses upper case (CLINIC, HOME; APP, PHONE, WALKIN); legacy lower case. | Lower-case `channel` and `source`. | Otherwise the same channel splits into two groups. | All Nova rows | No change to total tests; fixes segmentation. | Yes |
| 12 | Bookings | One visit spans several rows; one patient spans several visits. | Count distinct `booking_id` (visits) and `patient_id` (patients). | Rows are not visits or people. | All eligible rows | Visits 189,877 / 195,044; patients 94,582 / 98,120. | Yes |
| 13 | Patient master | We need one new-vs-returning rule for both systems. | New = `signup_date` on or after 1 January of the half; Nova's own `is_new` flag not used. | Same rule for both systems. | All joined patients (0 unmatched) | Drives segmentation. About a quarter of new patients have a first visit up to 27 days before their signup date, equally in both years; logged as a data-quality note. | Yes |
| 14 | Collections | Exact duplicate rows. | Drop exact duplicates before any visit-level rate. | Otherwise some visits count twice. | 2,894 rows | After removal, one row per booking. Protects no-show and coverage denominators. | Yes |
| 15 | Collections, results | A rescheduled Nova visit is recorded under its original booking ID. | Join on `ops_id` = `rescheduled_from` if present, else `booking_id`. | Otherwise the kept replacement loses its operational records. | Chennai and Jaipur April–June visits | Missing-collection share in Chennai/Jaipur H1 2026: 5.7% without the mapping, 2.9–3.0% with it (other cities 2.9–3.1%). | Yes |
| 16 | All `booking_id` join keys | Stray trailing spaces in booking IDs: legacy 12,559 rows, collections 24,461, results 48,102, invoices 20,801, campaigns 1,532. | Strip spaces and upper-case both sides before every `booking_id` join. | Formatting must not create false non-matches. Visit counts are unaffected: no booking appears under two spellings. | See counts | Unmatched collections 14,873 → 11,508; campaign-tagged visits 46,871 → 48,377. No conclusion changes. | Yes |
| 17 | Gateway payments | `notes_invoice_ref` has mixed case and trailing spaces. | Strip and upper-case before joining to `invoice_id`. | Formatting must not create false non-matches. Do not guess mistyped IDs. | 46,488 references | Affects H8/H13 only, not booked volume. | Yes |
| 18 | Growth calculations | Cities have different base volumes. | Company growth = total 2026 ÷ total 2025 − 1. | An unweighted average gives each city equal weight. | All company-level rates | 4.97%, not the 3.91% simple average of city growth rates. | Yes |

## 3. Joins

All keys were stripped and upper-cased on both sides (decision 16). Visits cover both halves (H1 2025 + H1 2026).

| # | Left table (rows) | Right table (rows) | Key | Type | Rows after | Keys matched more than once | Explanation of the change in count |
| --- | --- | --- | --- | --- | --- | --- | --- |
| J1 | Eligible visits (384,921) | Collections after dropping 2,894 exact duplicates (549,598) | `ops_id` → `booking_id` | LEFT | 384,921 | 0 | Row count unchanged. 11,508 visits (3.0%) have no collection record: 2.9–3.1% in every city and half. |
| J2 | Eligible visits (384,921) | Results summarised to first `result_ts` per booking (473,905, from 1,078,420 result rows) | `ops_id` → `booking_id` | LEFT | 384,921 | 0 | Summarised first so visits don't multiply. 66,169 visits have no result, mostly no-shows (which never reach the lab). |
| J3 | Eligible visits (384,921) | Patients (309,217; `patient_id` unique) | `patient_id` | LEFT | 384,921 | 0 | 0 unmatched. |
| J4 | Eligible visits (384,921) | Campaign tags (48,869; unique after cleaning) | `booking_id` | LEFT | 384,921 | 0 | 48,377 visits tagged, all home collection, all January–March 2026. 492 tags match no booking in either system. |
| J5 | H1 invoices by `invoice_date` (320,327 = 157,451 + 162,876) | Earliest captured gateway payment per invoice reference | `invoice_id` → cleaned `notes_invoice_ref` | LEFT | 320,327 | 0 | 244,071 invoices matched (76.2%): 76.7–77.2% by city in 2025, 74.9–75.6% in 2026. |

## 4. The decision trail: how the decisions led to the conclusion

| Step | Decision we made | Why | What the data showed | Led to |
| --- | --- | --- | --- | --- |
| 1 (H1) | Rebuild booked volume across both systems. | Establish the baseline before explaining it. | 436,302 → 457,982 (+4.97%); plan 515,740; gap 57,758. | Investigate the drivers under this definition. |
| 2 (H2) | Split into patients × visits per patient × tests per visit. | Each lever has a different owner and fix. | +3.74%, −0.98%, +2.19%; 1.0374 × 0.9902 × 1.0219 = 1.0497. | Patients is the short lever. |
| 3 (H10) | Examine test codes and packages. | An average can hide a category fall. | Tests per visit +2.2%; packages 54,112 → 72,913 (+34.7%); singles 382,190 → 385,069 (+0.8%); largest code fall T015, −2.9%. | Rejected as a cause. |
| 4 (H5) | Check visit frequency by city and month, and returns. | A falling average may hide concentrated churn. | Visits per patient −1.0%, no city/month pattern (Chennai +3.1%). 32.3–34.0% of H1 2025 patients returned in H1 2026; 46–48% return from one half to the next. | Rejected as a main cause; the return rates feed step 11. |
| 5 (H3/H4) | Locate the shortfall by city, then size each city's gap. | Prioritise by size and direction. | Patients fell only in Chennai (−5.0%) and Jaipur (−12.2%). Every city missed plan. | Two branches: 43.8% and 56.2% of the gap. |
| 6 (H7) | Split Chennai/Jaipur patients into new and returning. | Find who was lost. | New patients −26.6% and −34.3%; returning +9.2% and +2.9%. | Acquisition problem; examine timing. |
| 7 (H7) | Compare before/after April and patient-master signups. | Test the migration as the explanation. | Fall already present in January on legacy; signups per quarter fell from 2026 Q1 (Chennai 3,553 → 2,696; Jaipur 1,887 → 1,350). | Migration rejected; only the reschedule duplicates were an artefact. |
| 8 (H7) | Examine clinic, source and channel inside the two cities. | Narrow the next question. | Declines in every clinic, source and month. | Cause outside the data (competitor, referral, pricing): unresolved. |
| 9 (H9/H14) | Check walk-ins and campaign exposure. | Test narrower explanations. | Walk-ins +5.3% vs app +5.1%, phone +4.5%. Campaign tags 24.4–25.3% of H1 2026 visits in every city; new-patient share 28.8% vs 28.9%. | Neither explains two cities; campaign value left to Brief E. |
| 10 (H6/H11/H12/H8/H13) | Check no-shows, timing, completion and payments. | Rule out internal failures. | No-shows 14.59% → 14.01%, down in every city; collection delay and lab turnaround flat; no-result share flat; payment time 2.1 days both halves; failure rates flat. | No broad deterioration in the measured processes. |
| 11 (Four growing cities) | Test why they missed despite growing. | 56.2% of the gap sits here. | Plan is a flat uplift on 2025 volume. Returning tests needed +15.0–20.3% to hit plan; actual +0.1–3.0%. ~90% of returning patients were active in 2025; that base was flat (H2 2025 slightly below H1 2025); return rate steady at 46–48%. | A planning assumption, not an operating failure. Limit: no pre-2025 data. |
| 12 | Reconcile city gaps; label the new/returning allocation. | Keep totals traceable. | 25,296 tests in Chennai/Jaipur; 32,462 elsewhere. Only the split inside Chennai/Jaipur (19,210 new / 6,086 returning) depends on the allocation choice. | Board sentence. |
| 13 | Examine the January 2026 step change in new patients. | A sharp two-way shift needs explaining. | Four cities about +17% a month, Chennai about −26%, Jaipur about −34%, from January, held all six months. Campaign, channel, source, duplicate registrations, wrong-city records and signup recording all ruled out. | Unresolved; question for the COO's office. |

**Board sentence:** Under our booked-test definition, volume grew 4.97% against a plan implying 18.21%, leaving 57,758 tests short. Chennai and Jaipur account for 43.8% of the gap: they lost a quarter to a third of their new patients from January, before the April system change. The other four cities account for 56.2%: the plan expected their returning patients to grow 18–20%, while the base those patients come from was flat through 2025. We propose a 60-day plan: two weeks to find why Chennai and Jaipur stopped winning new patients, then a controlled first-visit package pilot in half their clinics, chronic-care reminders to lift repeat visits in the other four cities, and a re-based plan that sets new and returning patients separately.
