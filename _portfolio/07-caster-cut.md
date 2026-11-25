---
title: "CASTER CUT: Badminton Highlights with AI Caster Commentary"
excerpt: "Personal project (Oct.–Nov. 2026): a local web app that turns your own badminton video into a highlight clip with AI caster commentary and a broadcast-style game HUD. Tracks the shuttle and the players and rebuilds each rally in court space, so the commentary can say where and how the shuttle was played.<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
excerpt_ko: "개인 프로젝트(2026년 10월 – 11월): 직접 찍은 배드민턴 영상을 AI 캐스터 해설과 중계 화면(HUD)이 들어간 하이라이트로 만드는 로컬 웹 앱. 셔틀과 선수를 추적하고 랠리를 코트 좌표에서 복원해, 어디로 어떻게 쳤는지를 해설.<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
excerpt_zh: "个人项目（2026年10月–11月）：把自己拍摄的羽毛球视频做成带 AI 解说和转播式 HUD 的集锦的本地 Web 应用。跟踪羽毛球和球员，在场地坐标中重建每个回合，让解说能说出球打向哪里、怎么打。<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
tldr_en:
  - "Phone video of a badminton match in, highlight MP4 with caster commentary, game HUD and music out (web app)."
  - "A vision-language model looking at the video alone did not name shots reliably, so the shuttle and players are tracked and each rally is rebuilt in court space."
  - "The commentary model reads that record and names a shot only when the confidence of the shot judgement is 0.7 or more."
tldr_ko:
  - "휴대폰으로 찍은 배드민턴 영상 → 캐스터 해설·경기 HUD·음악이 들어간 하이라이트 MP4를 만드는 웹 앱"
  - "영상만 본 vision-language model은 샷 이름을 안정적으로 맞히지 못함 → 셔틀·선수를 추적하고 랠리를 코트 좌표에서 복원"
  - "해설 모델은 복원한 기록을 읽고, 샷 판단의 확신도가 0.7 이상일 때만 샷 이름 사용"
tldr_zh:
  - "输入手机拍摄的羽毛球比赛视频，输出带解说、比赛 HUD 和音乐的集锦 MP4（Web 应用）"
  - "只看视频的 vision-language model 无法稳定判断击球类型 → 跟踪羽毛球和球员，在场地坐标中重建每个回合"
  - "解说模型读取重建记录，仅在击球判断的置信度达到 0.7 以上时说出击球类型"
collection: portfolio
order: 0.5
---

{% include tldr.html %}

<div class="proj-body">

<figure class="pvid pvid--single">
<video controls playsinline preload="metadata" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_promo_poster.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_promo.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Promo video (86 s, Korean, with sound)</span><span class="pvid__label lang-ko" lang="ko">홍보 영상 (86초, 한국어, 소리 있음)</span><span class="pvid__label lang-zh" lang="zh-Hans">宣传视频（86 秒，韩语，有声音）</span><span class="lang-en">Rally time and shot count, player name tags (placeholder names), court map, smash speed, slow replay, caster voice. Own footage; the players agreed to appear.</span><span class="lang-ko" lang="ko">랠리 시간·타구 수, 선수 이름표(예시 이름), 코트 맵, 스매시 속도, 슬로 리플레이, 캐스터 음성 포함. 직접 촬영, 출연자 동의 받음.</span><span class="lang-zh" lang="zh-Hans">回合时间与击球数、球员名牌（示例名字）、场地小地图、扣杀球速、慢动作回放、解说语音。本人拍摄，出镜者已同意公开。</span></figcaption>
</figure>

<p class="pnote"><span class="lang-en">Personal project · Oct.–Nov. 2026 · Coding agent: Claude Code · My part: requirements, design decisions, judging the results</span><span class="lang-ko" lang="ko">개인 프로젝트 · 2026년 10월 – 11월 · 코딩 에이전트로 Claude Code 사용 · 요구사항 정의, 설계 결정, 결과 판단 담당</span><span class="lang-zh" lang="zh-Hans">个人项目 · 2026年10月–11月 · 编码智能体：Claude Code · 本人负责需求、设计决策和结果判断</span></p>

<h2 class="star-h"><span class="star-tag">S</span><span class="lang-en">Situation</span><span class="lang-ko" lang="ko">상황</span><span class="lang-zh" lang="zh-Hans">背景</span></h2>
<ul>
<li><span class="lang-en">A vision-language model shown only the video does not name badminton shots reliably (the shuttle is a few pixels wide and fast)</span><span class="lang-ko" lang="ko">영상만 본 vision-language model은 배드민턴 샷 이름을 안정적으로 맞히지 못함 (셔틀이 몇 픽셀 크기이고 빠름)</span><span class="lang-zh" lang="zh-Hans">只看视频的 vision-language model 无法稳定判断羽毛球的击球类型（羽毛球只有几个像素，而且很快）</span></li>
</ul>

<h2 class="star-h"><span class="star-tag">T</span><span class="lang-en">Task</span><span class="lang-ko" lang="ko">과제</span><span class="lang-zh" lang="zh-Hans">任务</span></h2>
<ul>
<li><span class="lang-en">One fixed-camera match video → a highlight with caster commentary that names the real shot, and a broadcast-style HUD</span><span class="lang-ko" lang="ko">고정 카메라 경기 영상 하나 → 실제 샷을 말하는 캐스터 해설과 중계 화면(HUD)이 들어간 하이라이트</span><span class="lang-zh" lang="zh-Hans">一段固定机位的比赛视频 → 解说能说出真实击球类型、并带转播式 HUD 的集锦</span></li>
</ul>

<h2 class="star-h"><span class="star-tag">A</span><span class="lang-en">Action</span><span class="lang-ko" lang="ko">행동</span><span class="lang-zh" lang="zh-Hans">行动</span></h2>

<figure class="pfig">
<img class="lang-en" style="width:100%;max-width:640px" src="{{ site.baseurl }}/images/projects/caster_cut_pipeline_en.png" alt="CASTER CUT pipeline: highlight selection, court calibration, shuttle and player tracking with SAM 3.1, rally reconstruction, commentary with Claude and ElevenLabs, HUD and render" loading="lazy">
<img class="lang-ko" lang="ko" style="width:100%;max-width:640px" src="{{ site.baseurl }}/images/projects/caster_cut_pipeline_ko.png" alt="CASTER CUT 파이프라인: 하이라이트 선정, 코트 보정, 셔틀·선수 추적(SAM 3.1), 랠리 복원, 해설(Claude, ElevenLabs), 중계 화면과 렌더" loading="lazy">
<img class="lang-zh" lang="zh-Hans" style="width:100%;max-width:640px" src="{{ site.baseurl }}/images/projects/caster_cut_pipeline_zh.png" alt="CASTER CUT 流程：集锦选取、场地标定、羽毛球与球员跟踪（SAM 3.1）、回合重建、解说（Claude、ElevenLabs）、转播画面与渲染" loading="lazy">
<figcaption><span class="lang-en">The pipeline, six stages. A logo marks a stage that calls an external AI model; the others are the project's own algorithms. The small drawings are schematic. Logos are trademarks of their owners.</span><span class="lang-ko" lang="ko">전체 파이프라인(여섯 단계). 로고가 붙은 단계는 외부 AI 모델 사용, 나머지는 직접 구현한 알고리즘. 작은 그림은 개념도. 로고는 각 회사의 상표.</span><span class="lang-zh" lang="zh-Hans">整体流程（六个阶段）。带标志的阶段调用外部 AI 模型，其余为项目自身实现的算法。小图为示意图。各标志为其所有者的商标。</span></figcaption>
</figure>

<h3>1. <span class="lang-en">Highlight selection</span><span class="lang-ko" lang="ko">하이라이트 선정</span><span class="lang-zh" lang="zh-Hans">集锦选取</span></h3>
<ul>
<li><span class="lang-en">Rallies found from hit sounds and motion inside the court</span><span class="lang-ko" lang="ko">타구음과 코트 안 움직임으로 랠리 찾음</span><span class="lang-zh" lang="zh-Hans">用击球声和场地内的运动找出回合</span></li>
<li><span class="lang-en">Scored by strong finish (0.4), fast exchanges (0.3), length (0.3) → the best rallies go in whole, from the serve to the last shot</span><span class="lang-ko" lang="ko">강한 마무리(0.4) · 빠른 주고받기(0.3) · 길이(0.3)로 점수 → 점수 높은 랠리를 서브부터 마지막 타구까지 담음</span><span class="lang-zh" lang="zh-Hans">按强力收尾（0.4）、快速对攻（0.3）、长度（0.3）打分 → 得分高的回合从发球到最后一拍完整收入</span></li>
</ul>
<figure class="pvid pvid--single">
<video controls playsinline preload="none" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_step1_rally.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_step1_rally.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Stage 1 (42 s, with sound)</span><span class="pvid__label lang-ko" lang="ko">1단계 영상 (42초, 소리 있음)</span><span class="pvid__label lang-zh" lang="zh-Hans">阶段 1（42 秒，有声音）</span><span class="lang-en">Hit sound strength, motion inside the court, the rallies found and their scores. On-screen labels are in Korean.</span><span class="lang-ko" lang="ko">타구음 세기, 코트 안 움직임, 찾은 랠리와 점수</span><span class="lang-zh" lang="zh-Hans">击球声强度、场地内的运动、找到的回合及其得分。画面中的文字为韩语。</span></figcaption>
</figure>

<h3>2. <span class="lang-en">Court calibration</span><span class="lang-ko" lang="ko">코트 보정</span><span class="lang-zh" lang="zh-Hans">场地标定</span></h3>
<ul>
<li><span class="lang-en">White lines found in a background without players; a court model fitted to them gives the camera position and angle</span><span class="lang-ko" lang="ko">선수가 없는 배경에서 흰 선을 찾고, 코트 규격 모델을 맞춰 카메라 위치·각도 계산</span><span class="lang-zh" lang="zh-Hans">在去掉球员的背景中找出白线，把场地模型与之拟合，得到相机的位置和角度</span></li>
<li><span class="lang-en">Screen pixels → metres on the court, automatic (no clicks)</span><span class="lang-ko" lang="ko">화면 픽셀 → 코트 위 미터로 변환 (클릭 없이 자동)</span><span class="lang-zh" lang="zh-Hans">画面像素 → 场地上的米，自动完成（无需点击）</span></li>
</ul>
<figure class="pvid pvid--single">
<video controls playsinline preload="none" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_step2_court.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_step2_court.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Stage 2 (29 s, no sound)</span><span class="pvid__label lang-ko" lang="ko">2단계 영상 (29초, 소리 없음)</span><span class="pvid__label lang-zh" lang="zh-Hans">阶段 2（29 秒，无声音）</span><span class="lang-en">Background without players → white lines → straight lines and vanishing point → fitted court model → players' feet on a court map. On-screen labels are in Korean.</span><span class="lang-ko" lang="ko">선수가 사라진 배경 → 흰 선 → 직선과 소실점 → 맞춰진 코트 모델 → 코트 지도 위 선수 발 위치</span><span class="lang-zh" lang="zh-Hans">去掉球员的背景 → 白线 → 直线与消失点 → 拟合后的场地模型 → 场地图上的球员脚位。画面中的文字为韩语。</span></figcaption>
</figure>

<h3>3. <span class="lang-en">Shuttle and player tracking</span><span class="lang-ko" lang="ko">셔틀·선수 추적</span><span class="lang-zh" lang="zh-Hans">羽毛球与球员跟踪</span></h3>
<ul>
<li><span class="lang-en">SAM 3.1 tracks the shuttle and the four players</span><span class="lang-ko" lang="ko">SAM 3.1로 셔틀과 선수 네 명 추적</span><span class="lang-zh" lang="zh-Hans">用 SAM 3.1 跟踪羽毛球和四名球员</span></li>
<li><span class="lang-en">The shuttle is small and fast → candidates added from frame-to-frame motion, points that do not fit a physical flight (a parabola) dropped</span><span class="lang-ko" lang="ko">셔틀은 작고 빠름 → 프레임 간 움직임에서 후보를 더하고, 물리적인 비행(포물선)에 맞지 않는 점 제거</span><span class="lang-zh" lang="zh-Hans">羽毛球又小又快 → 从帧间运动中补充候选点，剔除不符合物理飞行（抛物线）的点</span></li>
</ul>
<figure class="pfig">
<img style="width:100%;max-width:640px" src="{{ site.baseurl }}/images/detail/caster/caster_cut_physics_code.png" loading="lazy" alt="Reference code: a physics model with gravity and the shuttle terminal velocity, first and second derivatives of a track, and checks for a rising and a falling flight">
<figcaption><span class="lang-en">Reference code for the physics check: gravity and the shuttle's terminal velocity, first and second derivatives of a track, tests for a rising and a falling flight.</span><span class="lang-ko" lang="ko">물리 검사의 참고 코드: 중력 가속도와 셔틀 종단 속도, 궤적의 1차·2차 미분, 상승·하강 궤적 검사</span><span class="lang-zh" lang="zh-Hans">物理检查的参考代码：重力加速度与羽毛球终端速度、轨迹的一阶和二阶导数、上升与下降轨迹的判断</span></figcaption>
</figure>

<h3>4. <span class="lang-en">Rally reconstruction</span><span class="lang-ko" lang="ko">랠리 복원</span><span class="lang-zh" lang="zh-Hans">回合重建</span></h3>
<ul>
<li><span class="lang-en">The shuttle follows physics → its 3D flight is estimated with a gravity and air-drag model</span><span class="lang-ko" lang="ko">셔틀은 물리 법칙대로 궤적을 그리므로, 중력·공기 저항 모델로 3차원 비행 추정</span><span class="lang-zh" lang="zh-Hans">羽毛球按物理规律飞行 → 用重力与空气阻力模型估计三维飞行</span></li>
<li><span class="lang-en">Hits found where the flight changes direction → one record line per hit (who, from where to where, height, speed)</span><span class="lang-ko" lang="ko">비행 방향이 바뀌는 곳에서 타구 찾음 → 타구마다 기록 한 줄 (누가, 어디서 어디로, 높이, 속도)</span><span class="lang-zh" lang="zh-Hans">在飞行方向改变处找出击球 → 每次击球一行记录（谁打的、从哪里到哪里、高度、速度）</span></li>
</ul>

<h3>5. <span class="lang-en">Commentary</span><span class="lang-ko" lang="ko">해설</span><span class="lang-zh" lang="zh-Hans">解说</span></h3>
<ul>
<li><span class="lang-en">The hit record from the reconstruction goes to Claude → a shot judgement per hit and the commentary lines</span><span class="lang-ko" lang="ko">랠리 복원에서 나온 타구 기록을 Claude에 전달 → 타구별 샷 판단과 해설 줄 생성</span><span class="lang-zh" lang="zh-Hans">把回合重建得到的击球记录交给 Claude → 逐拍判断击球类型并生成解说句</span></li>
<li><span class="lang-en">A shot is named only at confidence 0.7 or more; the voice is made with ElevenLabs</span><span class="lang-ko" lang="ko">확신도 0.7 이상일 때만 샷 이름 사용, 음성은 ElevenLabs로 생성</span><span class="lang-zh" lang="zh-Hans">置信度达到 0.7 才说出击球类型，语音由 ElevenLabs 生成</span></li>
</ul>
<figure class="pvid pvid--single">
<video controls playsinline preload="none" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_steps345.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_steps345.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Stages 3 · 4 · 5 on the same rally, side by side (23 s, Korean commentary voice)</span><span class="pvid__label lang-ko" lang="ko">3 · 4 · 5단계 영상, 같은 랠리를 나란히 (23초, 해설 음성 있음)</span><span class="pvid__label lang-zh" lang="zh-Hans">阶段 3 · 4 · 5，同一回合并排播放（23 秒，有韩语解说语音）</span><span class="lang-en">Left: shuttle tracking (candidates, dropped ones, final track). Middle: rally reconstruction (flights, hits, record line). Right: commentary (shot and confidence per hit, the lines spoken). Full screen shows the detail. On-screen labels are in Korean.</span><span class="lang-ko" lang="ko">왼쪽: 셔틀 추적(후보, 걸러낸 후보, 최종 궤적) · 가운데: 랠리 복원(비행, 타구, 기록 한 줄) · 오른쪽: 해설(타구별 샷과 확신도, 나가는 해설 줄). 전체 화면으로 보면 자세히 보임.</span><span class="lang-zh" lang="zh-Hans">左：羽毛球跟踪（候选点、被剔除的点、最终轨迹）· 中：回合重建（飞行、击球、记录）· 右：解说（每拍的类型与置信度、播出的解说句）。全屏可看清细节。画面中的文字为韩语。</span></figcaption>
</figure>

<h3>6. <span class="lang-en">HUD and render</span><span class="lang-ko" lang="ko">중계 화면과 렌더</span><span class="lang-zh" lang="zh-Hans">转播画面与渲染</span></h3>
<ul>
<li><span class="lang-en">HUD (rally time, shot count, name tags, court map, smash speed, slow replay) drawn with Pillow, composited with FFmpeg</span><span class="lang-ko" lang="ko">HUD(랠리 시간, 타구 수, 이름표, 코트 맵, 스매시 속도, 슬로 리플레이)를 Pillow로 그리고 FFmpeg으로 합성</span><span class="lang-zh" lang="zh-Hans">HUD（回合时间、击球数、名牌、场地小地图、扣杀球速、慢动作回放）用 Pillow 绘制，用 FFmpeg 合成</span></li>
<li><span class="lang-en">Saved only after 7 automatic checks pass</span><span class="lang-ko" lang="ko">자동 검사 7가지를 통과해야 저장</span><span class="lang-zh" lang="zh-Hans">通过 7 项自动检查后才保存</span></li>
</ul>

<h2 class="star-h"><span class="star-tag">R</span><span class="lang-en">Result</span><span class="lang-ko" lang="ko">결과</span><span class="lang-zh" lang="zh-Hans">结果</span></h2>
<ul>
<li><span class="lang-en">One phone video → a highlight MP4 with caster commentary, HUD, music and slow replay</span><span class="lang-ko" lang="ko">휴대폰 영상 하나 → 캐스터 해설·HUD·음악·슬로 리플레이가 들어간 하이라이트 MP4</span><span class="lang-zh" lang="zh-Hans">一段手机视频 → 带解说、HUD、音乐和慢动作回放的集锦 MP4</span></li>
<li><span class="lang-en">Length chosen from 15 s · 30 s · 1 min · 3 min; each rally kept from the serve to the last shot</span><span class="lang-ko" lang="ko">길이는 15초 · 30초 · 1분 · 3분 중 선택, 랠리는 서브부터 마지막 타구까지</span><span class="lang-zh" lang="zh-Hans">时长可选 15 秒 · 30 秒 · 1 分钟 · 3 分钟，每个回合从发球到最后一拍完整保留</span></li>
</ul>

<h2 class="sec-h"><span class="lang-en">Tech stack</span><span class="lang-ko" lang="ko">기술 스택</span><span class="lang-zh" lang="zh-Hans">技术栈</span></h2>
<div class="table-wrap">
<table>
<thead><tr><th><span class="lang-en">Area</span><span class="lang-ko" lang="ko">구분</span><span class="lang-zh" lang="zh-Hans">类别</span></th><th style="text-align:left"><span class="lang-en">Stack</span><span class="lang-ko" lang="ko">사용 기술</span><span class="lang-zh" lang="zh-Hans">技术</span></th><th style="text-align:left;white-space:normal"><span class="lang-en">Used for</span><span class="lang-ko" lang="ko">쓰임</span><span class="lang-zh" lang="zh-Hans">用途</span></th></tr></thead>
<tbody>
<tr><td><span class="lang-en">Backend</span><span class="lang-ko" lang="ko">백엔드</span><span class="lang-zh" lang="zh-Hans">后端</span></td><td style="text-align:left">Python 3.13 · FastAPI · SQLite (WAL)</td><td style="text-align:left;white-space:normal"><span class="lang-en">API and job queue</span><span class="lang-ko" lang="ko">API와 작업 큐</span><span class="lang-zh" lang="zh-Hans">API 与任务队列</span></td></tr>
<tr><td><span class="lang-en">Frontend</span><span class="lang-ko" lang="ko">프런트엔드</span><span class="lang-zh" lang="zh-Hans">前端</span></td><td style="text-align:left">HTML · CSS · JavaScript (ES modules)</td><td style="text-align:left;white-space:normal"><span class="lang-en">upload and editing screens</span><span class="lang-ko" lang="ko">업로드 · 편집 화면</span><span class="lang-zh" lang="zh-Hans">上传与编辑界面</span></td></tr>
<tr><td><span class="lang-en">Video · audio</span><span class="lang-ko" lang="ko">영상 · 소리</span><span class="lang-zh" lang="zh-Hans">视频 · 音频</span></td><td style="text-align:left">FFmpeg · NumPy · Pillow</td><td style="text-align:left;white-space:normal"><span class="lang-en">render, tracking maths, HUD</span><span class="lang-ko" lang="ko">렌더 · 추적 계산 · HUD</span><span class="lang-zh" lang="zh-Hans">渲染、跟踪计算、HUD</span></td></tr>
<tr><td><span class="lang-en">Tracking model</span><span class="lang-ko" lang="ko">추적 모델</span><span class="lang-zh" lang="zh-Hans">跟踪模型</span></td><td style="text-align:left">SAM 3.1</td><td style="text-align:left;white-space:normal"><span class="lang-en">shuttle and player tracking</span><span class="lang-ko" lang="ko">셔틀 · 선수 추적</span><span class="lang-zh" lang="zh-Hans">羽毛球与球员跟踪</span></td></tr>
<tr><td><span class="lang-en">Language model</span><span class="lang-ko" lang="ko">언어 모델</span><span class="lang-zh" lang="zh-Hans">语言模型</span></td><td style="text-align:left">Claude</td><td style="text-align:left;white-space:normal"><span class="lang-en">shot judgement, commentary lines</span><span class="lang-ko" lang="ko">샷 판단 · 해설 줄 작성</span><span class="lang-zh" lang="zh-Hans">击球判断、解说句</span></td></tr>
<tr><td><span class="lang-en">Voice model</span><span class="lang-ko" lang="ko">음성 모델</span><span class="lang-zh" lang="zh-Hans">语音模型</span></td><td style="text-align:left">ElevenLabs · Supertonic 3</td><td style="text-align:left;white-space:normal"><span class="lang-en">caster voice (Supertonic: local fallback)</span><span class="lang-ko" lang="ko">캐스터 음성 (Supertonic은 로컬 대체)</span><span class="lang-zh" lang="zh-Hans">解说语音（Supertonic 为本地备用）</span></td></tr>
<tr><td><span class="lang-en">Tests</span><span class="lang-ko" lang="ko">테스트</span><span class="lang-zh" lang="zh-Hans">测试</span></td><td style="text-align:left">pytest · Playwright</td><td style="text-align:left;white-space:normal"><span class="lang-en">174 worker + 19 API tests, browser flow</span><span class="lang-ko" lang="ko">worker 174개 + API 19개, 브라우저 흐름</span><span class="lang-zh" lang="zh-Hans">worker 174 个 + API 19 个，浏览器流程</span></td></tr>
</tbody>
</table>
</div>

</div>
