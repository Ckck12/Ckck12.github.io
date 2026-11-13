---
title: "CASTER CUT: Badminton Highlights with AI Caster Commentary"
excerpt: "Personal project (Oct.–Nov. 2026): a local web app that turns your own badminton video into a highlight clip with AI caster commentary and a broadcast-style game HUD. The shuttle and the players are tracked and each rally is rebuilt in court space, so the commentary can say where and how the shuttle was played.<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
excerpt_ko: "개인 프로젝트(2026년 10월 – 11월): 직접 찍은 배드민턴 영상을 AI 캐스터 해설과 중계 화면(HUD)이 들어간 하이라이트로 만드는 로컬 웹 앱. 셔틀과 선수를 추적하고 랠리를 코트 좌표에서 복원해, 어디로 어떻게 쳤는지를 해설합니다.<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
excerpt_zh: "个人项目（2026年10月–11月）：把自己拍摄的羽毛球视频做成带 AI 解说和转播式 HUD 的集锦的本地 Web 应用。跟踪羽毛球和球员，并在场地坐标中重建每个回合，让解说能说出球打向哪里、怎么打。<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
tldr_en:
  - "A phone video of an amateur badminton match goes in; a highlight MP4 with caster commentary, a game HUD and music comes out, all on a local web app."
  - "A vision-language model looking at the video alone did not name shots reliably. So the pipeline tracks the shuttle and the players and rebuilds each rally in court space: who hit, from where to where, how high, how fast."
  - "The commentary model reads that record, and a line names a shot only when the confidence is 0.7 or more (12 of 13 such answers matched my hand labels). Every render must pass 7 automatic checks."
tldr_ko:
  - "휴대폰으로 찍은 동호인 배드민턴 영상을 넣으면, 캐스터 해설·경기 HUD·음악이 들어간 하이라이트 MP4가 로컬 웹 앱에서 만들어집니다."
  - "영상만 보여 준 vision-language model은 샷 이름을 안정적으로 맞히지 못했습니다. 그래서 셔틀과 선수를 추적하고 랠리를 코트 좌표에서 복원합니다. 누가, 어디서 어디로, 얼마나 높이, 얼마나 빠르게 쳤는지가 나옵니다."
  - "해설 모델은 이 기록을 읽고, 확신도가 0.7 이상일 때만 샷 이름을 말합니다(그런 답 13개 중 12개가 직접 적은 정답과 일치). 모든 렌더는 자동 검사 7가지를 통과해야 합니다."
tldr_zh:
  - "输入一段手机拍摄的业余羽毛球比赛视频，本地 Web 应用输出带解说、比赛 HUD 和音乐的集锦 MP4。"
  - "只看视频的 vision-language model 无法稳定判断击球类型。因此流程跟踪羽毛球和球员，并在场地坐标中重建每个回合：谁打的、从哪里打到哪里、多高、多快。"
  - "解说模型读取这份记录，只有置信度达到 0.7 以上时才说出击球类型（这类回答 13 个里有 12 个与我的手工标注一致）。每次渲染都必须通过 7 项自动检查。"
collection: portfolio
order: 0.5
---

{% include tldr.html %}

<div class="proj-body">

<figure class="pvid pvid--single">
<video controls playsinline preload="metadata" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_promo_poster.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_promo.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Promo video (86 s, Korean, with sound)</span><span class="pvid__label lang-ko" lang="ko">홍보 영상 (86초, 한국어, 소리 있음)</span><span class="pvid__label lang-zh" lang="zh-Hans">宣传视频（86 秒，韩语，有声音）</span><span class="lang-en">The seven steps of the app, then a rally rendered by it from serve to finish: rally time and shot count, player name tags (placeholder names), court map, an estimated shuttle speed on hard smashes, slow replay and the caster's voice. The footage is my own; the players agreed to appear.</span><span class="lang-ko" lang="ko">앱의 일곱 단계와, 앱이 만든 랠리 장면(서브부터 끝까지)입니다. 랠리 시간과 타구 수, 선수 이름표(예시 이름), 코트 맵, 강한 스매시의 셔틀 속도(추정), 슬로 리플레이, 캐스터 음성이 들어갑니다. 직접 촬영한 영상이며 출연자의 동의를 받았습니다.</span><span class="lang-zh" lang="zh-Hans">应用的七个步骤，以及由它渲染的一个回合（从发球到结束）：回合时间与击球数、球员名牌（示例名字）、场地小地图、重扣时的球速（估计值）、慢动作回放和解说语音。视频为本人拍摄，出镜者已同意公开。</span></figcaption>
</figure>

<p class="pnote"><span class="lang-en">Personal project · Oct.–Nov. 2026 · Written with Claude Code as the coding agent: I set the requirements, made the design decisions and judged the results (listening tests for the voice, 43 hits labelled by hand).</span><span class="lang-ko" lang="ko">개인 프로젝트 · 2026년 10월 – 11월 · 코딩 에이전트로 Claude Code를 사용했고, 요구사항 정의와 설계 결정, 결과 판단(음성 청취 비교, 타구 43개 직접 라벨링)을 맡았습니다.</span><span class="lang-zh" lang="zh-Hans">个人项目 · 2026年10月–11月 · 使用 Claude Code 作为编码智能体：我负责需求、设计决策和结果判断（语音试听对比、手工标注 43 次击球）。</span></p>

<div class="kpis">
<div class="kpi"><span class="kpi__value">35 / 43</span><span class="kpi__label lang-en">hand-labelled hits found with the right hitting side (sound alone: 15)</span><span class="kpi__label lang-ko" lang="ko">직접 라벨링한 타구 43개 중 친 팀까지 맞게 찾은 수 (소리만 쓸 때 15개)</span><span class="kpi__label lang-zh" lang="zh-Hans">手工标注的 43 次击球中，击球方也判断正确的数量（只用声音时为 15）</span></div>
<div class="kpi"><span class="kpi__value">12 / 13</span><span class="kpi__label lang-en">shot answers at confidence 0.7 or more that matched my labels</span><span class="kpi__label lang-ko" lang="ko">확신도 0.7 이상인 샷 판단 중 직접 적은 정답과 같은 수</span><span class="kpi__label lang-zh" lang="zh-Hans">置信度 0.7 以上的击球判断中与手工标注一致的数量</span></div>
<div class="kpi"><span class="kpi__value">7</span><span class="kpi__label lang-en">automatic checks every render must pass</span><span class="kpi__label lang-ko" lang="ko">렌더마다 통과해야 하는 자동 검사</span><span class="kpi__label lang-zh" lang="zh-Hans">每次渲染必须通过的自动检查</span></div>
<div class="kpi"><span class="kpi__value">15 s · 30 s · 1 min · 3 min</span><span class="kpi__label lang-en">highlight lengths the user can pick</span><span class="kpi__label lang-ko" lang="ko">사용자가 고르는 하이라이트 길이</span><span class="kpi__label lang-zh" lang="zh-Hans">用户可选的集锦时长</span></div>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">S</span>Situation</h2>
<p>Amateur players film whole matches with a phone on a stand, but few ever edit them. And when a vision-language model is asked to name the shots in such a video, it gives different answers for the same clip: the shuttle is a few pixels wide and the far players are small.</p>
<h2 class="star-h"><span class="star-tag">T</span>Task</h2>
<p>Turn one fixed-camera video into a highlight with caster commentary that names the real shot, and a broadcast-style HUD. It runs locally and always ends with a video, even when a stage fails.</p>
<h2 class="star-h"><span class="star-tag">A</span>Action</h2>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">S</span>상황</h2>
<p>동호인들은 휴대폰을 거치대에 세워 경기를 통째로 찍지만, 편집까지 하는 경우는 드뭅니다. 그리고 이런 영상에서 샷 이름을 물으면 vision-language model은 같은 장면에 다른 답을 냅니다. 셔틀은 몇 픽셀이고 먼 쪽 선수는 작게 찍히기 때문입니다.</p>
<h2 class="star-h"><span class="star-tag">T</span>과제</h2>
<p>고정 카메라 영상 하나를, 실제 샷 이름을 말하는 캐스터 해설과 중계 화면(HUD)이 들어간 하이라이트로 만듭니다. 로컬에서 실행하고, 어느 단계가 실패해도 영상은 끝까지 나오게 합니다.</p>
<h2 class="star-h"><span class="star-tag">A</span>행동</h2>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">S</span>背景</h2>
<p>业余选手常把手机架在支架上拍下整场比赛，但很少有人剪辑。而且让 vision-language model 判断这类视频中的击球类型时，同一段画面会得到不同答案：羽毛球只有几个像素，远端球员也很小。</p>
<h2 class="star-h"><span class="star-tag">T</span>任务</h2>
<p>把一段固定机位的视频做成集锦：解说要说出真实的击球类型，并带转播式 HUD。在本地运行；即使某个阶段失败，也要输出视频。</p>
<h2 class="star-h"><span class="star-tag">A</span>行动</h2>
</div>

<figure class="pfig">
<img class="lang-en" style="width:100%;max-width:640px" src="{{ site.baseurl }}/images/projects/caster_cut_pipeline_en.png" alt="CASTER CUT pipeline: highlight selection, court calibration, shuttle and player tracking with SAM 3.1, rally reconstruction, commentary with Claude and ElevenLabs, HUD and render" loading="lazy">
<img class="lang-ko" lang="ko" style="width:100%;max-width:640px" src="{{ site.baseurl }}/images/projects/caster_cut_pipeline_ko.png" alt="CASTER CUT 파이프라인: 하이라이트 선정, 코트 보정, 셔틀·선수 추적(SAM 3.1), 랠리 복원, 해설(Claude, ElevenLabs), 중계 화면과 렌더" loading="lazy">
<img class="lang-zh" lang="zh-Hans" style="width:100%;max-width:640px" src="{{ site.baseurl }}/images/projects/caster_cut_pipeline_zh.png" alt="CASTER CUT 流程：集锦选取、场地标定、羽毛球与球员跟踪（SAM 3.1）、回合重建、解说（Claude、ElevenLabs）、转播画面与渲染" loading="lazy">
<figcaption><span class="lang-en">The pipeline: a phone video becomes a highlight MP4 in six stages. Stages with a logo call an external AI model; the others are algorithms in the project's own code. The small drawings are schematic. Logos are trademarks of their owners.</span><span class="lang-ko" lang="ko">전체 파이프라인: 휴대폰 영상이 여섯 단계를 거쳐 하이라이트 MP4가 됩니다. 로고가 붙은 단계는 외부 AI 모델을 사용하고, 나머지는 프로젝트 코드로 구현한 알고리즘입니다. 단계 안의 작은 그림은 개념도입니다. 로고는 각 회사의 상표입니다.</span><span class="lang-zh" lang="zh-Hans">整体流程：一段手机视频经过六个阶段变成集锦 MP4。带标志的阶段调用外部 AI 模型，其余是项目自身代码实现的算法。各阶段中的小图为示意图。各标志为其所有者的商标。</span></figcaption>
</figure>

<div class="lang-en">
<ol>
<li><strong>Highlight selection.</strong> Rallies are found from hit sounds (sharp high-frequency peaks) together with motion inside the court. Each rally gets a score: a strong finish (0.4), fast exchanges (0.3) and length (0.3). The best ones go in whole, from the serve to the last shot.</li>
<li><strong>Court calibration.</strong> The median of frames spread over the video gives the hall without players. The white lines are found in it, and the camera pose that best lays the court model over them is searched. No clicks are needed, and from here every position is in court metres.</li>
<li><strong>Shuttle and player tracking.</strong> SAM 3.1 segments and tracks the shuttle and the four players. The shuttle is small and fast, so frame-to-frame motion adds candidates, dynamic programming picks the smoothest path, and points that do not fit a physical flight (a parabola bent by gravity) are dropped.</li>
<li><strong>Rally reconstruction.</strong> Each run of shuttle points is fitted as a flight with gravity and air drag, which gives its direction, speed, angle and peak height in court space. A change of direction is a hit, and the direction tells which side hit it. Every hit becomes one line of a record: who, from where to where, how high, how fast.</li>
<li><strong>Commentary.</strong> Claude reads that record as text, judges each shot with a confidence, picks the moments worth a line and writes the lines as JSON. Code then checks them: a shot is named only at confidence 0.7 or more, and lines with unsupported scores or rulings, or lines too long for their slot, are dropped. ElevenLabs voices the result.</li>
<li><strong>HUD and render.</strong> The HUD (rally time, shot count, name tags, court map, smash speed, slow replay) is drawn with Pillow and composited with FFmpeg. Name tags follow each person across rallies by clothing colour. Every render must pass 7 automatic checks before it is saved.</li>
</ol>
<p>Every stage has a fallback (no court found, no track, model or voice failure), so a video always comes out.</p>
<h2 class="star-h"><span class="star-tag">R</span>Result</h2>
<ul>
<li>Hits: of 43 hits I labelled by hand, 35 are found with the right hitting side. Sound alone gave 15.</li>
<li>Shot names: at confidence 0.7 or more, 12 of 13 answers matched my labels (the two runs after the threshold was set). Below it, the line describes the play without naming the shot.</li>
<li>A second video from another camera position ran end to end with the court found automatically. Its hit sounds were recorded quietly and the rallies came out split, so finding rallies from motion on the court is the next step.</li>
<li>Limits: the labels come from one video, and all three labelled rallies were used for tuning.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<ol>
<li><strong>하이라이트 선정.</strong> 타구음(날카로운 고음역 소리)과 코트 안 움직임으로 랠리를 찾습니다. 랠리마다 강한 마무리(0.4), 빠른 주고받기(0.3), 길이(0.3)로 점수를 매기고, 점수가 높은 랠리를 서브부터 마지막 타구까지 통째로 담습니다.</li>
<li><strong>코트 보정.</strong> 영상 곳곳의 프레임에서 중앙값을 내 선수가 없는 체육관 화면을 만듭니다. 거기서 흰 선을 찾고, 코트 규격 모델이 그 선에 가장 잘 겹치는 카메라 위치와 각도를 탐색합니다. 클릭이 필요 없고, 이후의 모든 위치는 코트 위 미터 단위로 다룹니다.</li>
<li><strong>셔틀·선수 추적.</strong> SAM 3.1이 셔틀과 선수 네 명을 분할하고 추적합니다. 셔틀은 작고 빨라서, 프레임 간 움직임에서 후보를 더 모으고 동적 계획법으로 가장 매끄러운 경로를 고른 뒤, 물리적인 비행(중력으로 휘는 포물선)에 맞지 않는 점을 버립니다.</li>
<li><strong>랠리 복원.</strong> 셔틀 점 묶음마다 중력과 공기 저항으로 비행을 맞춰, 코트 좌표에서의 방향·속도·각도·최고 높이를 구합니다. 방향이 바뀌는 곳이 타구이고, 방향으로 어느 팀이 쳤는지 알 수 있습니다. 타구마다 기록 한 줄이 나옵니다. 누가, 어디서 어디로, 얼마나 높이, 얼마나 빠르게 쳤는지입니다.</li>
<li><strong>해설.</strong> Claude가 이 기록을 글로 읽고, 타구마다 샷과 확신도를 판단하고, 말할 순간을 골라 해설 줄을 JSON으로 씁니다. 그다음 코드가 검사합니다. 확신도 0.7 이상일 때만 샷 이름을 말하고, 근거 없는 점수·판정이나 주어진 시간에 못 들어가는 줄은 뺍니다. 음성은 ElevenLabs가 만듭니다.</li>
<li><strong>중계 화면과 렌더.</strong> HUD(랠리 시간, 타구 수, 이름표, 코트 맵, 스매시 속도, 슬로 리플레이)는 Pillow로 그리고 FFmpeg으로 합성합니다. 이름표는 옷 색으로 랠리가 바뀌어도 같은 사람을 따라갑니다. 모든 렌더는 자동 검사 7가지를 통과해야 저장됩니다.</li>
</ol>
<p>모든 단계에 실패 대비 경로가 있어(코트를 못 찾음, 추적 실패, 모델·음성 실패) 영상은 항상 나옵니다.</p>
<h2 class="star-h"><span class="star-tag">R</span>결과</h2>
<ul>
<li>타구 찾기: 직접 라벨링한 43타 중 35타를 친 팀까지 맞게 찾습니다. 소리만 쓸 때는 15타였습니다.</li>
<li>샷 이름: 확신도 0.7 이상인 판단은 13개 중 12개가 정답과 같았습니다(기준을 정한 뒤의 두 실행). 그보다 낮으면 샷 이름 없이 플레이를 설명합니다.</li>
<li>다른 카메라 위치에서 찍은 두 번째 영상도 코트를 자동으로 찾아 끝까지 만들어졌습니다. 다만 타구음이 작게 녹음돼 랠리가 잘게 쪼개졌고, 코트 안 움직임으로 랠리를 찾는 것이 다음 작업입니다.</li>
<li>한계: 정답 라벨이 영상 하나에서 나왔고, 라벨링한 랠리 3개를 모두 조정에 썼습니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<ol>
<li><strong>集锦选取。</strong> 用击球声（尖锐的高频声音）和场地内的运动找出回合。每个回合按强力收尾（0.4）、快速对攻（0.3）、长度（0.3）打分，得分高的回合从发球到最后一拍完整收入。</li>
<li><strong>场地标定。</strong> 从视频各处的帧取中值，得到没有球员的场馆画面；在其中找出白线，再搜索能让场地模型与白线最吻合的相机位置和角度。无需点击，之后所有位置都以场地上的米为单位。</li>
<li><strong>羽毛球与球员跟踪。</strong> SAM 3.1 分割并跟踪羽毛球和四名球员。羽毛球又小又快，所以再从帧间运动中补充候选点，用动态规划选出最平滑的路径，并丢弃不符合物理飞行（受重力弯曲的抛物线）的点。</li>
<li><strong>回合重建。</strong> 对每一段跟踪点，用重力和空气阻力拟合出一段飞行，得到它在场地坐标中的方向、速度、角度和最高高度。方向改变之处就是一次击球，方向也说明是哪一方打的。每次击球生成一行记录：谁打的、从哪里到哪里、多高、多快。</li>
<li><strong>解说。</strong> Claude 以文字读取这份记录，判断每次击球的类型和置信度，选出值得解说的时刻，并以 JSON 写出解说句。随后由代码检查：置信度达到 0.7 才说出击球类型，没有依据的比分或判罚、以及时间内说不完的句子会被剔除。语音由 ElevenLabs 生成。</li>
<li><strong>转播画面与渲染。</strong> HUD（回合时间、击球数、名牌、场地小地图、扣杀球速、慢动作回放）用 Pillow 绘制，用 FFmpeg 合成。名牌依据衣服颜色，在不同回合中跟随同一个人。每次渲染都必须通过 7 项自动检查才会保存。</li>
</ol>
<p>每个阶段都有回退路径（找不到场地、跟踪失败、模型或语音失败），所以总能输出视频。</p>
<h2 class="star-h"><span class="star-tag">R</span>结果</h2>
<ul>
<li>击球检测：我手工标注的 43 次击球中，有 35 次连击球方也判断正确。只用声音时为 15 次。</li>
<li>击球类型：置信度 0.7 以上的判断，13 个里有 12 个与标注一致（确定阈值之后的两次运行）。低于该值时只描述打法，不说类型名称。</li>
<li>另一机位拍摄的第二段视频也能自动找到场地并完整生成。但它的击球声录得很小，回合被切碎；下一步是依据场地内的运动来检测回合。</li>
<li>局限：标注只来自一段视频，而且标注过的 3 个回合都用于调参。</li>
</ul>
</div>

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
