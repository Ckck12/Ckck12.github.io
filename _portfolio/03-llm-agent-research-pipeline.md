---
title: "LLM Agent Research Pipeline with Cross-Model Review"
order: 3
redirect_from:
  - /portfolio/02-llm-agent-research-pipeline/
excerpt: "Personal project (Sep. 2026): a multi-agent research workflow on the open-source ARIS skill pack, plus a cross-model-family reviewer harness with provenance logging.<br/><img src='/images/projects/llm_agent_pipeline.png' alt='LLM agent pipeline'>"
tldr_en:
  - "A multi-agent research pipeline (open-source ARIS on Claude Code) is fast, but a model family reviewing its own output is unreliable."
  - "I wrote the research brief and built a cross-model reviewer harness (Gemini API, Codex CLI) with fallback and provenance logging."
  - "It exposed disagreements: I kept the code-grounded reviewer's objections open, caught a reviewer error and dropped a citation that failed an existence check."
tldr_ko:
  - "멀티 에이전트 연구 파이프라인(Claude Code 위의 오픈소스 ARIS)은 빠르지만, 같은 모델 계열의 자기 평가는 믿기 어렵습니다."
  - "연구 브리프를 작성하고, 폴백과 출처 기록을 갖춘 교차 모델 리뷰어 하네스(Gemini API, Codex CLI)를 만들었습니다."
  - "코드 기반 리뷰어의 지적을 열린 이슈로 유지했고, 리뷰어의 사실 오류를 잡았으며, 존재 확인에 실패한 인용을 제외했습니다."
collection: portfolio
---

{% include lang-toggle.html %}

{% include tldr.html %}

<div class="proj-body">

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/projects/llm_agent_pipeline.png" alt="LLM agent research pipeline with a cross-model review stage" loading="lazy">
</figure>

<div class="lang-en">
<p><strong>Personal project, Sep. 2026.</strong> Built on the open-source <a href="https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep">ARIS</a> skill pack for Claude Code (ARIS is not my work; all credit to its authors). ARIS runs a research brief through idea fan-out, jury scoring, kill-argument, refinement and paper-plan stages.</p>
<h2 class="star-h"><span class="star-tag">S</span>Situation</h2>
<p>A multi-agent pipeline can produce research ideas and paper plans quickly, but when a single model family judges its own output, the review is easy to over-trust.</p>
<h2 class="star-h"><span class="star-tag">T</span>Task</h2>
<p>Give the pipeline a well-specified brief, and add an independent, auditable review stage from other model families.</p>
<h2 class="star-h"><span class="star-tag">A</span>Action</h2>
<ul>
<li>Wrote the 7-requirement research brief that every agent run took as its only input.</li>
<li>Built a Python cross-model-family reviewer harness: a Gemini API reviewer with the API key read from an env file (not code), automatic fallback across 3 models with back-off retries, and a provenance header recording which model actually answered.</li>
<li>Added Codex CLI (read-only) as a second reviewer that checks claims against the target model's released code.</li>
</ul>
<h2 class="star-h"><span class="star-tag">R</span>Result</h2>
<ul>
<li>The provenance header showed which model really answered: the first draft was reviewed by a lighter fallback Gemini model because the first-choice models were overloaded. The flash-tier Gemini reviewers scored the two drafts 8.2 and 7.63, both with a REVISE verdict; Codex, checking claims against the released code, scored them 5.35 and 5.65 (no pass).</li>
<li>I kept Codex's objections marked OPEN instead of reporting the higher score, caught a reviewer error (a claimed hidden size of at least 2048 vs. the actual 896), and dropped a recommended citation that failed an existence check.</li>
<li>Skills practised: LLM agents, multi-agent orchestration, Claude Code, Codex CLI, Gemini API, MCP servers, evaluation and provenance logging, LLM-as-a-judge reliability.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<p><strong>개인 프로젝트, 2026년 9월.</strong> Claude Code용 오픈소스 <a href="https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep">ARIS</a> 스킬 팩 위에서 진행했습니다(ARIS는 제 작업이 아니며, 공로는 원저자들에게 있습니다). ARIS는 연구 브리프를 아이디어 확장, 심사 점수화, 반론(kill-argument), 개선, 논문 계획 단계로 처리합니다.</p>
<h2 class="star-h"><span class="star-tag">S</span>상황 (Situation)</h2>
<p>멀티 에이전트 파이프라인은 연구 아이디어와 논문 계획을 빠르게 만들어 내지만, 한 모델 계열이 자기 결과물을 평가하면 그 리뷰를 과신하기 쉽습니다.</p>
<h2 class="star-h"><span class="star-tag">T</span>과제 (Task)</h2>
<p>파이프라인에 잘 정의된 브리프를 주고, 다른 모델 계열이 수행하는 독립적이고 추적 가능한 리뷰 단계를 추가한다.</p>
<h2 class="star-h"><span class="star-tag">A</span>행동 (Action)</h2>
<ul>
<li>모든 에이전트 실행이 유일한 입력으로 사용한 7개 요구사항의 연구 브리프를 작성했습니다.</li>
<li>Python으로 교차 모델 계열 리뷰어 하네스를 만들었습니다. API 키를 코드가 아닌 env 파일에서 읽는 Gemini API 리뷰어, 3개 모델 간 자동 폴백과 백오프 재시도, 실제로 어떤 모델이 답했는지 기록하는 출처 헤더를 갖췄습니다.</li>
<li>대상 모델의 공개 코드와 주장을 대조하는 두 번째 리뷰어로 Codex CLI(읽기 전용)를 추가했습니다.</li>
</ul>
<h2 class="star-h"><span class="star-tag">R</span>결과 (Result)</h2>
<ul>
<li>출처 헤더로 실제 응답 모델을 확인할 수 있었습니다. 첫 번째 초안은 1순위 모델들이 과부하 상태여서 더 가벼운 폴백 Gemini 모델이 리뷰했습니다. flash급 Gemini 리뷰어는 두 초안에 8.2점과 7.63점(둘 다 REVISE 판정)을, 공개 코드와 주장을 대조한 Codex는 5.35점과 5.65점(통과 아님)을 주었습니다.</li>
<li>더 높은 점수를 보고하는 대신 Codex의 지적을 OPEN 상태로 유지했고, 리뷰어의 오류(hidden size가 2048 이상이라는 주장, 실제 896)를 잡아냈으며, 존재 확인에 실패한 추천 인용을 제외했습니다.</li>
<li>다룬 기술: LLM 에이전트, 멀티 에이전트 오케스트레이션, Claude Code, Codex CLI, Gemini API, MCP 서버, 평가·출처 로깅, LLM-as-a-judge 신뢰도.</li>
</ul>
</div>

</div>
