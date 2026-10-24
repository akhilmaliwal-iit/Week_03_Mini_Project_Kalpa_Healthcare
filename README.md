# Build 1 · Brief A · Growth · Group <N>

Dr Priya Menon, COO of Kalpa Health, asked which part of the business is short, by how much and where. Test volume grew about 5 percent in the first half against a plan of 18, and the board will not accept a soft market as the explanation. The decision it drives is where to act in the second half: which cities, whether the problem is winning patients, getting them back or the plan itself, and what to do about costs that were sized for 18 percent. We rebuilt test volume from both booking systems, counting each visit once, and split the growth into patients × visits per patient × tests per visit. Patients are the short branch. The gap has two separate causes: a real fall in new patients in Chennai and Jaipur from January, before the April system switch, and a plan that expected returning patients in the other four cities to grow 18 to 20 percent when only about a third of patients come back in a half. Why Chennai and Jaipur lost new patients is not answerable from this data pack.

## The claim

Booked test volume in January to June 2026 was 458,089 against 436,302 in January to June 2025 (+4.99 percent, each visit counted once) when the plan required +18.2 percent, a gap of 57,651 tests, and patients (+3.8 percent against about 18 percent needed) are the branch that is short.

## Members and parts

| Member           | Part of the work owned               | Notebooks | Approach-log rows |
| ---------------- | ------------------------------------ | --------- | ----------------- |
| Akhil Maliwal    | Data Merging, Hypothesis 11,14       |           |                   |
| Mohit Pasupuleti | Hypothesis, Analysis, Hypothesis 1-5 |           |                   |
| Nitya            | Data Cleaning, Hypothesis 6-11       |           |                   |

## How to run

1. Open this repository in a GitHub Codespace.
2. Run the notebooks in `notebooks/` in numerical order, each from a fresh kernel (Kernel, Restart and Run All).
3. The headline number appears in the last cell of `06_answer_cold_run.ipynb`. Expected value: +4.99 percent growth in booked tests, 436,302 (January to June 2025) to 458,089 (January to June 2026), each visit counted once.
4. Runtime on a fresh Codespace: <about N minutes>.

## Links

| What                 | Where                                    |
| -------------------- | ---------------------------------------- |
| Video                | <link, viewable by anyone with the link> |
| Slides               | `slides/`                                |
| Task board           | <link to Issues or Projects>             |
| Data pack provenance | `data/raw/README.md`                     |

## The logs

`docs/approach-log.md` · `docs/decisions-log.md` · `docs/challenges-log.md` · `docs/writeups.md` · `docs/ai-log.md`

## Working agreement

Nobody pushes to `main`. Every change arrives by pull request approved by a member who did not write it. Merges are merge commits so the history stays readable. Notebook outputs are cleared before commit. The build freezes the evening before presentations.

Data Files: https://drive.google.com/drive/folders/1aGWmFOt6_E_sDW50oj0vjSGdxpNGZURG?usp=sharing
