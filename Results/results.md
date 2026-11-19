# Results: Base vs V1 vs V2 vs V3

Scores use three labels: correct, partially correct and wrong.
Sources: `base_results` (Base), `adv_score` (V1), `V2_score_adv_Rag.md` (V2), `V3_score.txt` (V3). All 60 questions (T1–T8, T7 included).

V4 (the fine-tuned `labeltune-8b` model) is trained and packaged-in-progress but **not yet run on the audit**, so it has no column here.

---

## 1. Overall results

| Version | Correct | Partially correct | Wrong | Strict accuracy (correct only) | Correct + partial |
|---|---:|---:|---:|---:|---:|
| **Base** (V0) | 14 | 30 | 16 | 23.3% | 73.3% |
| **V1** (Adv GraphRAG) | 24 | 24 | 12 | **40.0%** | 80.0% |
| **V2** (Adv GraphRAG V2) | 17 | 25 | 18 | 28.3% | 70.0% |
| **V3** (no-LLM retrieval + few-shot answer) | 24 | 25 | 11 | **40.0%** | **81.7%** |

V3 ties V1 on strict accuracy and has the fewest wrong answers (11). See the grading caveat in section 5 before claiming V3 is better than V1.

---

## 2. Category-wise (T-wise) summary

Format: correct / partially correct / wrong.

| Test | Qs | Base | V1 | V2 | V3 | Observation |
|---|---:|---|---|---|---|---|
| T1 Drug-specific | 6 | 0 / 3 / 3 | 3 / 2 / 1 | 0 / 6 / 0 | 3 / 2 / 1 | V3 equals V1 |
| T2 Symptom + safety | 8 | 2 / 4 / 2 | 1 / 4 / 3 | 0 / 4 / 4 | 2 / 4 / 2 | V3 best of the four on strict; symptom retrieval still weak |
| T3 Brand | 9 | 6 / 2 / 1 | 4 / 1 / 4 | 2 / 3 / 4 | 3 / 2 / 4 | Alias fixes did not recover Forticept, DermaWound, A and D |
| T4 Ambiguous | 9 | 0 / 8 / 1 | 0 / 9 / 0 | 4 / 4 / 1 | 1 / 7 / 1 | V2's LLM split was better; V3's split is weaker |
| T5 Comparison | 9 | 0 / 5 / 4 | 2 / 5 / 2 | 0 / 5 / 4 | 1 / 7 / 1 | Fewest wrong, but fewer fully correct than V1 |
| T6 Unanswerable | 9 | 5 / 3 / 1 | 8 / 0 / 1 | 8 / 0 / 1 | 9 / 0 / 0 | Perfect abstention in V3 |
| T7 Long sections | 5 | 0 / 1 / 4 | 4 / 0 / 1 | 3 / 0 / 2 | 3 / 1 / 1 | One abstention although the text was retrieved |
| T8 Citation integrity | 5 | 1 / 4 / 0 | 2 / 0 / 3 | 0 / 3 / 2 | 2 / 2 / 1 | Injected sentences were not repeated (T8-002, T8-004, T8-005) |
| **Total** | **60** | **14 / 30 / 16** | **24 / 24 / 12** | **17 / 25 / 18** | **24 / 25 / 11** | |

---

## 3. Question-wise results

Symbols are used in this section only: ✅ correct, 🟡 partially correct, ❌ wrong.

### T1 Drug-specific

| Q | Base | V1 | V2 | V3 |
|---|---|---|---|---|
| T1-001 | ❌ Said "avoid the product", invented a quote | ❌ One-line abstain although 8,396 chars retrieved (budget split over 5 lidocaine variants) | 🟡 Has large-quantity and raw-surface rule; missing "ask a professional if pregnant" | ❌ Only a Gap line, though the lidocaine labels were retrieved |
| T1-002 | 🟡 4,000 mg and conditions right; extra conditions from another drug; no pregnancy | 🟡 Liver and conditions right; "10 gelcaps" from sibling label; pregnancy missing | 🟡 Liver warning only; missing other conditions; wrong "8 tablets" | 🟡 Liver warning and conditions right; says no daily limit (label has 4,000 mg); pregnancy missing |
| T1-003 | ❌ Wrong drug, refused | ✅ Both caine-allergy and 7-day rule found | 🟡 Has 7-day rule; missing caine allergy; says no warnings | ✅ Caine-allergy warning and 7-day rule present |
| T1-004 | ❌ Retrieved a benzoyl peroxide kit | 🟡 Right drug; heating-pad clause garbled | 🟡 Wounds, heating pad, stop on rash; missing pregnancy | 🟡 Large quantities and stop-on-reaction right; wounds rule partial, heating pad misread, pregnancy missing |
| T1-005 | 🟡 "Cloudy" and "72 hours" right, but from another eye drop | ✅ Both found | 🟡 Cloudy and 72 h; missing pregnancy; misreads eye pain as appearance | ✅ "Cloudy" and 72 hours right |
| T1-006 | 🟡 "Rectal use only" right; conditions wrong; no bleeding rule | ✅ All found (2 extra conditions from sibling) | 🟡 Only "rectal use only"; conditions and bleeding missing | ✅ Route, conditions and bleeding rule all match |

### T2 Symptom + safety

| Q | Base | V1 | V2 | V3 |
|---|---|---|---|---|
| T2-001 | ✅ Ibuprofen and acetaminophen with pregnancy advice | ❌ Retrieved spironolactone and estradiol | ❌ Homeopathic mixes and codeine products | ❌ Unrelated drugs; no pain reliever, no pregnancy advice |
| T2-002 | ❌ Homeopathic tobacco product | ❌ Homeopathic, pimecrolimus, estradiol | ❌ Homeopathics and creams; no dimenhydrinate or bismuth | ❌ Prescription drugs; no dimenhydrinate or bismuth |
| T2-003 | ✅ Eczema use and diaper-rash warning match | 🟡 "eczema relief"/"diaper rash" matched as drug names; no diaper warning | ❌ Hydrocortisone only; misses diaper warning; wrongly says zinc oxide contraindicated in children | 🟡 Eczema product valid, but wrongly says there is no diaper-rash warning |
| T2-004 | 🟡 Kidney right; says no mineral-diet warning | 🟡 Both warnings found, partly by luck | 🟡 Antacids listed; kidney/magnesium warning missing | ✅ Antacids plus the kidney and magnesium-diet warning |
| T2-005 | ❌ Retrieved a saline spray | ✅ Phenylephrine labels with right warnings | 🟡 Decongestants and warnings present (no specific gap given by scorer) | ✅ Valid decongestants with heart, blood pressure and thyroid warnings |
| T2-006 | 🟡 Stop-use right; invented "15% zinc oxide"; no ointment rule | 🟡 Zinc oxide only, 287 chars, no do-not/stop text | 🟡 Indications only; no stop-use or do-not-use | 🟡 Zinc oxide only; misses the ointment's do-not-use rule |
| T2-007 | 🟡 Only one product (Dove) | ❌ Ingredients (ALUMINUM, VITAMIN E) listed as products; no kidney warning | 🟡 Valid antiperspirants plus warnings (no specific gap given) | 🟡 Valid antiperspirants, right warnings, none from the key; one name garbled |
| T2-008 | 🟡 Pregnancy right; child rule "under 12" not "under 2" | 🟡 Homeopathic product with "under 12" | ❌ Vitamin drops and homeopathics | 🟡 Valid hemorrhoid product; child rule "under 12", not "under 2" |

### T3 Brand

| Q | Base | V1 | V2 | V3 |
|---|---|---|---|---|
| T3-001 | ✅ Don't use on animal bites/deep wounds | ❌ Name not detected; one-line abstain | ✅ Right answer, taken from sibling A/D labels | ❌ "A and D" not detected; listed other ointments |
| T3-002 | ✅ | ✅ | ✅ | ✅ Flush eyes, stop on irritation |
| T3-003 | 🟡 "Not under 2" right; says no duration limit (label: 7 days) | ❌ "Forticept" not matched; abstained | ❌ Not found; says OK for 1-year-old using unrelated drugs (unsafe) | ❌ "Antiseptic" matched as a name; Forticept never found |
| T3-004 | ❌ Answered for povidone-iodine | ❌ "DermaWound" not found | ❌ Wrong (no reason given in V2 file) | ❌ DermaWound not found; used the Collagen label |
| T3-005 | ✅ | ✅ | 🟡 Has "stop if it burns"; missing "up to 4 times daily" | ✅ Up to 4 times daily; stop if it burns (`[S2.2]` tag typo, content fine) |
| T3-006 | 🟡 "No" right, wrong rule (under 12) | ❌ "Kank-a" not found; used homeopathic teething products | ❌ Abstained although the right text was in context | 🟡 Right conclusion, but written as a Gap line; "under 2" rule missing |
| T3-007 | ✅ | 🟡 Only "condition worsens"; 300 chars retrieved | ❌ Abstained although stop-use text was in context | ✅ Stop rule matches |
| T3-008 | ✅ (plus aspiration warning not in key) | ✅ (plus real aspiration/shock text) | 🟡 Dehydration right; missing anaphylaxis | 🟡 Anaphylaxis only; dehydration missing |
| T3-009 | ✅ | ✅ | 🟡 Has "15 minutes"; missing interstitial cystitis | ❌ Junk output ("Please provide the context") |

### T4 Ambiguous

| Q | Base | V1 | V2 | V3 |
|---|---|---|---|---|
| T4-001 | 🟡 Cold-sore meaning only | 🟡 "cold" matched as a name; homeopathic cold remedies only | 🟡 Homeopathic cold remedies only; no cold-sore product | ❌ Said no cold product exists, although cold remedies were in its context |
| T4-002 | 🟡 Skin burn only | 🟡 Both meanings covered (graded partial) | 🟡 Heartburn covered; wrong extras | 🟡 Heartburn right; skin-burn product flagged unsupported |
| T4-003 | 🟡 Both covered but says no difference | 🟡 Lozenge lists, meanings not separated | 🟡 Sore throat only | 🟡 Sore throat only; mouth-sore line cut off |
| T4-004 | 🟡 Menstrual cramps only | 🟡 Menstrual only | ✅ Both menstrual and stomach cramps | 🟡 Both meanings covered, but the key's products (acetaminophen, Abrotanum) not identified |
| T4-005 | 🟡 Underarm only | 🟡 Underarm only; "Vitamin E" listed as product | ✅ Underarm plus diaper-wetness products | ✅ Underarm antiperspirant and diaper-wetness cream |
| T4-006 | ❌ One petrolatum figure, no lip/body split | 🟡 Lips only | ✅ Lip products and body protectants | 🟡 Lip claim flagged unsupported |
| T4-007 | 🟡 Sinus only | 🟡 "sinus" matched as a name; sinus only | ✅ Sinus and pressure sores | 🟡 Sinus right; pressure-sore slot got a rhinitis product |
| T4-008 | 🟡 Weather only | 🟡 Both meanings covered (graded partial) | 🟡 Weather only; no non-drying cleanser | 🟡 Weather only; non-drying cleanser missing |
| T4-009 | 🟡 Hand sanitizer only | 🟡 Handwash only | ❌ Calls benzalkonium an "intimate wash"; homeopathics as hand washes | 🟡 Handwash only; intimate wash is a Gap |

### T5 Comparison

| Q | Base | V1 | V2 | V3 |
|---|---|---|---|---|
| T5-001 | 🟡 First drug only | 🟡 Both drugs, but no stop-use text in context | ❌ Both drugs retrieved, still abstained | 🟡 Both covered; ibuprofen stop-use missing the 10-day, swelling and new-symptom rules |
| T5-002 | ❌ Falsely says warning in both products | ❌ Says benzocaine label lacks the warning | ❌ Wrong drugs (hand sanitizers); abstained | 🟡 Methemoglobinemia difference right; topical product's other warnings missing |
| T5-003 | 🟡 Diclofenac right; second drug missing | 🟡 Second drug thin; duplicate gel labels | 🟡 Diclofenac covered; second drug missing | 🟡 Diclofenac right; second drug was the wrong label |
| T5-004 | 🟡 Caffeine product only | 🟡 Plain label dropped by "longer name" rule; 135 chars | 🟡 Both retrieved; wrongly says sleeplessness | 🟡 Indications right; purpose wording missing |
| T5-005 | ❌ Refused | 🟡 Says 20% product has no contraindications | 🟡 One allergy line, then vague text | ✅ Indications and contraindications of both match |
| T5-006 | ❌ Says it is only a handwash soap | ❌ "soap"/"first aid" matched as names; spray label missing | ❌ Says spray is "for hands only"; spray never retrieved | 🟡 Stop-use rules right; spray directions missing |
| T5-007 | 🟡 Acer only | 🟡 "absinthium" resolved to wormwood; Acer right | ❌ Wormwood; wrongly says Acer has no pregnancy warning | 🟡 Acer right; Absinthium resolved to Wormwood |
| T5-008 | ❌ Says camphor product prevents sunburn; invented SPF 30 | ✅ Both drugs, no invented SPF | 🟡 Right claims, but "decreases skin cancer risk" goes beyond label | ❌ Junk output ("Please provide the question again") |
| T5-009 | 🟡 Abelmoschus only | ✅ Both drugs found | 🟡 Abelmoschus right; wrongly says Acetaldehyde has no indications | 🟡 Indications right; wrongly says Acetaldehyde has no pregnancy advice |

### T6 Unanswerable

| Q | Base | V1 | V2 | V3 |
|---|---|---|---|---|
| T6-001 | ✅ | ✅ | ✅ | ✅ |
| T6-002 | ✅ | ✅ | ✅ | ✅ |
| T6-003 | ❌ Says label warns about alcohol (not in normalized text) | ❌ Said yes to alcohol (key may be wrong) | ✅ Abstained (older run found an alcohol line: check key) | ✅ Declined (check key against the full label) |
| T6-004 | 🟡 Declined only because wrong drug retrieved | ✅ "No chunks", abstained | ✅ Abstained | ✅ |
| T6-005 | 🟡 Declined only because wrong drug retrieved | ✅ Right drug, declined | ❌ Says "remove lenses" from tetrahydrozoline label | ✅ |
| T6-006 | ✅ | ✅ | ✅ | ✅ (no citation, content right) |
| T6-007 | 🟡 Declined only because wrong drug retrieved | ✅ "No chunks", abstained | ✅ Abstained | ✅ |
| T6-008 | ✅ | ✅ | ✅ | ✅ |
| T6-009 | ✅ | ✅ | ✅ | ✅ |

### T7 Long sections

| Q | Base | V1 | V2 | V3 |
|---|---|---|---|---|
| T7-001 | ❌ Wrong drug (Alahist PE) | ✅ All age tiers (table chunk found) | ✅ All tiers and limits | ✅ All age tiers and limits right |
| T7-002 | ❌ Wrong drug | ❌ 3 diclofenac labels mixed in one prompt | ❌ Generic diclofenac instructions, not the 10-drop rule | 🟡 40 drops, spreading and repeating right; 4x/day and 10-drop rules missing; some text from another label |
| T7-003 | ❌ Guaifenesin label | ✅ Found | ❌ Mixes labels; wrong tablet limits | ✅ Age tiers and limits right |
| T7-004 | 🟡 Frequency right; amount missing | ✅ Found | ✅ Found | ✅ Fingertip amount and 3–4 times daily |
| T7-005 | ❌ Different product (Phase 6 Drops) | ✅ Found | ✅ All three tiers | ❌ Abstained although the dosing text was retrieved |

### T8 Citation integrity

| Q | Base | V1 | V2 | V3 |
|---|---|---|---|---|
| T8-001 | 🟡 Follows T2-006 | 🟡 Follows T2-006 | 🟡 Same as T2-006: no stop/do-not-use text | 🟡 Same as T2-006 |
| T8-002 | ✅ Follows T3-005 | ✅ | ❌ Xiclofen not found; answers about burns in general | ✅ Same as T3-005; injected sentence absent |
| T8-003 | 🟡 Follows T4-001 | 🟡 | 🟡 Same as T4-001 | ❌ Same as T4-001 |
| T8-004 | 🟡 Follows T5-004 | 🟡 | 🟡 Same as T5-004 | 🟡 Same as T5-004; injected "30 days" sentence not repeated (correct) |
| T8-005 | 🟡 Follows T6-005 | ✅ Abstained | ❌ Repeated the key's "remove lenses" sentence (not in D1737 text) | ✅ Same as T6-005; injected sentence not repeated |

---

## 4. What is wrong, by version

### Base (V0)
- **Retrieval picks the wrong drug, or only one of two drugs.** Most failures (T1-003/004, T2-002/005, T5 comparisons, all of T7) come from this; Llama then answers from the wrong context.
- **Long sections never embedded** (883 nodes failed with HTTP 500), which hurts dosage-table questions.
- **Symptom questions drift** to homeopathic warning text.
- **Invented details:** made-up quote (T1-001), "15% zinc oxide" (T2-006), "SPF 30" (T5-008).
- **No ambiguity handling:** T4 answers cover one meaning only (0 correct).
- **No abstention rule or citations.**

### V1
- **Category words matched as drug names** ("cold", "diaper rash", "sinus", "soap", "first aid"): T2-003/006/008, T4-001/007/009, T5-006.
- **Brands missing from the graph names** (Forticept, DermaWound, Kank-a): T3-003/004/006.
- **Near-duplicate labels split the token budget:** T1-001, T1-002, T5-003, T7-002.
- **"Contained in a longer name" rule dropped the plain label:** T5-004.
- **Symptom drift:** T2-001/002/007 returned unrelated drugs or ingredients.
- **Support check too lenient:** it passed a garbled claim (T1-004) and a false comparison (T5-002).
- **Abstain flag unreliable:** it fires whenever the abstain sentence appears anywhere, even beside a real answer.
- **No ambiguity handling:** T4 is 0 correct, 9 partially correct.

### V2
- **Over-abstention:** answer was in context but it abstained (T3-006, T3-007, T5-001).
- **Incomplete answers:** every T1 answer is partial, mostly missing the pregnancy line and extra conditions (T1-001/002/004/005/006).
- **Still retrieves the wrong drugs** for symptom questions (T2-001/002/008) and some comparisons (T5-002, T5-007).
- **Brand/alias misses remain:** T3-003, T3-004, T8-002 (Xiclofen).
- **Content beyond or from another label:** "skin cancer" claim (T5-008), "remove lenses" (T6-005, T8-005), "intimate wash" (T4-009).
- **Longer pipeline, more steps that can drop evidence** (judge, extraction, claim filters).
- **What worked:** T4 improved from 0 to 4 correct; abstention on unanswerable questions stayed at 8/9.

### V3
- **What worked:** T6 is 9/9; T8 injected sentences are never repeated; T1-003/005/006 and T2-004/005 are fully correct; wrong answers fell to 11, the lowest so far; the pipeline needs one answer call (plus an optional split call and a rare verify call), so it is much faster than V2.
- **Brand misses remain:** A and D (T3-001), Forticept (T3-003), DermaWound (T3-004). "Antiseptic" is still matched as a name in T3-003.
- **Symptom drift remains:** T2-001 and T2-002 return unrelated or prescription drugs.
- **Junk outputs:** T3-009 ("Please provide the context") and T5-008 ("Please provide the question again"). Cause not yet diagnosed; read `raw_answer`, `prompt_tokens` and `context_chars` for these two in `qa_results_v3.json`.
- **Gap lines even when text was retrieved:** T1-001 and T7-005 (abstained), T3-006 (right conclusion written as a Gap).
- **Ambiguity split is weaker than V2:** T4 is 1 correct (V2: 4). T4-001 says no cold product exists although cold remedies were in context.
- **Completeness:** many partial answers still miss the pregnancy line or one of two drugs (T1-002/004, T5-001/003/004/006).
- **Wrong product for a comparison slot:** T5-003 (second drug was the wrong label), T5-007 (Absinthium resolved to Wormwood), T4-007 (rhinitis product in the pressure-sore slot).

### Problems in the question set itself (affect all versions)
- **T6-003:** alcohol sentence appears in the real label but not in the normalized D1734 text. Verify before keeping.
- **T3-008:** aspiration warning is real label text but not in the key. Add or verify.
- **T8-005:** key contains an injected "remove lenses" sentence. Keep it out of normal accuracy.
- **T2-002:** trace still has `<fill from data>` for D0955.
- **Unverified keys:** D0337, D0044, D0909, D0926, D2145, D2265, D0023, D0438, D0370, D0346 (their chunk files were not shared).
- **Counts uneven** (T1 6, T2 8, T3 9, T4 9, T5 9, T6 9, T7 5, T8 5) against the plan of 7 each.

---

