# Eliminating AI Hallucination and Drift with Python Compliance Checks

**By Joseph Murray Adams — October 2026**

## The problem

Language models drift. Give one a long piece of fiction to write and it wanders off the storyline, invents facts that contradict the established material, and hallucinates details the author never approved. The industry's standard fix is more prompting — longer system prompts, stricter instructions, more examples. It helps, sometimes. It never guarantees.

## The invention

I stopped asking the model to be compliant and started **enforcing compliance in code**.

In my novel-writing pipeline, the model never gets the final word. Everything it generates passes through deterministic Python compliance checks against the story's defined storyline, voice, and pace. If a section drifts, Python rejects it and the model has to rewrite it. The loop is simple:

1. The model generates a section of the manuscript.
2. Python checks that section against the storyline, voice, and pace rules — deterministically, in code.
3. Pass → it stands. Fail → rejected, the model rewrites.
4. Repeat until every section passes.
5. If a section keeps failing after a bounded number of rewrites, the system swaps in a different model and tries again — in practice one switch clears it, rarely two — then restores the default writer and continues. Because every section is gated independently, the user can run one chapter or the whole book in a single pass.

The model is a guesser. Python is the editor. The model gets no benefit of the doubt.

This is not a better prompt. It is a structural guarantee: nothing the model produces reaches the manuscript unless it survives the compliance check. Drift and hallucination are not reduced — they are caught and corrected, every time.

## Evidence: the October 2026 A/B test

Ten sections of one chapter, two models, same material, same checks:

| | gemma4:12b-it-qat | qwen3.6-35b-a3b:q4_k_m |
|---|---|---|
| Architecture | Dense 12B instruct, QAT, ~7.2 GB | 35B MoE (Bartowski imatrix Q4_K_M), ~22.29 GB, ~3B active/token |
| Sampler | temp 1.0 | temp 0.7, top_p 0.8, top_k 20 |
| First-pass strict checks clean | 10/10 (100%) | 9/10 (90%) |
| Redrafts | 0 | 1 (structural) |
| Final strict checks | 10/10 (100%) | 10/10 (100%) |
| Time to all sections clean | 1,240 s | 1,784 s |
| Recurring-motif warnings | 1 | 1 |
| MTLD (lexical diversity) | 70.47 | 112.45 |

Both models cleared every strict compliance check — that is the loop working as designed. For my purpose — a pipeline where every section must pass before it ships — the 12B dense model won on speed: a perfect first pass, no redrafts, 1,240 s against 1,784 s. On lexical diversity, the 35B MoE led decisively (MTLD 112.45 vs 70.47), and the harness's automatic rank named it on that metric. The board is mixed and I claim no flat-out winner; the 12B stays my default because it fits in 8 GB of VRAM and gets through the loop fastest.

The honest read: total parameter count is the wrong axis for generative quality. The MoE activates only ~3B parameters per token, so the real contest was 3B active vs 12B active. And under a reject-and-rewrite loop, the metric that matters most is first-pass accept rate — a weaker model simply rewrites more, but nothing non-compliant ever ships.

Test hardware: a networked two-machine setup — an Acer Aspire Go 15 running the pipeline UI, and an Acer Nitro 16 (RTX 5060, 8 GB VRAM; 32 GB RAM) running the models, with Ollama loading and unloading models as required.

## Beyond fiction

The process generalizes. The same compliance loop applies to brainstorming new coding projects, writing apps that link existing hardware with software to build out new features, and engineering research. The principle is identical: Python Compliance Checks (PCC) keep the AI honest and accountable. Every statement must have a verified source. No more rabbit holes. In research, compliance means source verification — a claim without a verifiable source is rejected the same way a drifting story section is rejected.

## October 15, 2026 — PCC on a second system

The compliance loop now runs on a second, entirely different AI system: my multi-agent Grok Bot, where all sub-agent output passes through a single coordinating agent before it goes anywhere. The checks sit at that choke point — every output is verified in code, no exceptions.

Result: bots can still get things wrong, but no drift or unsourced claim reaches me. Every line that gets through matches a source the checker opened itself. The fiction pipeline was the demonstration; this is the generality claim holding up on new ground.

## The claim

I, Joseph Murray Adams, created this method: a Python-enforced generate → check → reject → rewrite loop that limits or eliminates AI hallucination and drifting against a defined storyline — not by trusting the model, but by verifying it in code.

---

*Joseph Murray Adams is a software engineer and author based in Picton, Ontario. He writes science fiction as J.M. Adams and builds local-first AI tooling.*
