# LabelTune — Fine-Tuning Llama-3 8B for Grounded GraphRAG Question Answering

A complete record of the fine-tuning run carried out on 16–17 November 2026: what was built, why each decision was taken, what went wrong, how it was fixed, and how the result was verified.

---

## 1. Purpose

The GraphRAG system over OpenFDA drug labels (110,843 nodes, 355,909 edges, FAISS retrieval, Llama 3 8B via Ollama) had been evaluated on a frozen 60-question audit in eight categories (T1 drug-specific, T2 symptom and safety, T3 brand, T4 ambiguous, T5 comparison, T6 unanswerable, T7 long sections, T8 citation integrity). Recorded strict accuracy: Base 23.3%, V1 40.0%, V2 28.3%.

LabelTune fine-tunes the underlying 8B model so that it answers in the pipeline's grounded format: reason over the supplied excerpt first, then answer in a bullet that cites its source. The fine-tuned model is packaged as an Ollama model named `labeltune-8b` and is evaluated as **V4** by re-running the same frozen audit with only the model name changed.

## 2. Key design decisions

### 2.1 No OpenFDA data in training (leakage rule)

The knowledge graph and the 60-question audit are both built from OpenFDA label text. Training on OpenFDA or DailyMed label text would contaminate the audit: the model could appear to improve simply by having memorised the test material. OpenFDA data was therefore excluded from training completely.

### 2.2 Training dataset

- **FreedomIntelligence/medical-o1-reasoning-SFT** (Hugging Face), English subset (`"en"`), the chain-of-thought medical reasoning set built for HuatuoGPT-o1.
- Fields used: `Question`, `Complex_CoT`, `Response`.
- **3,000 examples** used for training, taken from the head of the dataset shuffled with seed 42.

### 2.3 Reshaping into the grounded format

The raw dataset teaches answering medical questions from memory. The pipeline contract is the opposite: answer only from a supplied excerpt, with a citation. Each dataset row was therefore reshaped before training:

- **Excerpt:** the row's `Response`, shortened to 220 words, presented as source `[S1]`.
- **User message:** `EXCERPTS:` block with the excerpt, then `QUESTION:`, then `ANSWER:`.
- **Target (assistant) message:** `Reasoning: ` followed by the row's `Complex_CoT` shortened to 110 words, a blank line, then a single bullet made from the `Response` shortened to 90 words and ending with the citation `[S1]`.
- **System prompt:** copied verbatim from the pipeline script `adv_graphrag.py`, including the abstention line `INSUFFICIENT FDA DATA: The retrieved label text does not contain this information.`
- Training used prompt-masked SFT: loss is computed only on the assistant's answer tokens, not on the prompt.

### 2.4 A synthetic behaviour pack was designed, then dropped

An earlier design added 800 synthetic examples teaching abstention (T6), ambiguity handling (T4), separated comparisons (T5) and citation integrity (T8) on fictional drugs. On review, this was dropped: the synthetic examples were variations of a small number of seed terms and templates, which risks teaching surface patterns rather than behaviour, and the T1–T8 categories are categories of clinical-case questions that the reasoning dataset already exercises. The final run is **dataset-only**: all 3,000 examples come from medical-o1, and the audit measures the effect per category afterwards.

### 2.5 Base model

- Intended base: `meta-llama/Meta-Llama-3-8B-Instruct`, the Hugging Face twin of the pipeline's Ollama `llama3:8b`. (Ollama is inference-only and cannot train; every fine-tuning route loads the Hugging Face weights and returns the result to Ollama afterwards.)
- The Meta repository is gated, and access was still awaiting review at run time, so the run used the ungated mirror **`NousResearch/Meta-Llama-3-8B-Instruct`** — the same weights, no approval needed.

## 3. Environment and setup

- **Platform:** Google Colab, with Google Drive mounted at `/content/drive`. All durable outputs were written to Drive, so runtime restarts lose nothing.
- **Drive layout** (`MyDrive/GraphRAG_FineTune/`):
  - `data/train_t1t8.jsonl` — the assembled training file
  - `adapter_labeltune_8b/` — the trained LoRA adapter, plus per-epoch checkpoints
  - `merged_labeltune_8b/` — the merged full model
  - `gguf/` — `labeltune-8b-f16.gguf`, `labeltune-8b-q4_k_m.gguf`, and the `Modelfile`
  - `training_summary.json` — the run's configuration record
- **Libraries:** `transformers`, `peft`, `bitsandbytes`, `accelerate`, `datasets`, `torch` (Colab-provided, plus notebook installs). Hugging Face access via a token stored as a Colab secret (only relevant to gated repositories; the mirror used here is public).
- **GPU used:** Tesla T4 (free/paid Colab tier). An A100 was considered for the merge step only (see §6).

## 4. Training configuration

| Setting | Value |
|---|---|
| Method | QLoRA (4-bit) |
| Quantisation | NF4, double quantisation, bf16 compute |
| LoRA rank / alpha / dropout | 16 / 32 / 0.05 |
| Target modules | q, k, v, o, gate, up, down projections |
| Trainable parameters | 41,943,040 of 8,072,204,288 (0.5196%) |
| Examples | 3,000 (all medical-o1, grounded format) |
| Max sequence length | 1024 tokens |
| Batch | 2 per device × 8 gradient accumulation = effective 16 |
| Epochs | 2 (376 steps total) |
| Learning rate | 2e-4, cosine schedule, warm-up 12 steps |
| Checkpointing | Saved at the end of every epoch (steps 188 and 376) |
| Seed | 42 |

## 5. Problems encountered during training, and fixes

1. **Gated repository error (403).** Loading `meta-llama/Meta-Llama-3-8B-Instruct` failed because Meta's access review was pending. Fixed by switching the base model to the `NousResearch` mirror — in both the tokenizer and the model loading lines (changing only one leaves the other pointing at the gated repo).
2. **`warmup_ratio` rejected.** The installed `transformers` version no longer accepts `warmup_ratio` in `TrainingArguments`. Fixed by using `warmup_steps=12` (the same 3% warm-up).
3. **Duplicate PEFT warnings.** The training cell was executed twice; the second run warned about modifying a model that already carried an adapter. Harmless: the trainable parameter count (41,943,040, exactly one rank-16 adapter) confirmed a single adapter was trained. The run was left to continue rather than restarted.
4. **Slow overnight progress.** An overnight run on the T4 advanced far less than expected. Diagnosis, in order: the model **was** on the GPU (the load step printed `model loaded on: cuda:0`); the cause was the unattended free runtime pausing/throttling, aggravated by the laptop sleeping and freezing the browser session. Mitigations: laptop sleep set to never, Colab tab kept open, and the per-epoch checkpoint in Drive bounding the loss from any disconnect. The T4 pace when actually running was about 0.04–0.05 iterations/second, roughly 20 seconds per step, about 2 hours 15 minutes for the full run.
5. **Training completed** on 17 November 2026. The save step wrote the adapter and tokenizer to `adapter_labeltune_8b/` and the configuration record to `training_summary.json`, which confirms: mirror base model, medical-o1 (en), 3,000 examples, LoRA r16/alpha 32/dropout 0.05, 2 epochs, learning rate 2e-4, no OpenFDA text in training.

## 6. Merge and packaging

The adapter must be merged into the base weights, converted to GGUF, and quantised before Ollama can serve it.

### 6.1 First attempt — failed, and why

The first merged folder was saved while the model was still in its 4-bit bitsandbytes form, so the saved configuration still declared the bitsandbytes quantisation method. The GGUF converter (`llama.cpp/convert_hf_to_gguf.py`) refuses that input: `NotImplementedError: Quant method is not yet supported: 'bitsandbytes'`. The quantise step then failed in cascade because the f16 GGUF it expected did not exist. The `ollama create` line also reported `ollama: not found`, which is expected inside Colab and harmless — that command belongs on the local machine.

### 6.2 Fix — merge again in bf16

- Load the base model in **bf16** (no quantisation), attach the adapter with `PeftModel.from_pretrained`, call `merge_and_unload()`, and save with `safe_serialization=True`. The old merged folder is deleted first so no stale files survive.
- One environment clash had to be cleared first: the installed `peft` refused to import because the runtime's `torchao` (0.10.0) was below its minimum (0.16.0). torchao is used for nothing in this workflow, so it was uninstalled (`pip uninstall -y torchao`) and the merge cell then ran unchanged.
- Memory note: a bf16 8B model needs about 16 GB. On the T4 (~15 GB usable) part of the model was offloaded to CPU memory during the merge; this works but is slow, and the save step warns about offloaded modules. An A100 runtime, or a CPU runtime with high RAM, avoids the offload entirely.
- **Verification of the merged folder:** the `.safetensors` file is about 16 GB, and `config.json` contains **no** `quantization_config` entry. Both were confirmed before proceeding.

### 6.3 Packaging steps

1. Clone `llama.cpp` and install its Python requirements (note: this install can replace the runtime's `torch` with a CPU build — restart the runtime before any future training in the same session).
2. Convert: `convert_hf_to_gguf.py` on the merged folder, output type f16 → `labeltune-8b-f16.gguf` (about 16 GB).
3. Build the `llama-quantize` tool with CMake.
4. Quantise: f16 GGUF → `labeltune-8b-q4_k_m.gguf` (about 4.9 GB).
5. The `Modelfile` (written by the packaging step) points at the q4_k_m GGUF and reproduces the pipeline's serving settings: the Llama-3 chat template, `temperature 0.1`, `num_ctx 8192`, stop token `<|eot_id|>`.
6. On the local machine, where Ollama is installed: `ollama create labeltune-8b -f Modelfile`, confirmed with `ollama list`.

## 7. Verification that the model is fine-tuned

A dedicated test cell checks the merged model **before** packaging. It saves nothing.

- **Test questions:** the last 3 rows of the same seed-42 shuffled dataset — well beyond the 3,000-example training slice, so the model never trained on them. This is the notebook's own prepared held-out set; no new questions were created.
- **Method:** each question is presented in the exact training format (system prompt, excerpt as `[S1]`, question), generation at temperature 0.1 with a 300-token cap.
- **Pass rule:** an answer passes if it opens with `Reasoning:` and contains the `[S1]` citation — the structure that exists only in the training targets.
- **Result (17 November 2026): 2 of 3 probes passed in full**, and the third followed the same format but was cut off by the 300-token cap mid-answer, before its citation could print — a length artifact, not a format failure. All three answers were clinically consistent with their excerpts (lung abscess; tuberculous meningitis; incineration of biohazardous dressings). Verdict: **fine-tuned and ready**.
- **Optional comparison:** the same test cell can be run against the base model (point it at `NousResearch/Meta-Llama-3-8B-Instruct` instead of the merged folder) to place base and fine-tuned answers side by side on identical questions.

## 8. Final evaluation (V4)

The decisive measurement is the original frozen 60-question GraphRAG audit, re-run with the single change `LLM_MODEL = 'labeltune-8b'` (in the pipeline script / few-shot notebook), and recorded as **V4** alongside Base (23.3%), V1 (40.0%) and V2 (28.3%), V3 strict accuracy, broken down by T-category. For comparability, V4 should be graded under the same rule, ideally in the same session as a re-grade of the earlier versions, since the earlier versions were graded in different sessions with different strictness.

## 9. Known limitations

- **T7 (long sections) is not a fine-tuning problem** — it stems from retrieval/embedding coverage and is not addressed by this run.
- Answers can occasionally run long and reach a token cap before printing the closing citation; if this appears in the audit, raise the generation token budget rather than retraining.
- Two epochs was the chosen setting. Because checkpoints were saved per epoch, adapters from different epoch counts can be merged and audited separately if a comparison is wanted later.
- The abstention, ambiguity, comparison and citation-integrity behaviours (T4/T5/T6/T8) were deliberately **not** trained synthetically in this run (§2.4); any movement in those categories in V4 comes from the reasoning data alone.

## 10. Reproduction checklist

1. Mount Drive; confirm the `GraphRAG_FineTune` folder layout (§3).
2. Load medical-o1 (en), shuffle with seed 42, reshape the first 3,000 valid rows into the grounded format (§2.3); keep the tail of the shuffle as the held-out test set.
3. Load the base model (mirror) in 4-bit, attach the LoRA configuration, and train with the settings in §4.
4. Save the adapter; check `training_summary.json` against §4.
5. Merge in bf16 (§6.2); verify the merged folder (size, no quantisation in config).
6. Run the held-out test cell (§7); proceed only if the format check passes.
7. Convert to GGUF f16, quantise to q4_k_m, write the Modelfile (§6.3).
8. `ollama create labeltune-8b` locally; run the frozen audit as V4 (§8).

---

*Run dates: 16–17 November 2026. Base model: NousResearch/Meta-Llama-3-8B-Instruct. Dataset: FreedomIntelligence/medical-o1-reasoning-SFT (en), 3,000 examples. Output model: `labeltune-8b`.*
