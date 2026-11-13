---
title: "CASTER CUT: Badminton Highlights with AI Caster Commentary"
excerpt: "Personal project (Oct.–Nov. 2026): a local web app that turns your own badminton video into a highlight clip with AI caster commentary and a broadcast-style game HUD. The shuttle track is checked against flight physics and each rally is rebuilt in court space, so the commentary can say where and how the shuttle was played.<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
excerpt_ko: "개인 프로젝트(2026년 10월 – 11월): 직접 찍은 배드민턴 영상을 AI 캐스터 해설과 중계 화면(HUD)이 들어간 하이라이트로 만드는 로컬 웹 앱. 셔틀 추적 점을 비행 물리로 검사하고 랠리를 코트 좌표에서 복원해, 어디로 어떻게 쳤는지를 해설합니다.<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
excerpt_zh: "个人项目（2026年10月–11月）：把自己拍摄的羽毛球视频做成带 AI 解说和转播式 HUD 的集锦的本地 Web 应用。用飞行物理检验羽毛球跟踪点，并在场地坐标中重建每个回合，让解说能说出球打向哪里、怎么打。<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
tldr_en:
  - "A phone video of an amateur badminton match goes in; a highlight MP4 with caster commentary, a game HUD and music comes out, all on a local web app."
  - "A vision-language model alone did not name shots reliably. So the pipeline tracks the shuttle (SAM 3.1 + frame motion + flight-physics checks; tracked frames 35% to 57% on 10 rallies) and rebuilds each rally in court space: who hit, from where to where, how high, how fast."
  - "The model reads that record as text, and a line names a shot only when the model's confidence is 0.7 or more: in the two runs after the threshold was set, 12 of 13 such answers matched my hand labels. Every render must pass 7 automatic checks. The numbers on this page are only the ones that were measured."
tldr_ko:
  - "휴대폰으로 찍은 동호인 배드민턴 영상을 넣으면, 캐스터 해설·경기 HUD·음악이 들어간 하이라이트 MP4가 로컬 웹 앱에서 만들어집니다."
  - "Vision-language model만으로는 샷 이름이 안정적으로 나오지 않았습니다. 그래서 셔틀을 추적하고(SAM 3.1 + 프레임 움직임 + 비행 물리 검사, 10개 랠리에서 추적 프레임 35%에서 57%로), 랠리를 코트 좌표에서 복원합니다. 누가, 어디서 어디로, 얼마나 높이, 얼마나 빠르게 쳤는지가 나옵니다."
  - "모델은 이 기록을 글로 읽고, 확신도가 0.7 이상일 때만 해설에서 샷 이름을 말합니다. 기준을 정한 뒤의 두 실행에서 그런 답 13개 중 12개가 직접 적은 정답과 같았습니다. 모든 렌더는 자동 검사 7가지를 통과해야 하며, 이 페이지의 숫자는 직접 잰 것만 적었습니다."
tldr_zh:
  - "输入一段手机拍摄的业余羽毛球比赛视频，本地 Web 应用输出带解说、比赛 HUD 和音乐的集锦 MP4。"
  - "仅靠 vision-language model 无法稳定判断击球类型。因此流程先跟踪羽毛球（SAM 3.1 + 帧间运动 + 飞行物理检验；10 个回合上跟踪到的帧从 35% 提高到 57%），再在场地坐标中重建每个回合：谁打的、从哪里打到哪里、多高、多快。"
  - "模型以文字形式读取这份记录，只有置信度达到 0.7 以上时，解说才说出击球类型：在确定阈值之后的两次运行中，这类回答 13 个里有 12 个与我的手工标注一致。每次渲染都必须通过 7 项自动检查。本页只写实际测量过的数字。"
collection: portfolio
order: 0.5
---

{% include tldr.html %}

<div class="proj-body">

<figure class="pvid pvid--single">
<video controls playsinline preload="metadata" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_promo_poster.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_promo.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Promo video (86 s, Korean, with sound)</span><span class="pvid__label lang-ko" lang="ko">홍보 영상 (86초, 한국어, 소리 있음)</span><span class="pvid__label lang-zh" lang="zh-Hans">宣传视频（86 秒，韩语，有声音）</span><span class="lang-en">The seven steps of the app, then a rally rendered by it from serve to finish: rally time and shot count, player name tags (placeholder names), court map, an estimated shuttle speed on hard smashes, slow replay and the caster's voice. The footage is my own; the players agreed to appear.</span><span class="lang-ko" lang="ko">앱의 일곱 단계와, 앱이 만든 랠리 장면(서브부터 끝까지)입니다. 랠리 시간과 타구 수, 선수 이름표(예시 이름), 코트 맵, 강한 스매시의 셔틀 속도(추정), 슬로 리플레이, 캐스터 음성이 들어갑니다. 직접 촬영한 영상이며 출연자의 동의를 받았습니다.</span><span class="lang-zh" lang="zh-Hans">应用的七个步骤，以及由它渲染的一个回合（从发球到结束）：回合时间与击球数、球员名牌（示例名字）、场地小地图、重扣时的球速（估计值）、慢动作回放和解说语音。视频为本人拍摄，出镜者已同意公开。</span></figcaption>
</figure>

<p class="pnote"><span class="lang-en">Personal project · Oct.–Nov. 2026 · Built for the Gyeonggi sports industry contest · Written with Claude Code as the coding agent: I set the requirements, made the design decisions and judged the results (listening tests for the voice, 43 hits labelled by hand).</span><span class="lang-ko" lang="ko">개인 프로젝트 · 2026년 10월 – 11월 · 경기스포츠산업 공모전 출품용으로 제작 · 코딩 에이전트로 Claude Code를 사용했고, 요구사항 정의와 설계 결정, 결과 판단(음성 청취 비교, 타구 43개 직접 라벨링)을 맡았습니다.</span><span class="lang-zh" lang="zh-Hans">个人项目 · 2026年10月–11月 · 为京畿体育产业大赛制作 · 使用 Claude Code 作为编码智能体：我负责需求、设计决策和结果判断（语音试听对比、手工标注 43 次击球）。</span></p>

<div class="kpis">
<div class="kpi"><span class="kpi__value">35% &rarr; 57%</span><span class="kpi__label lang-en">frames with the shuttle tracked in flight, 10 rallies (3,436 frames), previous tracker &rarr; physics-checked tracker</span><span class="kpi__label lang-ko" lang="ko">셔틀이 비행 중으로 추적된 프레임, 10개 랠리(3,436프레임), 이전 추적기 &rarr; 물리 검사 추적기</span><span class="kpi__label lang-zh" lang="zh-Hans">羽毛球在飞行中被跟踪到的帧，10 个回合（3,436 帧），旧跟踪器 &rarr; 物理检验跟踪器</span></div>
<div class="kpi"><span class="kpi__value">35 / 43</span><span class="kpi__label lang-en">hand-labelled hits found with the right hitting side (within 0.4 s), from the direction of each fitted flight; sound loudness and alternation gave 15</span><span class="kpi__label lang-ko" lang="ko">직접 라벨링한 타구 43개 중 친 팀까지 맞게 찾은 수(0.4초 이내). 맞춘 비행의 방향으로 판단하며, 소리 세기와 번갈아 치기만 쓸 때는 15개</span><span class="kpi__label lang-zh" lang="zh-Hans">手工标注的 43 次击球中，连击球方也判断正确的数量（0.4 秒以内）；依据拟合出的飞行方向判断，只用声音强度和轮流击球时为 15</span></div>
<div class="kpi"><span class="kpi__value">12 / 13</span><span class="kpi__label lang-en">shot answers with confidence 0.7 or more whose side and shot matched my labels, in the two runs after the threshold was set (one video, 3 rallies)</span><span class="kpi__label lang-ko" lang="ko">확신도 0.7 이상인 샷 판단 중 친 팀과 샷이 정답과 같은 수. 기준을 정한 뒤의 두 실행(영상 하나, 랠리 3개)</span><span class="kpi__label lang-zh" lang="zh-Hans">置信度 0.7 以上的击球判断中，击球方和类型都与标注一致的数量；确定阈值之后的两次运行（一段视频，3 个回合）</span></div>
<div class="kpi"><span class="kpi__value">7</span><span class="kpi__label lang-en">automatic checks on every render: format, duration, frames, black frames, voice onset, sync, speech fit</span><span class="kpi__label lang-ko" lang="ko">렌더마다 통과해야 하는 자동 검사: 형식, 길이, 프레임 수, 검은 화면, 음성 시작, 싱크, 말 길이</span><span class="kpi__label lang-zh" lang="zh-Hans">每次渲染的自动检查：格式、时长、帧数、黑帧、语音起点、同步、语音长度</span></div>
<div class="kpi"><span class="kpi__value">15 s · 30 s · 1 min · 3 min</span><span class="kpi__label lang-en">highlight lengths the user can pick</span><span class="kpi__label lang-ko" lang="ko">사용자가 고르는 하이라이트 길이</span><span class="kpi__label lang-zh" lang="zh-Hans">用户可选的集锦时长</span></div>
<div class="kpi"><span class="kpi__value">174 + 19</span><span class="kpi__label lang-en">automated tests (worker + API)</span><span class="kpi__label lang-ko" lang="ko">자동 테스트 (worker + API)</span><span class="kpi__label lang-zh" lang="zh-Hans">自动化测试（worker + API）</span></div>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">S</span>Situation</h2>
<p>Amateur players film their matches with a phone on a stand, but few edit them. Asked to name the shots in such a video, a frontier vision-language model gave different answers for the same clip: the shuttle is a few pixels wide and the far players are small.</p>
<h2 class="star-h"><span class="star-tag">T</span>Task</h2>
<p>Turn one fixed-camera video into a highlight clip with commentary that names the actual shot, and a game HUD. Run locally, and always finish with a video even when a stage fails.</p>
<h2 class="star-h"><span class="star-tag">A</span>Action</h2>
<ul>
<li>App: FastAPI + SQLite (WAL) job queue, one worker, each job in a child process; a web app in plain ES modules.</li>
<li>Rally finder: sharp high-frequency sounds combined with motion inside the court. Rallies are scored (strong finish 0.4, fast exchange 0.3, length 0.3), and a highlight is one whole rally from the serve.</li>
<li>Court: the camera is calibrated from the court lines without clicks (a background frame without players, a line map, a search over camera poses). The user's four corners are the fallback.</li>
<li>Shuttle tracker:
<ul>
<li>Candidates from SAM 3.1 (text prompt, then point-prompt propagation) and from frame-to-frame motion.</li>
<li>A size limit from the court calibration; a least-bending path picked by dynamic programming.</li>
<li>Each flight checked against a parabola fit and the direction gravity bends it.</li>
</ul>
</li>
<li>Rally reconstruction:
<ul>
<li>Each unbroken run of shuttle points is fitted as one flight in court space with gravity and drag (terminal speed 6.8 m/s).</li>
<li>A flight moving away from the camera was hit by the near pair, and the reverse. A change of direction is a hit.</li>
<li>Every hit gets one line of a record: hitting side, contact height, direction, speed range, peak height, where the shuttle was last seen, time to the next hit, and the situation (both pairs' formations, straight or cross, distance to the nearest receiver). Missing and unreliable values are marked as such.</li>
</ul>
</li>
<li>Commentary: one Claude call per video reads the record as text (no images). It judges every shot with a confidence, chooses the hits to speak at and writes the lines as JSON. Code keeps a shot name only beside a hit with confidence 0.7 or more, and drops lines that break the rules (scores or rulings without facts, banned phrases, a line that does not fit before the next one). ElevenLabs voices the lines.</li>
<li>HUD: drawn with Pillow. Name tags follow each person across rallies by shirt and shorts colour; an unclear match shows no name. A speed appears only at 100 km/h or more with a narrow measured range. SMASH! appears only on hits judged a smash with that confidence (without commentary: hits measured as overhead, flat and fast).</li>
<li>Fallbacks: no court found &rarr; the earlier still-frame path; no track &rarr; no trail; model failure &rarr; template script; voice failure &rarr; a local voice.</li>
<li>Render: one FFmpeg filter graph, then the 7 checks.</li>
</ul>
<h2 class="star-h"><span class="star-tag">R</span>Result</h2>
<ul>
<li>Tracked frames on 10 rallies: 31% with the text prompt alone, 35% with the previous tracker, 57% with the physics-checked tracker. Two rallies went from 0% to 55% and 79%.</li>
<li>Hits, against 43 hits I labelled by hand in 3 rallies: sound loudness and alternation found 28, 15 of them with the right side. Flight directions found 36, 32 with the right side; with player positions added to the fit, 35.</li>
<li>Shot names from the record alone: side and shot both matched the labels on 23 of 41 hits. Answers with confidence 0.7 or more matched 12 of 12 in the runs used to choose the threshold and 12 of 13 in the two runs after it. So a line names a shot only at 0.7 or more and describes the rest without a name.</li>
<li>In the current 30 s and 60 s demo videos, every spoken shot name that my labels cover is correct, and all 7 checks pass.</li>
<li>A second video (same hall, another camera position) ran end to end with the court found automatically. The rally finder split its rallies, though: 165 of 435 hit sounds were grouped and the longest rally was 7.5 s.</li>
<li>Things that did not work, and were dropped:
<ul>
<li>Filling gaps with predicted points: 6 of 13 checked points were wrong, so it is off.</li>
<li>Giving the model the flight in screen pixels: agreement over 3 runs was 14/24 without it, 19/24 with it, then 11/24 on a rerun. Not reproduced.</li>
<li>Reading the hitting side from sound loudness: loudness follows the shot, not the distance.</li>
<li>Finding untracked flights again in the motion points: a shuttle flying toward the camera barely moves on screen.</li>
<li>A km/h figure for every hit: without a tracked flight the range is too wide (one smash came out as 92 to 386 km/h), so a speed is shown only when the range is narrow.</li>
</ul>
</li>
<li>Limits: the labels come from one video, and all three labelled rallies were used for tuning, so no rally was held out. Fast shots are often not tracked right after contact. Commentary lines differ from run to run.</li>
<li>Next: finding rallies from motion on the court, checked on both videos.</li>
<li>Skills practised: FastAPI, SQLite job queue, FFmpeg, NumPy, video segmentation API (SAM 3.1), camera calibration from court lines, flight fitting with gravity and drag, LLM structured output and validation, TTS, pytest, Playwright.</li>
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
<li>랠리 찾기: 날카로운 고음역 소리와 코트 안 움직임을 함께 봅니다. 랠리에 점수를 매기고(강한 마무리 0.4, 빠른 주고받기 0.3, 길이 0.3), 하이라이트에는 랠리 하나를 서브부터 끝까지 통째로 담습니다.</li>
<li>코트: 클릭 없이 코트 선으로 카메라를 보정합니다(선수 없는 배경 화면, 선 지도, 카메라 자세 탐색). 못 찾으면 사용자가 찍은 네 모서리를 씁니다.</li>
<li>셔틀 추적기:
<ul>
<li>SAM 3.1(text prompt, 이어서 point prompt 전파)과 프레임 간 움직임에서 후보를 모읍니다.</li>
<li>코트 보정으로 크기 상한을 정하고, 동적 계획법으로 가장 덜 꺾이는 경로를 고릅니다.</li>
<li>비행 구간마다 포물선 맞춤과 중력이 휘는 방향으로 검사합니다.</li>
</ul>
</li>
<li>랠리 복원:
<ul>
<li>끊기지 않은 셔틀 점 묶음마다, 중력과 공기 저항(종단 속도 6.8m/s)으로 코트 좌표의 비행 하나를 맞춥니다.</li>
<li>카메라에서 멀어지는 비행은 가까운 쪽 팀이, 가까워지는 비행은 먼 쪽 팀이 친 것입니다. 방향이 바뀌는 곳이 타구입니다.</li>
<li>타구마다 기록 한 줄을 만듭니다. 친 팀, 맞은 높이, 방향, 속도 범위, 최고 높이, 셔틀이 마지막으로 보인 곳, 다음 타구까지의 시간, 그리고 상황(양 팀의 대형, 직선·대각, 가장 가까운 상대까지의 거리)이 들어갑니다. 못 잰 값과 믿기 어려운 값은 그렇게 표시합니다.</li>
</ul>
</li>
<li>해설: 영상 하나당 Claude 호출 1번으로 이 기록을 글로 읽습니다(그림은 보내지 않습니다). 타구마다 샷과 확신도를 판단하고, 말할 타구를 고르고, 해설 줄을 JSON으로 씁니다. 코드는 확신도 0.7 이상인 타구 옆에서만 샷 이름을 남기고, 규칙을 어긴 줄을 뺍니다(사실 없는 점수·판정, 금지 표현, 다음 줄 전에 끝나지 않는 줄). ElevenLabs가 음성을 만듭니다.</li>
<li>HUD: Pillow로 그립니다. 이름표는 상의·하의 색으로 랠리가 바뀌어도 같은 사람을 따라가고, 구분이 분명하지 않으면 이름을 표시하지 않습니다. 속도는 100km/h 이상이고 측정 범위가 좁을 때만 표시합니다. SMASH!는 그 확신도로 스매시라고 판단한 타구에만 뜹니다(해설을 만들지 않을 때는 머리 위에서 평평하고 빠르게 재진 타구).</li>
<li>실패 대비: 코트를 못 찾음 &rarr; 예전의 정지 화면 방식, 추적 실패 &rarr; 궤적 없이 진행, 모델 실패 &rarr; 템플릿 원고, 음성 실패 &rarr; 로컬 음성.</li>
<li>렌더: FFmpeg filter graph 하나로 하고, 끝나면 7가지를 검사합니다.</li>
</ul>
<h2 class="star-h"><span class="star-tag">R</span>결과</h2>
<ul>
<li>10개 랠리의 추적 프레임: text prompt만 쓰면 31%, 이전 추적기 35%, 물리 검사 추적기 57%. 두 랠리는 0%에서 55%, 79%가 됐습니다.</li>
<li>타구 찾기(랠리 3개에서 직접 라벨링한 43타 기준): 소리 세기와 번갈아 치기로는 28타를 찾고 그중 15타가 친 팀까지 맞았습니다. 비행 방향으로는 36타를 찾고 32타가 팀까지 맞았으며, 비행 맞춤에 선수 위치를 넣은 뒤 35타가 됐습니다.</li>
<li>기록만 보고 판단한 샷 이름: 41타 중 23타에서 친 팀과 샷이 모두 정답과 같았습니다. 확신도 0.7 이상인 답은 기준을 정하는 데 쓴 실행들에서 12개 중 12개, 그 뒤의 두 실행에서 13개 중 12개가 맞았습니다. 그래서 0.7 이상일 때만 샷 이름을 말하고, 나머지는 이름 없이 설명합니다.</li>
<li>지금의 30초·60초 시연 영상에서는, 정답표로 확인할 수 있는 샷 이름이 모두 맞고 자동 검사 7가지를 모두 통과합니다.</li>
<li>두 번째 영상(같은 체육관, 다른 카메라 위치)도 코트를 자동으로 찾아 끝까지 만들어졌습니다. 다만 랠리 찾기가 랠리를 잘게 쪼갰습니다. 타구음 435개 중 165개만 랠리로 묶였고 가장 긴 랠리가 7.5초였습니다.</li>
<li>해 봤지만 안 돼서 뺀 것:
<ul>
<li>빈칸을 예측 점으로 채우기: 확인한 13개 중 6개가 틀려 껐습니다.</li>
<li>비행을 화면 픽셀 값으로 모델에 주기: 3회 반복의 일치가 없을 때 14/24, 있을 때 19/24였다가 재실행에서 11/24. 재현되지 않았습니다.</li>
<li>소리 크기로 친 팀 정하기: 소리 크기는 거리가 아니라 샷의 세기를 따릅니다.</li>
<li>추적 안 된 비행을 움직임 점에서 다시 찾기: 카메라 쪽으로 날아오는 셔틀은 화면에서 거의 움직이지 않습니다.</li>
<li>모든 타구에 km/h 숫자 붙이기: 비행이 추적되지 않으면 범위가 너무 넓어서(한 스매시가 92~386km/h), 범위가 좁을 때만 속도를 표시합니다.</li>
</ul>
</li>
<li>한계: 정답 라벨은 영상 하나에서 나왔고, 라벨링한 랠리 3개를 모두 조정에 써서 따로 남겨 둔 확인용 랠리가 없습니다. 빠른 샷은 타구 직후가 추적되지 않는 경우가 많습니다. 해설 줄은 실행할 때마다 달라집니다.</li>
<li>다음: 코트 안 움직임으로 랠리를 찾는 방식으로 바꾸고, 두 영상에서 확인합니다.</li>
<li>다룬 기술: FastAPI, SQLite 작업 큐, FFmpeg, NumPy, 영상 분할 API(SAM 3.1), 코트 선 기반 카메라 보정, 중력·공기 저항 비행 맞춤, LLM structured output과 검증, TTS, pytest, Playwright.</li>
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
<li>回合检测：结合尖锐的高频声音与场地内的运动。给回合打分（强力收尾 0.4、快速对攻 0.3、长度 0.3），集锦收入从发球到结束的完整回合。</li>
<li>场地：无需点击，用场地线标定相机（无球员的背景画面、线条图、相机姿态搜索）。找不到时使用用户点选的四个角。</li>
<li>羽毛球跟踪器：
<ul>
<li>候选点来自 SAM 3.1（text prompt，再做 point prompt 传播）和帧间运动。</li>
<li>用场地标定确定尺寸上限；用动态规划选出弯折最小的路径。</li>
<li>每段飞行都用抛物线拟合和重力弯曲方向来检验。</li>
</ul>
</li>
<li>回合重建：
<ul>
<li>对每一段连续的跟踪点，用重力和空气阻力（终端速度 6.8 m/s）在场地坐标中拟合出一段飞行。</li>
<li>远离相机的飞行是近端一方打出的，靠近相机的飞行是远端一方打出的。方向改变之处就是一次击球。</li>
<li>每次击球生成一行记录：击球方、击球高度、方向、速度范围、最高高度、羽毛球最后出现的位置、到下一次击球的时间，以及局面（双方站位、直线或斜线、离最近接球者的距离）。未测到和不可靠的数值会照实标明。</li>
</ul>
</li>
<li>解说：每段视频调用一次 Claude，以文字读取这份记录（不发送图像）。它判断每次击球的类型和置信度，选出要解说的击球，并以 JSON 写出解说句。代码只在置信度 0.7 以上的击球旁保留击球类型的名称，并剔除违反规则的句子（没有事实依据的比分或判罚、禁用表达、在下一句之前说不完的句子）。ElevenLabs 生成语音。</li>
<li>HUD：用 Pillow 绘制。名牌依据上衣和短裤的颜色，在不同回合中跟随同一个人；区分不清时不显示名字。只有速度达到 100 km/h 且测量范围较窄时才显示球速。SMASH! 只出现在以该置信度判断为扣杀的击球上（不生成解说时：测得为头顶、平直且快速的击球）。</li>
<li>回退：找不到场地 &rarr; 使用原先的静帧方式；跟踪失败 &rarr; 不画轨迹；模型失败 &rarr; 模板稿；语音失败 &rarr; 本地语音。</li>
<li>渲染：使用单个 FFmpeg filter graph，之后做 7 项检查。</li>
</ul>
<h2 class="star-h"><span class="star-tag">R</span>结果</h2>
<ul>
<li>10 个回合上跟踪到的帧：仅用 text prompt 为 31%，旧跟踪器 35%，物理检验跟踪器 57%。其中两个回合从 0% 变为 55% 和 79%。</li>
<li>击球检测（以我在 3 个回合中手工标注的 43 次击球为准）：用声音强度和轮流击球找到 28 次，其中 15 次击球方正确。用飞行方向找到 36 次，32 次击球方正确；在拟合中加入球员位置后为 35 次。</li>
<li>仅凭记录判断击球类型：41 次击球中有 23 次的击球方和类型都与标注一致。置信度 0.7 以上的回答，在用来确定阈值的几次运行中 12 个全部正确，在之后的两次运行中 13 个里有 12 个正确。因此只有达到 0.7 时才说出击球类型，其余只做描述、不说名称。</li>
<li>在目前的 30 秒和 60 秒演示视频中，凡是标注能够核对的击球类型名称全部正确，7 项自动检查全部通过。</li>
<li>第二段视频（同一场馆、不同机位）也能自动找到场地并完整生成。但回合检测把回合切碎了：435 个击球声中只有 165 个被归入回合，最长的回合为 7.5 秒。</li>
<li>试过但无效、已去掉的做法：
<ul>
<li>用预测点填补空缺：检查的 13 个点中有 6 个是错的，因此关闭。</li>
<li>把飞行以屏幕像素值交给模型：3 次运行的一致数在不加时为 14/24，加上后为 19/24，重跑为 11/24。未能复现。</li>
<li>用声音大小判断击球方：声音大小取决于击球力度，而不是距离。</li>
<li>在运动点中重新寻找未跟踪到的飞行：朝相机飞来的羽毛球在画面上几乎不动。</li>
<li>给每次击球都标 km/h：飞行未被跟踪时范围太宽（一次扣杀算出 92 到 386 km/h），所以只在范围较窄时显示球速。</li>
</ul>
</li>
<li>局限：标注只来自一段视频，而且标注过的 3 个回合都用于调参，没有留出验证用的回合。快速击球在击球后的瞬间常常跟踪不到。解说句每次运行都会不同。</li>
<li>下一步：改为依据场地内的运动来检测回合，并在两段视频上验证。</li>
<li>涉及技术：FastAPI、SQLite 任务队列、FFmpeg、NumPy、视频分割 API（SAM 3.1）、基于场地线的相机标定、含重力与空气阻力的飞行拟合、LLM structured output 与校验、TTS、pytest、Playwright。</li>
</ul>
</div>

</div>
