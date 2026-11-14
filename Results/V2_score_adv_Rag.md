# **Question-Wise Evaluation Report: Drug QA Benchmark**

## **1. Overall Evaluation Results**

The evaluation covers **60 questions across eight tasks (T1–T8)**. T7 is retained as part of the analysis.

| Result            | Count  | Percentage |
| ----------------- | ------ | ---------- |
| Correct           | 17     | 28.33%     |
| Partially correct | 25     | 41.67%     |
| Wrong             | 18     | 30.00%     |
| **Total**         | **60** | **100%**   |

## **2. Question-Wise Evaluation**

### **T1: Drug-specific**

**Score: 0 Correct | 6 Partially Correct | 0 Wrong**

| Q   | Result            | Explanation                                                                                                                                                |
| --- | ----------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 001 | Partially correct | Has "large quantities" and "raw surfaces". Missing "ask a professional if pregnant". Extras ("under 3 years", "allergic reaction") come from other labels. |
| 002 | Partially correct | Has the 4,000 mg liver warning and liver disease. Missing diabetes, heart, BP, thyroid and prostate. Adds a wrong "8 tablets" limit.                       |
| 003 | Partially correct | Has the 7-day mouth limit. Missing the "caine" allergy warning. Wrongly says menthol lozenges have no warnings.                                            |
| 004 | Partially correct | Has wounds, large quantities, heating pad and stop on rash. Missing the pregnancy line.                                                                    |
| 005 | Partially correct | Has "cloudy" and "72 hours". Missing pregnancy. It also wrongly calls eye pain a solution "appearance".                                                    |
| 006 | Partially correct | Only "rectal use only". The conditions and the bleeding warning are missing.                                                                               |

### **T2: Symptom + Safety**

**Score: 0 Correct | 4 Partially Correct | 4 Wrong**

| Q   | Result            | Explanation                                                                                                                                                              |
| --- | ----------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| 001 | Wrong             | Retrieved homeopathic mixes and codeine products. No OTC pain reliever and no valid pregnancy advice.                                                                    |
| 002 | Wrong             | Lists homeopathics and creams. Dimenhydrinate, bismuth and the child rules are missing.                                                                                  |
| 003 | Wrong             | Mentions hydrocortisone for eczema, but misses most expected indications and the diaper-rash warning. Also incorrectly claims zinc oxide is contraindicated in children. |
| 004 | Partially correct | Antacids listed. The kidney and magnesium warning is missing.                                                                                                            |
| 005 | Partially correct | Decongestants and the heart, BP and thyroid warning are present, but the answer is classified as partial.                                                                |
| 006 | Partially correct | Zinc oxide indications only. No stop-use or do-not-use text.                                                                                                             |
| 007 | Partially correct | Valid antiperspirants plus the kidney and skin warnings, but the answer is classified as partial.                                                                        |
| 008 | Wrong             | Answers with vitamin drops and homeopathics, not hemorrhoid products.                                                                                                    |

### **T3: Brand**

**Score: 2 Correct | 3 Partially Correct | 4 Wrong**

| Q   | Result            | Explanation                                                                                      |
| --- | ----------------- | ------------------------------------------------------------------------------------------------ |
| 001 | Correct           | "Don't use on animal bites or deep wounds" is right (taken from sibling A/D labels).             |
| 002 | Correct           | Flush eyes and stop on irritation are both present.                                              |
| 003 | Wrong             | Forticept was not found. It says "okay for a 1-year-old" using unrelated drugs, which is unsafe. |
| 004 | Wrong             | Classified as wrong in the revised evaluation.                                                   |
| 005 | Partially correct | Has "stop if it burns". Missing "up to 4 times daily".                                           |
| 006 | Wrong             | It abstained although the correct text ("under 2, do not use") was in context.                   |
| 007 | Wrong             | It abstained although the stop-use text was in context.                                          |
| 008 | Partially correct | Has dehydration (aspiration is real label text). Missing anaphylaxis.                            |
| 009 | Partially correct | Has "15 minutes". Missing interstitial cystitis.                                                 |

### **T4: Ambiguous**

**Score: 4 Correct | 4 Partially Correct | 1 Wrong**

| Q   | Result            | Explanation                                                                                                   |
| --- | ----------------- | ------------------------------------------------------------------------------------------------------------- |
| 001 | Partially correct | Only homeopathic cold remedies. No cold-sore product.                                                         |
| 002 | Partially correct | Heartburn is covered. Wrong extras ("omeprazole or aspirin for a burn", a cold-sore product for a skin burn). |
| 003 | Partially correct | Sore-throat remedies only. The mouth-sore side is missing.                                                    |
| 004 | Correct           | Both menstrual cramps (acetaminophen) and stomach cramps are covered.                                         |
| 005 | Correct           | Underarm antiperspirants plus diaper-wetness zinc oxide products.                                             |
| 006 | Correct           | Lip products and body-skin protectants (dimethicone 1%).                                                      |
| 007 | Correct           | Sinus (pseudoephedrine) and pressure sores (benzethonium/allantoin).                                          |
| 008 | Partially correct | Only "drying weather". The non-drying cleanser is missing.                                                    |
| 009 | Wrong             | Says benzalkonium is an "intimate wash" and homeopathics are hand washes, both unsupported.                   |

### **T5: Comparison**

**Score: 0 Correct | 5 Partially Correct | 4 Wrong**

| Q   | Result            | Explanation                                                                                                                         |
| --- | ----------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
| 001 | Wrong             | Both drugs were retrieved, but it abstained anyway.                                                                                 |
| 002 | Wrong             | Wrong drugs retrieved (hand sanitizers), so it abstained.                                                                           |
| 003 | Partially correct | Diclofenac gel is covered. The second drug is missing.                                                                              |
| 004 | Partially correct | Both labels retrieved. Wrongly says the caffeine product is for sleeplessness.                                                      |
| 005 | Partially correct | One correct allergy line, then vague "different contraindications".                                                                 |
| 006 | Wrong             | Says the spray is "for hands only", which is wrong. The spray label was never retrieved.                                            |
| 007 | Wrong             | "Absinthium" resolved to wormwood. It also wrongly says Acer has no pregnancy warning.                                              |
| 008 | Partially correct | Camphor has no sun claim and octinoxate prevents sunburn, both right. The "decreases skin cancer risk" claim goes beyond the label. |
| 009 | Partially correct | Abelmoschus is right. Wrongly says Acetaldehyde has no indications.                                                                 |

### **T6: Unanswerable**

**Score: 8 Correct | 0 Partially Correct | 1 Wrong**

| Q   | Result  | Explanation                                                                                |
| --- | ------- | ------------------------------------------------------------------------------------------ |
| 001 | Correct | Found the drug and abstained.                                                              |
| 002 | Correct | Found the drug and abstained.                                                              |
| 003 | Correct | Abstained, matching the key. An older run found an alcohol line, so check the key.         |
| 004 | Correct | Abstained.                                                                                 |
| 005 | Wrong   | Says "remove lenses", but that text is from the tetrahydrozoline label, not difluprednate. |
| 006 | Correct | Abstained.                                                                                 |
| 007 | Correct | Abstained.                                                                                 |
| 008 | Correct | Abstained.                                                                                 |
| 009 | Correct | Abstained.                                                                                 |

### **T7: Additional Analysis**

**Score: 3 Correct | 0 Partially Correct | 2 Wrong**

| Q   | Result  | Explanation                                                      |
| --- | ------- | ---------------------------------------------------------------- |
| 001 | Correct | All three age tiers and the limits are right.                    |
| 002 | Wrong   | Gives generic diclofenac gel instructions, not the 10-drop rule. |
| 003 | Wrong   | Mixes several labels and gives wrong tablet limits.              |
| 004 | Correct | Fingertip amount and 3–4 times daily are both present.           |
| 005 | Correct | All three tiers are right.                                       |

### **T8: Citation Integrity**

**Score: 0 Correct | 3 Partially Correct | 2 Wrong**

| Q   | Result            | Explanation                                                                                                                                              |
| --- | ----------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| 001 | Partially correct | Same as T2-006. No stop-use or do-not-use text.                                                                                                          |
| 002 | Wrong             | Xiclofen was not found. It answers about burns in general.                                                                                               |
| 003 | Partially correct | Same as T4-001.                                                                                                                                          |
| 004 | Partially correct | Same as T5-004.                                                                                                                                          |
| 005 | Wrong             | Same error as T6-005. The key's "remove lenses" sentence is not in the D1737 text. It looks like the injected test sentence, and the system repeated it. |

## **3. Final Task-Wise Score Summary**

| Task                    | Correct | Partially correct | Wrong  | Total  |
| ----------------------- | ------- | ----------------- | ------ | ------ |
| T1: Drug-specific       | 0       | 6                 | 0      | 6      |
| T2: Symptom + Safety    | 0       | 4                 | 4      | 8      |
| T3: Brand               | 2       | 3                 | 4      | 9      |
| T4: Ambiguous           | 4       | 4                 | 1      | 9      |
| T5: Comparison          | 0       | 5                 | 4      | 9      |
| T6: Unanswerable        | 8       | 0                 | 1      | 9      |
| T7: Additional Analysis | 3       | 0                 | 2      | 5      |
| T8: Citation Integrity  | 0       | 3                 | 2      | 5      |
| **Total**               | **17**  | **25**            | **18** | **60** |

## **4. Evaluation Observations**

The results indicate the following:

1\. **Partial correctness is the largest category:** 25 of 60 answers (41.67%) contain some relevant information but fail to provide all required details or introduce unsupported information.

2\. **Correctness is limited:** Only 17 answers (28.33%) contain all required information without incorrect additions.

3\. **Incorrect retrieval and answer generation:** Several wrong answers involve retrieving unrelated drug labels, mixing information from different products, or generating unsupported indications and dosage instructions.

4\. **Incomplete safety information:** Some answers identify the correct drug or indication but omit important warnings, contraindications, age restrictions, or stop-use instructions.

5\. **Abstention behaviour:** T6 demonstrates generally appropriate abstention, with one instance of information incorrectly transferred from another drug label.

6\. **Citation integrity:** T8 identifies cases where answers are unsupported by the intended source or repeat information that appears to have been injected into the evaluation context.

7\. **T7 remains included:** Its five questions are retained in the overall benchmark analysis, contributing three correct and two wrong answers.

**Final result: 17 correct, 25 partially correct, and 18 wrong out of 60 questions.**