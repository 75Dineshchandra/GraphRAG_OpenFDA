# Results: Base vs V1 (Adv GraphRAG) vs V2 (Adv GraphRAG V2)

Scores use three labels: correct, partially correct and wrong.
Sources: `base_results` (Base), `adv_score` (V1), `V2_score_adv_Rag.md` (V2). All 60 questions (T1–T8, T7 included).

---

## 1. Overall results

| Version | Correct | Partially correct | Wrong | Strict accuracy (correct only) | Correct + partial |
|---|---:|---:|---:|---:|---:|
| **Base** (V0) | 14 | 30 | 16 | 23.3% | 73.3% |
| **V1** (Adv GraphRAG) | 24 | 24 | 12 | **40.0%** | **80.0%** |
| **V2** (Adv GraphRAG V2) | 17 | 25 | 18 | 28.3% | 70.0% |

---

## 2. Category-wise (T-wise) summary

Format: correct / partially correct / wrong.

| Test | Qs | Base | V1 | V2 | Observation |
|---|---:|---|---|---|---|
| T1 Drug-specific | 6 | 0 / 3 / 3 | 3 / 2 / 1 | 0 / 6 / 0 | V1 better, V2 weaker (no fully correct answers) |
| T2 Symptom + safety | 8 | 2 / 4 / 2 | 1 / 4 / 3 | 0 / 4 / 4 | Declines each version; symptom retrieval still weak |
| T3 Brand | 9 | 6 / 2 / 1 | 4 / 1 / 4 | 2 / 3 / 4 | Declines each version |
| T4 Ambiguous | 9 | 0 / 8 / 1 | 0 / 9 / 0 | 4 / 4 / 1 | V2 improves (splitting by meaning helps) |
| T5 Comparison | 9 | 0 / 5 / 4 | 2 / 5 / 2 | 0 / 5 / 4 | V1 better, V2 weaker |
| T6 Unanswerable | 9 | 5 / 3 / 1 | 8 / 0 / 1 | 8 / 0 / 1 | V1 better, V2 unchanged |
| T7 Long sections | 5 | 0 / 1 / 4 | 4 / 0 / 1 | 3 / 0 / 2 | V1 better, V2 slightly weaker |
| T8 Citation integrity | 5 | 1 / 4 / 0 | 2 / 0 / 3 | 0 / 3 / 2 | V1 better, V2 weaker |
| **Total** | **60** | **14 / 30 / 16** | **24 / 24 / 12** | **17 / 25 / 18** | |

---

## 3. Question-wise results

Symbols are used in this section only: ✅ correct, 🟡 partially correct, ❌ wrong.

### T1 Drug-specific

| Q | Base | V1 | V2 |
|---|---|---|---|
| T1-001 | ❌ Said "avoid the product", invented a quote | ❌ One-line abstain although 8,396 chars retrieved (budget split over 5 lidocaine variants) | 🟡 Has large-quantity and raw-surface rule; missing "ask a professional if pregnant" |
| T1-002 | 🟡 4,000 mg and conditions right; extra conditions from another drug; no pregnancy | 🟡 Liver and conditions right; "10 gelcaps" from sibling label; pregnancy missing | 🟡 Liver warning only; missing other conditions; wrong "8 tablets" |
| T1-003 | ❌ Wrong drug, refused | ✅ Both caine-allergy and 7-day rule found | 🟡 Has 7-day rule; missing caine allergy; says no warnings |
| T1-004 | ❌ Retrieved a benzoyl peroxide kit | 🟡 Right drug; heating-pad clause garbled | 🟡 Wounds, heating pad, stop on rash; missing pregnancy |
| T1-005 | 🟡 "Cloudy" and "72 hours" right, but from another eye drop | ✅ Both found | 🟡 Cloudy and 72 h; missing pregnancy; misreads eye pain as appearance |
| T1-006 | 🟡 "Rectal use only" right; conditions wrong; no bleeding rule | ✅ All found (2 extra conditions from sibling) | 🟡 Only "rectal use only"; conditions and bleeding missing |

### T2 Symptom + safety

| Q | Base | V1 | V2 |
|---|---|---|---|
| T2-001 | ✅ Ibuprofen and acetaminophen with pregnancy advice | ❌ Retrieved spironolactone and estradiol | ❌ Homeopathic mixes and codeine products |
| T2-002 | ❌ Homeopathic tobacco product | ❌ Homeopathic, pimecrolimus, estradiol | ❌ Homeopathics and creams; no dimenhydrinate or bismuth |
| T2-003 | ✅ Eczema use and diaper-rash warning match | 🟡 "eczema relief"/"diaper rash" matched as drug names; no diaper warning | ❌ Hydrocortisone only; misses diaper warning; wrongly says zinc oxide contraindicated in children |
| T2-004 | 🟡 Kidney right; says no mineral-diet warning | 🟡 Both warnings found, partly by luck | 🟡 Antacids listed; kidney/magnesium warning missing |
| T2-005 | ❌ Retrieved a saline spray | ✅ Phenylephrine labels with right warnings | 🟡 Decongestants and warnings present (no specific gap given by scorer) |
| T2-006 | 🟡 Stop-use right; invented "15% zinc oxide"; no ointment rule | 🟡 Zinc oxide only, 287 chars, no do-not/stop text | 🟡 Indications only; no stop-use or do-not-use |
| T2-007 | 🟡 Only one product (Dove) | ❌ Ingredients (ALUMINUM, VITAMIN E) listed as products; no kidney warning | 🟡 Valid antiperspirants plus warnings (no specific gap given) |
| T2-008 | 🟡 Pregnancy right; child rule "under 12" not "under 2" | 🟡 Homeopathic product with "under 12" | ❌ Vitamin drops and homeopathics |

### T3 Brand

| Q | Base | V1 | V2 |
|---|---|---|---|
| T3-001 | ✅ Don't use on animal bites/deep wounds | ❌ Name not detected; one-line abstain | ✅ Right answer, taken from sibling A/D labels |
| T3-002 | ✅ | ✅ | ✅ |
| T3-003 | 🟡 "Not under 2" right; says no duration limit (label: 7 days) | ❌ "Forticept" not matched; abstained | ❌ Not found; says OK for 1-year-old using unrelated drugs (unsafe) |
| T3-004 | ❌ Answered for povidone-iodine | ❌ "DermaWound" not found | ❌ Wrong (no reason given in V2 file) |
| T3-005 | ✅ | ✅ | 🟡 Has "stop if it burns"; missing "up to 4 times daily" |
| T3-006 | 🟡 "No" right, wrong rule (under 12) | ❌ "Kank-a" not found; used homeopathic teething products | ❌ Abstained although the right text was in context |
| T3-007 | ✅ | 🟡 Only "condition worsens"; 300 chars retrieved | ❌ Abstained although stop-use text was in context |
| T3-008 | ✅ (plus aspiration warning not in key) | ✅ (plus real aspiration/shock text) | 🟡 Dehydration right; missing anaphylaxis |
| T3-009 | ✅ | ✅ | 🟡 Has "15 minutes"; missing interstitial cystitis |

### T4 Ambiguous

| Q | Base | V1 | V2 |
|---|---|---|---|
| T4-001 | 🟡 Cold-sore meaning only | 🟡 "cold" matched as a name; homeopathic cold remedies only | 🟡 Homeopathic cold remedies only; no cold-sore product |
| T4-002 | 🟡 Skin burn only | 🟡 Both meanings covered (graded partial) | 🟡 Heartburn covered; wrong extras |
| T4-003 | 🟡 Both covered but says no difference | 🟡 Lozenge lists, meanings not separated | 🟡 Sore throat only |
| T4-004 | 🟡 Menstrual cramps only | 🟡 Menstrual only | ✅ Both menstrual and stomach cramps |
| T4-005 | 🟡 Underarm only | 🟡 Underarm only; "Vitamin E" listed as product | ✅ Underarm plus diaper-wetness products |
| T4-006 | ❌ One petrolatum figure, no lip/body split | 🟡 Lips only | ✅ Lip products and body protectants |
| T4-007 | 🟡 Sinus only | 🟡 "sinus" matched as a name; sinus only | ✅ Sinus and pressure sores |
| T4-008 | 🟡 Weather only | 🟡 Both meanings covered (graded partial) | 🟡 Weather only; no non-drying cleanser |
| T4-009 | 🟡 Hand sanitizer only | 🟡 Handwash only | ❌ Calls benzalkonium an "intimate wash"; homeopathics as hand washes |

### T5 Comparison

| Q | Base | V1 | V2 |
|---|---|---|---|
| T5-001 | 🟡 First drug only | 🟡 Both drugs, but no stop-use text in context | ❌ Both drugs retrieved, still abstained |
| T5-002 | ❌ Falsely says warning in both products | ❌ Says benzocaine label lacks the warning | ❌ Wrong drugs (hand sanitizers); abstained |
| T5-003 | 🟡 Diclofenac right; second drug missing | 🟡 Second drug thin; duplicate gel labels | 🟡 Diclofenac covered; second drug missing |
| T5-004 | 🟡 Caffeine product only | 🟡 Plain label dropped by "longer name" rule; 135 chars | 🟡 Both retrieved; wrongly says sleeplessness |
| T5-005 | ❌ Refused | 🟡 Says 20% product has no contraindications | 🟡 One allergy line, then vague text |
| T5-006 | ❌ Says it is only a handwash soap | ❌ "soap"/"first aid" matched as names; spray label missing | ❌ Says spray is "for hands only"; spray never retrieved |
| T5-007 | 🟡 Acer only | 🟡 "absinthium" resolved to wormwood; Acer right | ❌ Wormwood; wrongly says Acer has no pregnancy warning |
| T5-008 | ❌ Says camphor product prevents sunburn; invented SPF 30 | ✅ Both drugs, no invented SPF | 🟡 Right claims, but "decreases skin cancer risk" goes beyond label |
| T5-009 | 🟡 Abelmoschus only | ✅ Both drugs found | 🟡 Abelmoschus right; wrongly says Acetaldehyde has no indications |

### T6 Unanswerable

| Q | Base | V1 | V2 |
|---|---|---|---|
| T6-001 | ✅ | ✅ | ✅ |
| T6-002 | ✅ | ✅ | ✅ |
| T6-003 | ❌ Says label warns about alcohol (not in normalized text) | ❌ Said yes to alcohol (key may be wrong) | ✅ Abstained (older run found an alcohol line: check key) |
| T6-004 | 🟡 Declined only because wrong drug retrieved | ✅ "No chunks", abstained | ✅ Abstained |
| T6-005 | 🟡 Declined only because wrong drug retrieved | ✅ Right drug, declined | ❌ Says "remove lenses" from tetrahydrozoline label |
| T6-006 | ✅ | ✅ | ✅ |
| T6-007 | 🟡 Declined only because wrong drug retrieved | ✅ "No chunks", abstained | ✅ Abstained |
| T6-008 | ✅ | ✅ | ✅ |
| T6-009 | ✅ | ✅ | ✅ |

### T7 Long sections

| Q | Base | V1 | V2 |
|---|---|---|---|
| T7-001 | ❌ Wrong drug (Alahist PE) | ✅ All age tiers (table chunk found) | ✅ All tiers and limits |
| T7-002 | ❌ Wrong drug | ❌ 3 diclofenac labels mixed in one prompt | ❌ Generic diclofenac instructions, not the 10-drop rule |
| T7-003 | ❌ Guaifenesin label | ✅ Found | ❌ Mixes labels; wrong tablet limits |
| T7-004 | 🟡 Frequency right; amount missing | ✅ Found | ✅ Found |
| T7-005 | ❌ Different product (Phase 6 Drops) | ✅ Found | ✅ All three tiers |

### T8 Citation integrity

| Q | Base | V1 | V2 |
|---|---|---|---|
| T8-001 | 🟡 Follows T2-006 | 🟡 Follows T2-006 | 🟡 Same as T2-006: no stop/do-not-use text |
| T8-002 | ✅ Follows T3-005 | ✅ | ❌ Xiclofen not found; answers about burns in general |
| T8-003 | 🟡 Follows T4-001 | 🟡 | 🟡 Same as T4-001 |
| T8-004 | 🟡 Follows T5-004 | 🟡 | 🟡 Same as T5-004 |
| T8-005 | 🟡 Follows T6-005 | ✅ Abstained | ❌ Repeated the key's "remove lenses" sentence (not in D1737 text) |

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
- **Longer pipeline, more steps that can drop evidence** (judge, extraction, claim filters). Check these first: the judge can reject right chunks, and the claim-length and citation-count filters can drop right claims.
- **What worked:** T4 improved from 0 to 4 correct; abstention on unanswerable questions stayed at 8/9.

### Problems in the question set itself (affect all versions)
- **T6-003:** alcohol sentence appears in the real label but not in the normalized D1734 text. Verify before keeping.
- **T3-008:** aspiration warning is real label text but not in the key. Add or verify.
- **T8-005:** key contains an injected "remove lenses" sentence. Keep it out of normal accuracy.
- **T2-002:** trace still has `<fill from data>` for D0955.
- **Unverified keys:** D0337, D0044, D0909, D0926, D2145, D2265, D0023, D0438, D0370, D0346 (their chunk files were not shared).
- **Counts uneven** (T1 6, T2 8, T3 9, T4 9, T5 9, T6 9, T7 5, T8 5) against the plan of 7 each.

---

## 5. Grading caveat (important)

The three score files were written in different sessions and are **not graded by the same rule**:
- Same kind of answer got different labels. V1 marked T1-003/005/006 as correct although the pregnancy line was missing, while V2 marked similar answers as partially correct.
- V1 marked T4-002/008 as partially correct ("both meanings covered"), while V2 marked T4-004/005/006/007 as correct for the same behaviour.

So the V1 versus V2 gap (40% vs 28% strict) is partly real (V2 over-abstains and loses detail) and partly grader strictness. To get a fair comparison, re-grade all three result files with one written rule (for example: correct = every fact in the key present and nothing false; partially correct = some key facts present, nothing false; wrong = key fact missing or a false claim), then recompute section 1.
