---
title: "LLM Agent Research Pipeline with Cross-Model Review"
excerpt: "Personal project (Sep. 2026): a multi-agent research workflow on the open-source ARIS skill pack, plus a cross-model-family reviewer harness with provenance logging.<br/><img src='/images/projects/llm_agent_pipeline.png' alt='LLM agent pipeline'>"
collection: portfolio
---

![LLM agent pipeline](/images/projects/llm_agent_pipeline.png)

**Personal project, Sep. 2026.** Built on the open-source [ARIS](https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep) skill pack for Claude Code (ARIS is not my work; all credit to its authors). ARIS runs a research brief through idea fan-out, jury scoring, kill-argument, refinement, and paper-plan stages.

- **Situation:** A multi-agent pipeline produces research ideas and paper plans quickly, but a single model family judging its own output is an unreliable reviewer.
- **Task:** Feed the pipeline a well-specified brief and add an independent, auditable review stage from other model families.
- **Action:** Wrote the 7-requirement research brief that every agent run took as its only input. Built a Python cross-model-family reviewer harness: a Gemini API reviewer with the API key read from an env file (not code), automatic fallback across 3 models with back-off retries, and a provenance header recording which model actually answered; plus Codex CLI (read-only) as a second reviewer that checks claims against the target model's released code.
- **Result:** Flash-tier Gemini reviewers scored 8.2 (paper 1, answered by the lighter fallback model after the first-choice models were overloaded) and 7.63 (paper 2, first-choice model); both verdicts were REVISE. Codex scored 5.35 / 5.65 (no pass). I kept Codex's objections marked OPEN instead of reporting the higher score, caught a reviewer error (a claimed hidden size of at least 2048 vs. the actual 896), and dropped a recommended citation that failed an existence check.
- **Keywords:** LLM agents, multi-agent orchestration, Claude Code, Codex CLI, Gemini API, MCP servers, evaluation and provenance logging, LLM-as-a-judge reliability.
