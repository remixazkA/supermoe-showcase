# How SuperMoE works

A high-level description. Prompts, weights, thresholds and source code are not published (see the [disclosure policy](public-disclosure-policy.md)).

## The round

1. **Question.** The oldest question in a queue is taken. A question points at a specific part of a codebase.
2. **Blind answers.** Each voice receives the same material and answers independently. No voice sees another's answer. Each answer must open with a fixed-format verdict, so a program can read it.
3. **Citation check.** A program verifies every quote and line reference against the real files. A citation that does not exist or does not say what is claimed does not count.
4. **Absence check.** Claims that something is absent are checked against the whole codebase.
5. **Grouping.** Similar claims from different voices are merged, and groups that contradict each other are flagged.
6. **Decision.** Groups are accepted, left in dispute, or marked unverified, through a weighted decision.
7. **New questions.** One voice proposes follow-up questions aimed at what is still disputed or unverified, or at attacking what was accepted. A program validates them (files exist, quoted text is found) before queuing them.

An "accepted" result is not treated as a verified finding. Accepted conclusions are reproduced later with a test or a measurement before anyone relies on them.

## Who is on the panel

Open-weight models from public families, run locally, plus a cloud voice:

- Local voices: Qwen 27B (on the A40), Qwen Coder, Devstral, Gemma, Phi, Granite, GLM, Nemotron, Mellum, gpt-oss and Muse.
- Cloud: a cloud voice (Mistral), with training on our data turned off, and a second cloud voice (GPT) when available.

The roster changes as models are measured; some are added or dropped, and not every voice answers every round.

## Two lanes, taking turns

Models do not all fit in memory at once, so they load in turns across two lanes, one per GPU. A coordinator program handles loading, ordering and shutdown. A round with about a dozen voices takes roughly 12 minutes.

## What is mechanical and what is judgement

| Step | Done by |
|---|---|
| Queue, assembling the question, GPU turns | program |
| Citation checking, weighted decision, absence check | program |
| Grouping similar claims | model today; largely mechanical (see [What we learned](what-we-learned.md)) |
| Spotting that two groups contradict | model (judgement) |
| Proposing new questions | model; validation is a program |
