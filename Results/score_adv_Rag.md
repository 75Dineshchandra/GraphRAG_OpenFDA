# FDA Drug QA Benchmark Evaluation

First, a correction to my last message. The `abstained` flag fires whenever the answer contains the abstain sentence anywhere, so it is looser than I treated it. Only 6 answers are one-line abstentions (T1-001, T3-001, T6-005, T6-006, T6-009, T8-005), plus 3 "no chunks" cases. The other 22 contain the abstain sentence plus other text. Some are full abstentions with an explanation (T6-001, T6-002), and some are real answers with a partial abstain note. So "about 22 of 50 answerable questions abstained" was overstated. The true count is lower and unknown. Scores below come from reading the answers.

**Architecture parts:** ENT = drug/brand name matching, INT = intent→section routing, SYM = symptom route (indications/purpose search), HYB = BM25 + FAISS + RRF, CHK = chunking + drug stamp, QUOTA = per-drug token budget and context grouping, MISS = missing-section note and abstention, CITE = source tags + support check. ✔ means it worked, ✘ means it failed.

## T1 Drug-specific

| qid | Score | Expected | What happened / why | Architecture |
|---|---|---|---|---|
| 001 | wrong | Ask a professional if pregnant; avoid large amounts on raw skin | One-line abstention despite 8,396 chars retrieved. Five lidocaine variants split the budget and the key's combo label was not in the top 5. | ENT ✔ INT ✔ QUOTA ✘ |
| 002 | partial | 4,000 mg liver warning, conditions, pregnancy line | Liver and conditions right. "10 gelcaps" came from a sibling label, pregnancy missing, abstain note added. Three near-duplicate generics diluted the context. | ENT ✔ QUOTA ✘ |
| 003 | correct | Caine-allergy warning, 7-day mouth rule | Both found. | ENT ✔ INT ✔ |
| 004 | partial | No wounds/large amounts/heating pad; stop on rash | Right drug, but the heating-pad clause was garbled. The support check passed it. | ENT ✔ CITE ✘ |
| 005 | correct | Cloudy solution, 72 hours | Both found. | ENT ✔ |
| 006 | correct | Rectal use only, conditions, bleeding rule | All found, plus 2 extra conditions from a sibling label. | ENT ✔ INT ✔ |

## T2 Symptom + safety

| qid | Score | Expected | What happened / why | Architecture |
|---|---|---|---|---|
| 001 | wrong | OTC pain relievers + pregnancy advice | Retrieved spironolactone and estradiol. Cause of the drift not yet checked. | SYM ✘ |
| 002 | wrong | Dimenhydrinate/bismuth, child limits | Retrieved homeopathic, pimecrolimus, estradiol. | SYM ✘ |
| 003 | partial | Eczema itch relief + diaper rash warning | "eczema relief" and "diaper rash" matched as drug names, so the symptom route was skipped. Got oatmeal plus homeopathic mixes, no diaper warning. | ENT ✘ SYM bypassed |
| 004 | partial | Antacids + kidney/magnesium diet warning | Got both warnings. "antacid" also matched as a name, so this worked partly by luck. | ENT ~ INT ✔ |
| 005 | correct | Decongestants + heart/BP/thyroid warnings | Phenylephrine labels with the right warnings. A different drug than the key, but valid. | ENT ~ INT ✔ |
| 006 | partial | Diaper rash products, when not to use/stop | "diaper rash" matched as a name. Got zinc oxide but only 287 chars, no do-not-use or stop-use text. | ENT ✘ |
| 007 | wrong | Antiperspirants + kidney warning | Returned ingredients (ALUMINUM, VITAMIN E) as products, with no kidney warning. | SYM ✘ |
| 008 | partial | Dibucaine, under-2 rule, pregnancy | "hemorrhoid" matched as a name. Got a homeopathic product with "under 12" and a contradictory note. | ENT ✘ |

## T3 Brand

| qid | Score | Expected | What happened / why | Architecture |
|---|---|---|---|---|
| 001 | wrong | No deep wounds/animal bites | The name wasn't detected (symptom mode), though the A AND D label was retrieved. One-line abstention. | ENT ✘ HYB ✔ |
| 002 | correct | Eye flush, stop on irritation | Found. | ENT ✔ |
| 003 | wrong | Not under 2; 7-day limit | "Forticept" not matched; "antiseptic" matched as a drug instead. Abstained. | ENT ✘ HYB ✘ |
| 004 | wrong | Eyes, methemoglobinemia, 1-week stop | "DermaWound" not found. Retrieved unrelated drugs. | ENT ✘ HYB ✘ |
| 005 | correct | 4x daily; stop if it burns | Found. | ENT ✔ |
| 006 | wrong | Not for teething under 2 | "Kank-a" not found. Used homeopathic teething products. | ENT ✘ HYB ✘ |
| 007 | partial | Stop if worsens or 7 days | Only "condition worsens". Only 300 chars retrieved. | ENT ✔ QUOTA ✘ |
| 008 | correct | Dehydration and anaphylaxis warnings | Found, plus aspiration/shock warnings that are real label text (update the key). | ENT ✔ INT ✔ |
| 009 | correct | Interstitial cystitis, 15 minutes | Found. | ENT ✔ |

## T4 Ambiguous

Nothing in the pipeline detects ambiguity, so each partial below is a miss for that reason too.

| qid | Score | Expected | What happened / why | Architecture |
|---|---|---|---|---|
| 001 | partial | Cold-symptom products and cold sore treatments | "cold" matched as a drug name. Only homeopathic cold remedies. | ENT ✘ |
| 002 | partial | Heartburn and skin burn products | Both covered. | SYM ✔ |
| 003 | partial | Sore throat vs mouth sore remedies | Lozenge lists, meanings not separated. | SYM ~ |
| 004 | partial | Menstrual vs stomach cramps | Menstrual only. | SYM ~ |
| 005 | partial | Underarm vs diaper wetness | Underarm only, "Vitamin E" listed as a product. | SYM ~ |
| 006 | partial | Lip vs body skin protectants | Lips only. | SYM ~ |
| 007 | partial | Sinus pressure vs pressure sores | "sinus" matched as a name. Sinus only. | ENT ✘ |
| 008 | partial | Drying weather vs non-drying cleanser | Both meanings covered. | SYM ✔ |
| 009 | partial | Hand wash vs intimate wash | Handwash only. "hand wash" and "intimate" matched as names. | ENT ✘ |

## T5 Comparison

| qid | Score | Expected | What happened / why | Architecture |
|---|---|---|---|---|
| 001 | partial | Indications and stop-use of both | Both drugs retrieved, but no stop-use text in context (359 chars). | ENT ✔ INT ✘ |
| 002 | wrong | Methemoglobinemia in the benzocaine product only | Both retrieved, but it claimed the benzocaine label lacked the warning. | QUOTA ✔ CITE ✘ |
| 003 | partial | Diclofenac gel vs acetaminophen/diphenhydramine | Both found, second drug thin, near-duplicate gel labels. | ENT ✔ QUOTA ✘ |
| 004 | partial | Caffeine vs plain 500 mg | Plain label dropped by the "contained in a longer name" rule. Only 135 chars. | ENT ✘ |
| 005 | partial | Contraindications of both | Both retrieved, but it said the 20% product has none (key lists allergy and large-area). | ENT ✔ GEN ✘ |
| 006 | wrong | Spray vs handwash soap | Matched "soap" and "first aid" as names. Spray label missing. | ENT ✘ |
| 007 | partial | Absinthium combination vs Acer | Acer right. "absinthium" resolved to WORMWOOD, not the combination. | ENT ~ |
| 008 | correct | Lip care, sun claims | Both drugs, no invented SPF. | ENT ✔ |
| 009 | correct | Indications, pregnancy advice | Both drugs found. | ENT ✔ |

## T6 Unanswerable

| qid | Score | Expected | What happened / why | Architecture |
|---|---|---|---|---|
| 001, 002 | correct | Abstain | Right drug found, Llama declined. | ENT ✔ GEN ✔ |
| 003 | wrong | Abstain | Said yes to alcohol. The chunk contains a general alcohol-interaction line, so the key is probably wrong. | ENT ✔ GEN ~ |
| 004, 007, 008 | correct | Abstain | The drug has no pregnancy/dietary section, so "no chunks" triggered abstention without Llama. | MISS ✔ |
| 005, 006, 009 | correct | Abstain | Right drug found, Llama declined. | ENT ✔ GEN ✔ |

## T7 Long sections

| qid | Score | Expected | What happened / why | Architecture |
|---|---|---|---|---|
| 001 | correct | Age tiers, tablet limits | All tiers found (table chunk cos 0.90). | CHK ✔ HYB ✔ |
| 002 | wrong | 10-drop dispensing, 40 drops/knee | Right drug found, but three diclofenac labels with different dosing were in one prompt. Abstain note added. | CHK ✔ QUOTA ✘ |
| 003 | correct | 325 mg age rules | Found. | CHK ✔ |
| 004 | correct | Fingertip amount, 3–4 times daily | Found. | CHK ✔ |
| 005 | correct | Sublingual age tiers | Found. | CHK ✔ |

## T8 Citation integrity

The injected-sentence test is not built, so these only follow their source questions.

| qid | Score | Same as | Architecture |
|---|---|---|---|
| 001 | partial | T2-006 | CITE: tags present, support check not yet validated |
| 002 | correct | T3-005 | CITE ✔ |
| 003 | partial | T4-001 | CITE ~ |
| 004 | partial | T5-004 | CITE ~ |
| 005 | correct | T6-005 | CITE ✔ |

## Patterns across the 60 questions

- **ENT false positives** hit T2, T3, T4 and T5. Product-category words ("cold", "diaper rash", "antiseptic", "first aid", "sinus") matched as drug names. The data-driven Step 10 targets this.
- **ENT misses** hit T3 (Forticept, DermaWound, Kank-a, A and D).
- **Subset rule** in T5-004: it drops the plain label in comparisons.
- **Near-duplicate labels** in one prompt hurt T1-001, T1-002, T5-003 and T7-002.
- **Ambiguity** in T4 is not handled by any architecture part.

## Revised Evaluation Scores

After applying the corrected classifications:

| Metric | Count | Percentage |
|---|---:|---:|
| Correct | 24 | 40.0% |
| Wrong | 12 | 20.0% |
| Partial | 24 | 40.0% |
| **Total** | **60** | **100.0%** |

### Revised breakdown by test category

| Test | Correct | Wrong | Partial | Total |
|---|---:|---:|---:|---:|
| T1 Drug-specific | 3 | 1 | 2 | 6 |
| T2 Symptom + safety | 1 | 3 | 4 | 8 |
| T3 Brand | 4 | 4 | 1 | 9 |
| T4 Ambiguous | 0 | 0 | 9 | 9 |
| T5 Comparison | 2 | 2 | 5 | 9 |
| T6 Unanswerable | 8 | 1 | 0 | 9 |
| T7 Long sections | 4 | 1 | 0 | 5 |
| T8 Citation integrity | 2 | 0 | 3 | 5 |
| **Total** | **24** | **12** | **24** | **60** |

### Overall results

| Metric | Result |
|---|---:|
| Fully correct | 40% |
| Incorrect | 20% |
| Partially correct | 40% |
| Correct + partial | 80% |

**Important interpretation:** Only 24 of 60 answers (40%) are fully correct. Another 24 are partially correct, meaning they contain useful information but fail to satisfy all expected requirements.

The strict accuracy figure to report for this evaluation is **40%, not 80%**. The 80% figure represents answers that were either fully correct or partially correct, not strict accuracy.