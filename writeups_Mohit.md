# Individual write-ups · Brief A · Group <N>

## Group summary

Dr Menon asked which part of Kalpa Health's business is short, by how much, and where. Test volume grew 4.97% in January to June 2026 (436,302 to 457,982 tests, each visit counted once) against a plan of +18.2%, a gap of 57,758 tests. We rebuilt the number from both booking systems and split it into patients × visits per patient × tests per visit. Patients are the short branch (+3.7% against about 18% needed). Visits per patient (−1.0%) and tests per visit (+2.2%) do not explain the gap, and we tested and rejected no-shows, walk-ins, test groups, the free home-collection offer, lab and collection capacity, payments and the April system switch as causes.

The gap has two separate causes. Chennai and Jaipur lost new patients (−26.6% and −34.3%) from January 2026, in every clinic, source and month and before the April system switch. That is 25,296 tests, or 43.8% of the gap, and the data cannot say why. In the other four cities new patients met plan, but the plan also expected returning patients to grow 18–20%, and only about a third of patients come back in a half. That is 32,462 tests, or 56.2% of the gap, and it is a plan-setting problem as much as an execution one. Dr Menon should not cut targets or headcount in Chennai and Jaipur until the city heads say what changed in January, should rebuild the second-half plan city by city from patients and visits, and should fix the dashboard so each visit counts once.

---

Each section below is written by one member in their own words, 600 to 900 words, and committed by its author. Read each other's sections; do not edit them.

## Mohit

**1. In your own words, what was Dr Menon asking for, and what did she actually need?**

She asked which part of the business is short, by how much, and where. On paper that is a growth question: tests grew 4.97% against a plan of 18.2%. What she actually needed was something she can act on before the board meets. Is the gap a counting problem, a soft market, or something inside Kalpa that she can fix? And does she act on all six cities or only a few? So the answer had to be one sentence she can say out loud, with numbers finance can trace back to rows.

**2. Which part of the work was yours, and what did you produce that the group now depends on?**

My part was the structure of the investigation. I took the 14 hypotheses and put them in an order that goes from "is the number real" to "which branch is short" to "where" to "why", and marked which ones end as dead ends. The investigation map and the rejected-hypotheses log came out of that, along with the numbered notebook questions and their check values. The team uses these to see whether their own numbers are on track. [Add the parts your team members own, with their notebooks.]

**3. Which approaches did you consider for your part, which did you choose, and why not the others?**

For the table to count tests from, I looked at lab results (+5.75%), visits (+2.74%), raw booked lines (+5.78%) and cleaned booked lines (+4.97%). I chose cleaned booked lines because the plan counts tests, and plan divided by last year's base is a constant for each city only on booked lines. For a rescheduled visit I kept the replacement and dropped the original, because the replacement carries the slot date of the visit that actually happened. For the tree I used three branches (patients, visits per patient, tests per visit) instead of two, because the middle branch separates "people came less often" from "each visit carried fewer tests". I split by city before channel because the plan is owned by city, and the channel shift happened everywhere.

**4. Which number do you trust most, what is its denominator, and what would make you stop trusting it?**

The one I trust most is 4.97%: 457,982 tests in January to June 2026 against 436,302 in January to June 2025, each visit counted once, by slot date. The denominator is last year's tests. I would stop trusting it if the reschedule rule were wrong. A figure near 5.78% means the originals were left in. A figure near −8.5% means the Nova files were left out. If you bin by export-file month you get 4.99%, because 107 Nova lines slotted in July move across the boundary. Both are right as long as the basis is written down.

**5. What broke, what did you try, and what did you change? Point at the challenges-log entry.**

My first count came out at 5.78%, not about 5. The cause was that Nova keeps the original of every rescheduled visit as well as its replacement. Dropping the originals (3,541 test lines on 1,500 bookings) moved growth to 4.97%. It was the only cleaning rule that changed the headline. The same issue came back in the joins: collections and results are filed under the original id, so Chennai and Jaipur showed 6.2% of visits with no collection record. After mapping each replacement back to its `rescheduled_from` id, it fell to 3.4–3.5%, in line with the other cities. See challenges-log entries 4 and 8.

**6. What do you understand about a diagnostics business now that you did not before?**

Volume is people times how often they come times how much each visit carries, and each part needs a different fix. Only about a third of last year's patients come back in a half (32.3–34.0% in every city), so a plan that expects returning patients to grow 18–20% is hard to hit. Packages grew 34.7% while single tests grew 0.8%, so tests per visit can rise without any new patients. The free home-collection offer moved patients from clinic to home but did not explain why two cities fell. And a dropped repeat test may be a missed diagnosis, not only a missed sale.

**7. Where did the team work well, where did it not, and what would you change for Build 2?**

What worked was agreeing one definition of test volume before any code, and giving the team check values to match. What did not work at first was the time basis. Different documents showed 4.97% and 4.99% until we fixed slot date as the basis and wrote it down. For Build 2 I would write the definition, the time basis and the identity rule on day one, and log them before the first notebook.

**8. Which idea from Weeks 1 and 2 do you understand better after using it on this data, and what was fuzzy before?**

The tree. I was fuzzy on whether it multiplies totals. It has to be ratios (visits per patient, tests per visit) so the units cancel down to tests, and then the branches multiply back to the total: 1.0374 × 0.9902 × 1.0219 = 1.0497. I also understand the fair comparison better. Ranking cities by growth rate puts Jaipur last, but ranking by gap puts Bengaluru second (12,424 tests) even though it grew 7.6%. A rate and a share of the gap tell different stories, and you need both.

**9. Which viva question would you least like to be asked, and what is your answer to it?**

"Why did Chennai and Jaipur lose new patients?" I don't know, and the data cannot say. What I can say is that the loss is real, that it started in January before the April system switch, and that it shows in every clinic, source and month. Three tables agree: bookings, the lab and the patient master. The pack has no competitor, pricing or referral data. The next step is to ask the city heads, then test one fix in a few clinics against similar clinics that do not get it. "Not yet, and here is what would tell us" is a complete answer when it is true.

**10. If you had two more days, what would you do first, and why that?**

First I would pull 2024 bookings to see whether the return rate of about a third has changed. That decides whether the other four cities have a plan problem or a retention problem, and they hold 56.2% of the gap. Second, I would ask the Chennai and Jaipur city heads about competitors, pricing and referral doctors, since that is the only way to find out what happened from January. What would help even more is having some data on Metropolis or LalPath's so that I can compare KPI's and identify where Kalpa's test volume fell short
