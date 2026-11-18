# nepali-asr-evaluation

Do WER and CER measure Nepali speech recognition fairly? This project compares them with the judgment of a Nepali speaker.

**Status:** pilot study, results are preliminary (one rater).

## The problem

Speech recognition (ASR) is usually scored with WER (word error rate) and CER (character error rate). WER counts a whole word as wrong, even when only one small sign is different. In Nepali, many small differences do not change the meaning for a reader. For example, a space inside a word, a short or long vowel, or a number written in other digits. So a score may punish text that a Nepali reader accepts.

## What I did

1. Ran the Meta MMS model (`facebook/mms-1b-all`, Nepali adapter) on 500 audio files from OpenSLR 54 (200 files first, then 300 new files from new speakers).
2. Computed WER and CER for every file.
3. Built a rule-based error sorter. It sorts each difference into types: spacing only, punctuation only, digit style only, English letters mixed in, junk, wrong number, a few letters different, or many letters different.
4. Rated the rows by hand. The rule: "If I read the model text, do I understand the same meaning as the correct text, without effort?" (1 = yes, 0 = no, ? = not sure).
5. Built a "folded CER". Before comparing, it changes harmless differences into the same sign (for example short and long vowels, श ष स, spaces, digit style). Each rule was supported by at least 2 accepted rows.
6. Froze the rules, then tested them on 300 new files from new speakers.

## Results (preliminary)

On the 300 new files, the model had WER 59.8% and CER 19.8%. On 156 rows from the test set, I rated 51 as acceptable, 80 as not acceptable, and 25 as not sure.

How much each score agrees with my ratings (Spearman correlation, closer to -1 is better). The range depends on how the 25 unsure rows are treated (left out, or counted as 0, or counted as 1):

| Score | Agreement with my ratings |
|---|---|
| WER | -0.07 to -0.17 |
| CER | -0.42 to -0.52 |
| Folded CER | -0.68 to -0.78 |

Other findings:
- In rows I accepted, WER still counted more than half of the words as wrong (average 0.55).
- The folding made 33 of the 51 accepted rows exact, and 0 of the 80 rejected rows.
- The model often mixes English letters into Nepali text: 59 of the 300 test rows (20%).

## Limits (please read)

- Only one rater. Agreement between raters is not measured yet.
- One model (MMS), one dataset (OpenSLR 54, read speech, first zip file only).
- The rating sheet is not a random sample. The sorter chose the rows, so it has many borderline cases.
- The unsure rows are left out of the first numbers. The ranges above show how much this matters.
- Early practice ratings (the first 30 rows, and round 2) included answers suggested by an AI assistant. I use them only as practice. **(check)** The test-set ratings were made by me alone, without row answers from the assistant.
- I did not find a Nepali-specific study like this in my searches, but I did not do a systematic literature review.

## Notebooks

| Notebook | What it does |
|---|---|
| 01 | Load 20 samples from OpenSLR 54 |
| 02 | Run the MMS model on them |
| 03 | WER, CER and first labels |
| 04 | Simple cleaning rules |
| 05 | Run the model on 200 files, make a rating sheet |
| 06, 07 | First ratings, and rating again with a clearer rule |
| 08, 09 | Error sorter (versions 1 and 2) |
| 10 | Rate round 2 |
| 11 | Folded CER |
| 12 | 300 new test files, frozen rules |
| 13 | Rate the test set and compare the scores |

All notebooks run in Google Colab (a GPU is needed for 02, 05 and 12).

## Next steps

- Re-rate the first 60 test rows alone after a few days, to measure my own consistency.
- Get a second rater (Cohen's kappa).
- Add new rules found in the test set (for example ब/व and ण/न) as "version 2", and test them on a third set of new rows.

## Data and credit

Audio and text: OpenSLR 54 (SLR54), license CC BY-SA 4.0. Citation: Kjartansson et al., "Crowd-Sourced Speech Corpora for Javanese, Sundanese, Sinhala, Nepali, and Bangladeshi Bengali", SLTU 2018. The audio is not stored in this repo.
