---
title: "LLM Agent Research Pipeline with Cross-Model Review"
order: 3
redirect_from:
  - /portfolio/02-llm-agent-research-pipeline/
excerpt: "Personal project (Sep. 2026): a multi-agent research workflow on the open-source ARIS skill pack, plus my cross-model-family reviewer harness with provenance logging.<br/><img src='/images/projects/thumb_llm_agent.png' alt='LLM agent pipeline'>"
excerpt_ko: "개인 프로젝트(2026년 9월): 오픈소스 ARIS 스킬 팩 기반 멀티 에이전트 연구 워크플로와, 직접 만든 출처 기록형 교차 모델 리뷰어 하네스.<br/><img src='/images/projects/thumb_llm_agent.png' alt='LLM agent pipeline'>"
excerpt_zh: "个人项目（2026年9月）：基于开源 ARIS 技能包的多智能体研究工作流，以及我搭建的带来源记录的跨模型族评审框架。<br/><img src='/images/projects/thumb_llm_agent.png' alt='LLM agent pipeline'>"
tldr_en:
  - "A multi-agent research pipeline (open-source ARIS on Claude Code) is fast, but a model family reviewing its own output is unreliable."
  - "I wrote the research brief and built a cross-model reviewer harness (Gemini API, Codex CLI) with fallback and provenance logging."
  - "I kept the code-grounded reviewer's objections open, caught a reviewer error, and dropped a citation that failed an existence check."
tldr_ko:
  - "멀티 에이전트 연구 파이프라인(Claude Code 위 오픈소스 ARIS)은 빠르지만, 같은 모델 계열의 자기 평가는 믿기 어렵습니다."
  - "연구 브리프를 작성하고, 폴백과 출처 기록을 갖춘 교차 모델 리뷰어 하네스(Gemini API, Codex CLI)를 만들었습니다."
  - "코드 기반 리뷰어의 지적을 열린 이슈로 유지하고, 리뷰어 오류를 잡았으며, 존재 확인에 실패한 인용을 뺐습니다."
tldr_zh:
  - "多智能体研究流程（Claude Code 上的开源 ARIS）速度快，但同一模型族自评并不可靠。"
  - "我撰写了研究需求说明，并搭建了带回退与来源记录的跨模型评审框架（Gemini API、Codex CLI）。"
  - "我保留了基于代码的评审者提出的问题，发现了一处评审错误，并删去一条无法核实其存在的引用。"
collection: portfolio
---

{% include tldr.html %}

<div class="proj-body">

<figure class="pfig">
<img src="{{ site.baseurl }}/images/projects/llm_agent_pipeline.png" alt="LLM agent research pipeline with a cross-model review stage" loading="lazy">
<figcaption><span class="lang-en">Claude Code runs the agent pipeline. The reviewer harness sends each proposal to Gemini and Codex and logs which model answered. Scores are reviewer outputs, not benchmark results.</span><span class="lang-ko" lang="ko">Claude Code가 agent pipeline을 실행합니다. Reviewer harness는 각 제안서를 Gemini와 Codex에 보내고, 실제로 답한 모델을 기록합니다. 점수는 리뷰어 출력이며 벤치마크 결과가 아닙니다.</span><span class="lang-zh" lang="zh-Hans">Claude Code 运行 agent pipeline。Reviewer harness 把每份提案发给 Gemini 和 Codex，并记录实际作答的模型。分数为评审模型的输出，并非基准测试结果。</span></figcaption>
</figure>

<div class="lang-en">
<p><strong>Personal project, Sep. 2026.</strong> Built on the open-source <a href="https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep">ARIS</a> skill pack, with Claude Code as the main agent. ARIS takes a brief through idea fan-out, jury scoring, kill-argument, refinement and paper-plan stages.</p>
<h2 class="star-h"><span class="star-tag">S</span>Situation</h2>
<p>A multi-agent pipeline drafts research ideas and paper plans fast. But when one model family judges its own output, the review is easy to over-trust.</p>
<h2 class="star-h"><span class="star-tag">T</span>Task</h2>
<p>Give the pipeline a precise brief. Add an independent, auditable review stage from other model families.</p>
<h2 class="star-h"><span class="star-tag">A</span>Action</h2>
<ul>
<li>Wrote the 7-requirement research brief, the only input to every agent run.</li>
<li>Built a Python cross-model-family reviewer harness:
<ul>
<li>Gemini API reviewer; the API key is read from an env file, not code.</li>
<li>Automatic fallback across 3 models with back-off retries.</li>
<li>A provenance header recording which model actually answered.</li>
</ul>
</li>
<li>Added Codex CLI (read-only) as a second reviewer. It checks claims against the target model's released code.</li>
</ul>
<h2 class="star-h"><span class="star-tag">R</span>Result</h2>
<ul>
<li>The provenance header revealed the real reviewer: draft 1 went to a lighter fallback Gemini model, because the first-choice models were overloaded.</li>
<li>Flash-tier Gemini reviewers scored the two drafts 8.2 and 7.63, both REVISE. Codex, checking against the released code, gave 5.35 and 5.65 (no pass).</li>
<li>I kept Codex's objections marked OPEN instead of reporting the higher score.</li>
<li>I caught a reviewer error (claimed hidden size of at least 2048; actual 896) and dropped a recommended citation that failed an existence check.</li>
<li>Skills practised: LLM agents, multi-agent orchestration, Claude Code, Codex CLI, Gemini API, MCP servers, evaluation and provenance logging, LLM-as-a-judge reliability.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<p><strong>개인 프로젝트, 2026년 9월.</strong> Claude Code용 오픈소스 <a href="https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep">ARIS</a> 스킬 팩 위에서 진행했고, 메인 에이전트는 Claude Code입니다. ARIS는 브리프를 아이디어 확장, 심사 점수화, 반론(kill-argument), 개선, 논문 계획 단계로 처리합니다.</p>
<h2 class="star-h"><span class="star-tag">S</span>상황</h2>
<p>멀티 에이전트 파이프라인은 연구 아이디어와 논문 계획을 빠르게 만듭니다. 하지만 한 모델 계열이 자기 결과물을 평가하면 그 리뷰를 과신하기 쉽습니다.</p>
<h2 class="star-h"><span class="star-tag">T</span>과제</h2>
<p>파이프라인에 명확한 브리프를 주고, 다른 모델 계열의 독립적이고 추적 가능한 리뷰 단계를 추가합니다.</p>
<h2 class="star-h"><span class="star-tag">A</span>행동</h2>
<ul>
<li>모든 에이전트 실행의 유일한 입력인 7개 요구사항의 연구 브리프를 작성했습니다.</li>
<li>Python으로 교차 모델 계열 리뷰어 하네스를 만들었습니다.
<ul>
<li>Gemini API 리뷰어. API 키는 코드가 아닌 env 파일에서 읽습니다.</li>
<li>3개 모델 간 자동 폴백과 백오프 재시도.</li>
<li>실제로 답한 모델을 기록하는 출처 헤더.</li>
</ul>
</li>
<li>두 번째 리뷰어로 Codex CLI(읽기 전용)를 추가했습니다. 대상 모델의 공개 코드와 주장을 대조합니다.</li>
</ul>
<h2 class="star-h"><span class="star-tag">R</span>결과</h2>
<ul>
<li>출처 헤더로 실제 리뷰어를 확인했습니다. 1순위 모델들이 과부하여서 초안 1은 더 가벼운 폴백 Gemini 모델이 리뷰했습니다.</li>
<li>flash급 Gemini 리뷰어는 두 초안에 8.2점과 7.63점(둘 다 REVISE)을 주었습니다. 공개 코드와 대조한 Codex는 5.35점과 5.65점(통과 아님)이었습니다.</li>
<li>더 높은 점수를 보고하는 대신 Codex의 지적을 OPEN으로 유지했습니다.</li>
<li>리뷰어 오류(hidden size가 2048 이상이라는 주장, 실제 896)를 잡고, 존재 확인에 실패한 추천 인용을 뺐습니다.</li>
<li>다룬 기술: LLM 에이전트, 멀티 에이전트 오케스트레이션, Claude Code, Codex CLI, Gemini API, MCP 서버, 평가·출처 로깅, LLM-as-a-judge 신뢰도.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<p><strong>个人项目，2026年9月。</strong>基于 Claude Code 的开源 <a href="https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep">ARIS</a> 技能包，主 agent 为 Claude Code。ARIS 将研究需求说明依次送入创意发散、评审打分、反驳论证（kill-argument）、改进和论文规划等阶段。</p>
<h2 class="star-h"><span class="star-tag">S</span>情境</h2>
<p>多智能体流程能快速产出研究创意和论文规划。但同一模型族评审自身产出时，评审结果容易被过度信任。</p>
<h2 class="star-h"><span class="star-tag">T</span>任务</h2>
<p>为流程提供明确的需求说明，并加入由其他模型族执行、独立且可追溯的评审环节。</p>
<h2 class="star-h"><span class="star-tag">A</span>行动</h2>
<ul>
<li>撰写包含 7 项要求的研究需求说明，作为每次智能体运行的唯一输入。</li>
<li>用 Python 搭建跨模型族评审框架：
<ul>
<li>Gemini API 评审者；API key 从 env 文件读取，而不写在代码中。</li>
<li>在 3 个模型间自动回退，并带退避重试。</li>
<li>来源记录头，标明实际作答的模型。</li>
</ul>
</li>
<li>加入 Codex CLI（只读）作为第二评审者，对照目标模型公开的代码核查论断。</li>
</ul>
<h2 class="star-h"><span class="star-tag">R</span>结果</h2>
<ul>
<li>来源记录头揭示了真实评审者：首选模型过载，第 1 份草稿由更轻量的回退 Gemini 模型评审。</li>
<li>flash 级 Gemini 评审者给两份草稿打出 8.2 和 7.63 分，结论均为 REVISE；对照公开代码核查的 Codex 给出 5.35 和 5.65 分（未达标）。</li>
<li>我没有报告更高的分数，而是把 Codex 的问题保持为 OPEN。</li>
<li>发现一处评审错误（声称 hidden size 至少为 2048，实际为 896），并删去一条无法核实其存在的推荐引用。</li>
<li>涉及技能：LLM 智能体、多智能体编排、Claude Code、Codex CLI、Gemini API、MCP 服务器、评估与来源记录、LLM-as-a-judge 可靠性。</li>
</ul>
</div>

</div>
