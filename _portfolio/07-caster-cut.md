---
title: "CASTER CUT: Badminton Highlights with AI Caster Commentary"
excerpt: "Personal project (Oct.–Nov. 2026): a local web app that turns your own badminton video into a highlight clip with AI caster commentary and a broadcast-style game HUD. A shuttle tracker that checks every point against flight physics.<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
excerpt_ko: "개인 프로젝트(2026년 10월 – 11월): 직접 찍은 배드민턴 영상을 AI 캐스터 해설과 중계 화면(HUD)이 들어간 하이라이트로 만드는 로컬 웹 앱. 셔틀 추적 점을 비행 물리로 검사하는 추적기.<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
excerpt_zh: "个人项目（2026年10月–11月）：把自己拍摄的羽毛球视频做成带 AI 解说和转播式 HUD 的集锦的本地 Web 应用。用飞行物理检验每个跟踪点的羽毛球跟踪器。<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
tldr_en:
  - "A phone video of an amateur badminton match goes in; a highlight MP4 with caster commentary, a game HUD and music comes out, all on a local web app."
  - "A vision-language model alone did not name shots reliably, so the pipeline tracks the shuttle (SAM 3.1 + frame motion + flight-physics checks). Tracked frames rose from 35% to 57% on 10 rallies."
  - "Every stage has a fallback, and every render must pass 7 automatic checks. The numbers on this page are only the ones that were measured."
tldr_ko:
  - "휴대폰으로 찍은 동호인 배드민턴 영상을 넣으면, 캐스터 해설·경기 HUD·음악이 들어간 하이라이트 MP4가 로컬 웹 앱에서 만들어집니다."
  - "Vision-language model만으로는 샷 이름이 안정적으로 나오지 않아, 셔틀을 추적하는 단계(SAM 3.1 + 프레임 움직임 + 비행 물리 검사)를 넣었습니다. 10개 랠리에서 추적 프레임이 35%에서 57%로 올랐습니다."
  - "모든 단계에 실패 대비 경로가 있고, 모든 렌더는 자동 검사 7가지를 통과해야 합니다. 이 페이지의 숫자는 직접 잰 것만 적었습니다."
tldr_zh:
  - "输入一段手机拍摄的业余羽毛球比赛视频，本地 Web 应用输出带解说、比赛 HUD 和音乐的集锦 MP4。"
  - "仅靠 vision-language model 无法稳定判断击球类型，因此加入了羽毛球跟踪（SAM 3.1 + 帧间运动 + 飞行物理检验）。在 10 个回合上，跟踪到的帧从 35% 提高到 57%。"
  - "每个阶段都有回退路径，每次渲染都必须通过 7 项自动检查。本页只写实际测量过的数字。"
collection: portfolio
order: 3.5
---

{% include tldr.html %}

<div class="proj-body">

<figure class="pvid pvid--single">
<video controls playsinline preload="metadata" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_promo_poster.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_promo.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Promo video (84 s, Korean, with sound)</span><span class="pvid__label lang-ko" lang="ko">홍보 영상 (84초, 한국어, 소리 있음)</span><span class="pvid__label lang-zh" lang="zh-Hans">宣传视频（84 秒，韩语，有声音）</span><span class="lang-en">The six steps of the app, then a rally rendered by it: rally time and shot count, player rings, court map, slow replay and the caster's voice. The footage is my own; the players agreed to appear.</span><span class="lang-ko" lang="ko">앱의 여섯 단계와, 앱이 만든 랠리 장면입니다. 랠리 시간과 타구 수, 선수 표시, 코트 맵, 슬로 리플레이, 캐스터 음성이 들어갑니다. 직접 촬영한 영상이며 출연자의 동의를 받았습니다.</span><span class="lang-zh" lang="zh-Hans">应用的六个步骤，以及由它渲染的一个回合：回合时间与击球数、球员标记、场地小地图、慢动作回放和解说语音。视频为本人拍摄，出镜者已同意公开。</span></figcaption>
</figure>

<p class="pnote"><span class="lang-en">Personal project · Oct.–Nov. 2026 · Built for the Gyeonggi sports industry contest · Written with Claude Code as the coding agent: I set the requirements, made the design decisions and judged the results (listening tests for the voice, 43 hits labelled by hand).</span><span class="lang-ko" lang="ko">개인 프로젝트 · 2026년 10월 – 11월 · 경기스포츠산업 공모전 출품용으로 제작 · 코딩 에이전트로 Claude Code를 사용했고, 요구사항 정의와 설계 결정, 결과 판단(음성 청취 비교, 타구 43개 직접 라벨링)을 맡았습니다.</span><span class="lang-zh" lang="zh-Hans">个人项目 · 2026年10月–11月 · 为京畿体育产业大赛制作 · 使用 Claude Code 作为编码智能体：我负责需求、设计决策和结果判断（语音试听对比、手工标注 43 次击球）。</span></p>

<div class="kpis">
<div class="kpi"><span class="kpi__value">35% &rarr; 57%</span><span class="kpi__label lang-en">frames with the shuttle tracked in flight, 10 rallies (3,436 frames), previous tracker &rarr; physics-checked tracker</span><span class="kpi__label lang-ko" lang="ko">셔틀이 비행 중으로 추적된 프레임, 10개 랠리(3,436프레임), 이전 추적기 &rarr; 물리 검사 추적기</span><span class="kpi__label lang-zh" lang="zh-Hans">羽毛球在飞行中被跟踪到的帧，10 个回合（3,436 帧），旧跟踪器 &rarr; 物理检验跟踪器</span></div>
<div class="kpi"><span class="kpi__value">7</span><span class="kpi__label lang-en">automatic checks on every render: format, duration, frames, black frames, voice onset, sync, speech fit</span><span class="kpi__label lang-ko" lang="ko">렌더마다 통과해야 하는 자동 검사: 형식, 길이, 프레임 수, 검은 화면, 음성 시작, 싱크, 말 길이</span><span class="kpi__label lang-zh" lang="zh-Hans">每次渲染的自动检查：格式、时长、帧数、黑帧、语音起点、同步、语音长度</span></div>
<div class="kpi"><span class="kpi__value">15 s · 30 s · 1 min · 3 min</span><span class="kpi__label lang-en">highlight lengths the user can pick</span><span class="kpi__label lang-ko" lang="ko">사용자가 고르는 하이라이트 길이</span><span class="kpi__label lang-zh" lang="zh-Hans">用户可选的集锦时长</span></div>
<div class="kpi"><span class="kpi__value">136 + 18</span><span class="kpi__label lang-en">automated tests (worker + API)</span><span class="kpi__label lang-ko" lang="ko">자동 테스트 (worker + API)</span><span class="kpi__label lang-zh" lang="zh-Hans">自动化测试（worker + API）</span></div>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">S</span>Situation</h2>
<p>Amateur players film their matches with a phone on a stand, but few edit them. Asked to name the shots in such a video, a frontier vision-language model gave different answers for the same clip: the shuttle is a few pixels wide and the far players are small.</p>
<h2 class="star-h"><span class="star-tag">T</span>Task</h2>
<p>Turn one fixed-camera video into a highlight clip with commentary that names the actual shot, and a game HUD. Run locally, and always finish with a video even when a stage fails.</p>
<h2 class="star-h"><span class="star-tag">A</span>Action</h2>
<ul>
<li>App: FastAPI + SQLite (WAL) job queue, one worker, each job in a child process; a web app in plain ES modules.</li>
<li>Rally finder: sharp high-frequency sounds combined with motion inside the court.</li>
<li>Shuttle tracker:
<ul>
<li>Candidates from SAM 3.1 (text prompt, then point-prompt propagation) and from frame-to-frame motion.</li>
<li>A size limit from the court calibration; a least-bending path picked by dynamic programming.</li>
<li>Each flight checked against a parabola fit and the direction gravity bends it.</li>
</ul>
</li>
<li>Commentary: Claude reads the hit frames and writes lines as JSON. Code rejects lines that break the rules (a shot name needs evidence; no scores or rulings without facts). ElevenLabs voices the lines.</li>
<li>Fallbacks: no track &rarr; no trail; model failure &rarr; template script; voice failure &rarr; a local voice.</li>
<li>HUD drawn with Pillow; one FFmpeg filter graph for the render; 7 checks after it.</li>
</ul>
<h2 class="star-h"><span class="star-tag">R</span>Result</h2>
<ul>
<li>Tracked frames on 10 rallies: 31% with the text prompt alone, 35% with the previous tracker, 57% with the physics-checked tracker. Two rallies went from 0% to 55% and 79%.</li>
<li>Things that did not work, and were dropped:
<ul>
<li>Filling gaps with predicted points: 6 of 13 checked points were wrong, so it is off.</li>
<li>Giving the model the flight in screen pixels: agreement over 3 runs was 14/24 without it, 19/24 with it, then 11/24 on a rerun. Not reproduced.</li>
</ul>
</li>
<li>Limits: one camera, one hall, one video for the measurements. Shot naming still fails on many hits.</li>
<li>Ongoing: rebuilding each rally in court space (a camera calibrated from the court lines, flights fitted with gravity and drag) so the model gets where and how fast the shuttle flew, not pixels.</li>
<li>Skills practised: FastAPI, SQLite job queue, FFmpeg, NumPy, video segmentation API (SAM 3.1), LLM structured output and validation, TTS, pytest, Playwright.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">S</span>상황</h2>
<p>동호인들은 휴대폰을 거치대에 세워 경기를 찍지만 편집까지 하는 경우는 드뭅니다. 이런 영상에서 샷 이름을 물으면 최신 vision-language model도 같은 장면에 다른 답을 냈습니다. 셔틀은 몇 픽셀이고 먼 쪽 선수는 작게 찍히기 때문입니다.</p>
<h2 class="star-h"><span class="star-tag">T</span>과제</h2>
<p>고정 카메라 영상 하나를, 실제 샷 이름을 말하는 해설과 경기 HUD가 들어간 하이라이트로 만듭니다. 로컬에서 실행하고, 어느 단계가 실패해도 영상은 끝까지 나오게 합니다.</p>
<h2 class="star-h"><span class="star-tag">A</span>행동</h2>
<ul>
<li>앱: FastAPI + SQLite(WAL) 작업 큐, worker 1개, 작업마다 자식 프로세스. 웹은 순수 ES modules.</li>
<li>랠리 찾기: 날카로운 고음역 소리와 코트 안 움직임을 함께 봅니다.</li>
<li>셔틀 추적기:
<ul>
<li>SAM 3.1(text prompt, 이어서 point prompt 전파)과 프레임 간 움직임에서 후보를 모읍니다.</li>
<li>코트 보정으로 크기 상한을 정하고, 동적 계획법으로 가장 덜 꺾이는 경로를 고릅니다.</li>
<li>비행 구간마다 포물선 맞춤과 중력이 휘는 방향으로 검사합니다.</li>
</ul>
</li>
<li>해설: Claude가 타구 장면을 보고 해설 줄을 JSON으로 씁니다. 규칙을 어긴 줄은 코드가 걸러냅니다(샷 이름에는 근거가 필요하고, 사실 없이 점수·판정을 말할 수 없습니다). ElevenLabs가 음성을 만듭니다.</li>
<li>실패 대비: 추적 실패 &rarr; 궤적 없이 진행, 모델 실패 &rarr; 템플릿 원고, 음성 실패 &rarr; 로컬 음성.</li>
<li>HUD는 Pillow로 그리고, 렌더는 FFmpeg filter graph 하나로 하며, 끝나면 7가지를 검사합니다.</li>
</ul>
<h2 class="star-h"><span class="star-tag">R</span>결과</h2>
<ul>
<li>10개 랠리의 추적 프레임: text prompt만 쓰면 31%, 이전 추적기 35%, 물리 검사 추적기 57%. 두 랠리는 0%에서 55%, 79%가 됐습니다.</li>
<li>해 봤지만 안 돼서 뺀 것:
<ul>
<li>빈칸을 예측 점으로 채우기: 확인한 13개 중 6개가 틀려 껐습니다.</li>
<li>비행을 화면 픽셀 값으로 모델에 주기: 3회 반복의 일치가 없을 때 14/24, 있을 때 19/24였다가 재실행에서 11/24. 재현되지 않았습니다.</li>
</ul>
</li>
<li>한계: 측정은 카메라 한 대, 체육관 한 곳, 영상 하나에서 했습니다. 샷 이름은 아직 많은 타구에서 틀립니다.</li>
<li>진행 중: 랠리를 코트 좌표에서 복원하는 작업(코트 선으로 카메라를 보정하고, 중력과 공기 저항으로 비행을 맞춤). 모델에 픽셀이 아니라 셔틀이 어디로 얼마나 빠르게 갔는지를 주기 위해서입니다.</li>
<li>다룬 기술: FastAPI, SQLite 작업 큐, FFmpeg, NumPy, 영상 분할 API(SAM 3.1), LLM structured output과 검증, TTS, pytest, Playwright.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">S</span>背景</h2>
<p>业余选手常把手机架在支架上拍比赛，但很少有人剪辑。让最新的 vision-language model 判断这类视频中的击球类型时，同一段画面会得到不同答案：羽毛球只有几个像素，远端球员也很小。</p>
<h2 class="star-h"><span class="star-tag">T</span>任务</h2>
<p>把一段固定机位的视频做成集锦，解说要说出真实的击球类型，并带比赛 HUD。在本地运行；即使某个阶段失败，也要输出视频。</p>
<h2 class="star-h"><span class="star-tag">A</span>行动</h2>
<ul>
<li>应用：FastAPI + SQLite（WAL）任务队列，1 个 worker，每个任务一个子进程；前端为原生 ES modules。</li>
<li>回合检测：结合尖锐的高频声音与场地内的运动。</li>
<li>羽毛球跟踪器：
<ul>
<li>候选点来自 SAM 3.1（text prompt，再做 point prompt 传播）和帧间运动。</li>
<li>用场地标定确定尺寸上限；用动态规划选出弯折最小的路径。</li>
<li>每段飞行都用抛物线拟合和重力弯曲方向来检验。</li>
</ul>
</li>
<li>解说：Claude 看击球画面并以 JSON 写出解说句。违反规则的句子由代码剔除（说出击球类型需要依据；没有事实不能说比分或判罚）。ElevenLabs 生成语音。</li>
<li>回退：跟踪失败 &rarr; 不画轨迹；模型失败 &rarr; 模板稿；语音失败 &rarr; 本地语音。</li>
<li>HUD 用 Pillow 绘制；渲染使用单个 FFmpeg filter graph；之后做 7 项检查。</li>
</ul>
<h2 class="star-h"><span class="star-tag">R</span>结果</h2>
<ul>
<li>10 个回合上跟踪到的帧：仅用 text prompt 为 31%，旧跟踪器 35%，物理检验跟踪器 57%。其中两个回合从 0% 变为 55% 和 79%。</li>
<li>试过但无效、已去掉的做法：
<ul>
<li>用预测点填补空缺：检查的 13 个点中有 6 个是错的，因此关闭。</li>
<li>把飞行以屏幕像素值交给模型：3 次运行的一致数在不加时为 14/24，加上后为 19/24，重跑为 11/24。未能复现。</li>
</ul>
</li>
<li>局限：测量只来自一台相机、一个场馆、一段视频。击球类型在很多击球上仍然判断错误。</li>
<li>进行中：在场地坐标中重建每个回合（用场地线标定相机，用重力和空气阻力拟合飞行），让模型得到羽毛球飞向哪里、多快，而不是像素。</li>
<li>涉及技术：FastAPI、SQLite 任务队列、FFmpeg、NumPy、视频分割 API（SAM 3.1）、LLM structured output 与校验、TTS、pytest、Playwright。</li>
</ul>
</div>

</div>
