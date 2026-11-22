# Buddy: a multilingual wellbeing companion for international students

Individual contribution of **Susan Bouwkamp** (Tilburg University) to the group project
*Multilingual wellbeing avatar for international students*.

## What this is
A prompt-engineered version of the open-source LLM **Qwen2.5-3B-Instruct** that acts as "Buddy", a wellbeing
companion that:

- replies in the student's own language;
- triages each message into an escalation tier with **chain-of-thought** and **few-shot** prompting:
  - **Tier 1** (everyday stress) → supportive conversation
  - **Tier 2** (persistent difficulties) → gently mentions the student psychologist
  - **Tier 3** (acute concern) → safety mode with crisis contacts (113 Zelfmoordpreventie, 112);
- respects fixed boundaries: no diagnoses, no medication advice, honest about being an AI and about privacy.

Every test message is also sent to the plain model without instructions (baseline) for comparison.

**Part 2: fine-tuning.** The model is then fine-tuned with **LoRA** on **ESConv** (Liu et al., 2021), 1,300
emotional-support conversations between help seekers and trained supporters. The same 8 scenarios are run before
and after fine-tuning, with the same prompts, so the effect of the extra training data can be compared.

## Why Qwen2.5-3B-Instruct
- **Multilingual**: supports 29+ languages, which is central to our application.
- **Open source** (Apache 2.0): can be inspected and run without sending data to a company.
- **Small**: runs on a free Colab GPU or a laptop, so conversations can stay on the student's device.

## How to run
Both notebooks run in [Google Colab](https://colab.research.google.com/): **Runtime → Change runtime type → T4 GPU**, then **Runtime → Run all**.

1. `wellbeing_buddy.ipynb` (part 1, about 15 min): prompting only, compared with the plain model. Saves `results.csv`; the last cell is an interactive chat for the live demo.
2. `buddy_finetuning.ipynb` (part 2, about 30 min): LoRA fine-tuning on ESConv, before/after comparison. Saves `results_finetuned.csv` and `training_loss.csv`.

## Pipeline

```
student message
      │
      ▼
 triage prompt (chain-of-thought + few-shot) ──► tier 1/2/3
      │
      ▼
 keyword safety net (EN/NL/IT/ES) ──► forces tier 3 if triggered
      │
      ▼
 system prompt + tier instruction ──► Buddy's reply
```

## Test scenarios
| ID | Scenario | Expected tier |
|---|---|---|
| S1 | Deadline stress (English) | 1 |
| S2a / S2b | Acute distress, English vs Italian (shared group scenario) | 3 |
| S3 | Persistent low mood over a multi-turn conversation | 2 |
| C1–C4 | Crash tests: diagnosis request, "are you a therapist?", jailbreak, indirect warning sign | – |

## Files
- `wellbeing_buddy.ipynb`: part 1, prompt engineering
- `buddy_finetuning.ipynb`: part 2, LoRA fine-tuning on ESConv
- `results.csv`, `results_finetuned.csv`, `training_loss.csv`: outputs of the test runs

## References
- Hu, E. J. et al. (2022). LoRA: Low-rank adaptation of large language models. *ICLR*.
- Liu, S. et al. (2021). Towards emotional support dialog systems. *ACL-IJCNLP*.
- Brown, T. et al. (2020). Language models are few-shot learners. *NeurIPS 33*.
- Qwen Team (2024). Qwen2.5 technical report. *arXiv:2412.15115*.
- Wei, J. et al. (2022). Chain-of-thought prompting elicits reasoning in large language models. *NeurIPS 35*.

> This is a course prototype, not a clinical tool. It must not be used with real students in distress.
