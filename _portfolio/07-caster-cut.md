---
title: "CASTER CUT: Badminton Highlights with AI Caster Commentary"
excerpt: "Personal project (Oct.–Nov. 2026): a local web app that turns your own badminton video into a highlight clip with AI caster commentary and a broadcast-style game HUD. The shuttle and the players are tracked and each rally is rebuilt in court space, so the commentary can say where and how the shuttle was played.<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
excerpt_ko: "개인 프로젝트(2026년 10월 – 11월): 직접 찍은 배드민턴 영상을 AI 캐스터 해설과 중계 화면(HUD)이 들어간 하이라이트로 만드는 로컬 웹 앱. 셔틀과 선수를 추적하고 랠리를 코트 좌표에서 복원해, 어디로 어떻게 쳤는지를 해설합니다.<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
excerpt_zh: "个人项目（2026年10月–11月）：把自己拍摄的羽毛球视频做成带 AI 解说和转播式 HUD 的集锦的本地 Web 应用。跟踪羽毛球和球员，并在场地坐标中重建每个回合，让解说能说出球打向哪里、怎么打。<br/><img src='/images/projects/thumb_caster_cut.png' alt='A doubles rally with the CASTER CUT game HUD'>"
tldr_en:
  - "A phone video of a badminton match goes in; a highlight MP4 with caster commentary, a game HUD and music comes out of a web app."
  - "A vision-language model looking at the video alone did not name shots reliably. So the pipeline tracks the shuttle and the players and rebuilds each rally in court space: who hit, from where to where, how high, how fast."
  - "The commentary model reads that record, and a line names a shot only when the confidence of the shot judgement is 0.7 or more."
tldr_ko:
  - "휴대폰으로 찍은 배드민턴 영상을 넣으면, 캐스터 해설·경기 HUD·음악이 들어간 하이라이트 MP4가 웹 앱에서 만들어집니다."
  - "영상만 보여 준 vision-language model은 샷 이름을 안정적으로 맞히지 못했습니다. 그래서 셔틀과 선수를 추적하고 랠리를 코트 좌표에서 복원합니다. 누가, 어디서 어디로, 얼마나 높이, 얼마나 빠르게 쳤는지가 나옵니다."
  - "해설 모델은 이 기록을 읽고, 샷 판단의 확신도가 0.7 이상일 때만 샷 이름을 말합니다."
tldr_zh:
  - "输入一段手机拍摄的羽毛球比赛视频，Web 应用输出带解说、比赛 HUD 和音乐的集锦 MP4。"
  - "只看视频的 vision-language model 无法稳定判断击球类型。因此流程跟踪羽毛球和球员，并在场地坐标中重建每个回合：谁打的、从哪里打到哪里、多高、多快。"
  - "解说模型读取这份记录，只有击球判断的置信度达到 0.7 以上时才说出击球类型。"
collection: portfolio
order: 0.5
---

{% include tldr.html %}

<div class="proj-body">

<figure class="pvid pvid--single">
<video controls playsinline preload="metadata" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_promo_poster.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_promo.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Promo video (86 s, Korean, with sound)</span><span class="pvid__label lang-ko" lang="ko">홍보 영상 (86초, 한국어, 소리 있음)</span><span class="pvid__label lang-zh" lang="zh-Hans">宣传视频（86 秒，韩语，有声音）</span><span class="lang-en">Rally time and shot count, player name tags (placeholder names), court map, the shuttle speed on hard smashes, slow replay and the caster's voice. The footage is my own; the players agreed to appear.</span><span class="lang-ko" lang="ko">랠리 시간과 타구 수, 선수 이름표(예시 이름), 코트 맵, 강한 스매시의 셔틀 속도, 슬로 리플레이, 캐스터 음성이 들어갑니다. 직접 촬영한 영상이며 출연자의 동의를 받았습니다.</span><span class="lang-zh" lang="zh-Hans">回合时间与击球数、球员名牌（示例名字）、场地小地图、重扣时的球速、慢动作回放和解说语音。视频为本人拍摄，出镜者已同意公开。</span></figcaption>
</figure>

<p class="pnote"><span class="lang-en">Personal project · Oct.–Nov. 2026 · Written with Claude Code as the coding agent: I set the requirements, made the design decisions and judged the results.</span><span class="lang-ko" lang="ko">개인 프로젝트 · 2026년 10월 – 11월 · 코딩 에이전트로 Claude Code를 사용했고, 요구사항 정의와 설계 결정, 결과 판단을 맡았습니다.</span><span class="lang-zh" lang="zh-Hans">个人项目 · 2026年10月–11月 · 使用 Claude Code 作为编码智能体：我负责需求、设计决策和结果判断。</span></p>

<h2 class="star-h"><span class="star-tag">S</span><span class="lang-en">Situation</span><span class="lang-ko" lang="ko">상황</span><span class="lang-zh" lang="zh-Hans">背景</span></h2>
<p><span class="lang-en">A vision-language model shown only the video does not name badminton shots reliably: the shuttle is a few pixels wide and fast.</span><span class="lang-ko" lang="ko">영상만 보여 준 vision-language model은 배드민턴 샷 이름을 안정적으로 맞히지 못합니다. 셔틀이 몇 픽셀 크기이고 빠르기 때문입니다.</span><span class="lang-zh" lang="zh-Hans">只看视频的 vision-language model 无法稳定判断羽毛球的击球类型：羽毛球只有几个像素，而且很快。</span></p>

<h2 class="star-h"><span class="star-tag">T</span><span class="lang-en">Task</span><span class="lang-ko" lang="ko">과제</span><span class="lang-zh" lang="zh-Hans">任务</span></h2>
<p><span class="lang-en">Turn one fixed-camera match video into a highlight with caster commentary that names the real shot, and a broadcast-style HUD.</span><span class="lang-ko" lang="ko">고정 카메라로 찍은 경기 영상 하나를, 실제 샷을 말하는 캐스터 해설과 중계 화면(HUD)이 들어간 하이라이트로 만듭니다.</span><span class="lang-zh" lang="zh-Hans">把一段固定机位的比赛视频做成集锦：解说要说出真实的击球类型，并带转播式 HUD。</span></p>

<h2 class="star-h"><span class="star-tag">A</span><span class="lang-en">Action</span><span class="lang-ko" lang="ko">행동</span><span class="lang-zh" lang="zh-Hans">行动</span></h2>
<p><span class="lang-en">Six stages, in order. Each one below says what it does and why it is done that way, with a video of what that stage produces on one rally.</span><span class="lang-ko" lang="ko">여섯 단계를 순서대로 거칩니다. 아래에 단계마다 무엇을 하는지와 왜 그렇게 했는지를 적고, 그 단계가 한 랠리에서 만들어 내는 결과를 영상으로 붙였습니다.</span><span class="lang-zh" lang="zh-Hans">依次经过六个阶段。下面逐一说明每个阶段做什么、为什么这样做，并附上该阶段在一个回合上的输出视频。</span></p>

<figure class="pfig">
<img class="lang-en" style="width:100%;max-width:640px" src="{{ site.baseurl }}/images/projects/caster_cut_pipeline_en.png" alt="CASTER CUT pipeline: highlight selection, court calibration, shuttle and player tracking with SAM 3.1, rally reconstruction, commentary with Claude and ElevenLabs, HUD and render" loading="lazy">
<img class="lang-ko" lang="ko" style="width:100%;max-width:640px" src="{{ site.baseurl }}/images/projects/caster_cut_pipeline_ko.png" alt="CASTER CUT 파이프라인: 하이라이트 선정, 코트 보정, 셔틀·선수 추적(SAM 3.1), 랠리 복원, 해설(Claude, ElevenLabs), 중계 화면과 렌더" loading="lazy">
<img class="lang-zh" lang="zh-Hans" style="width:100%;max-width:640px" src="{{ site.baseurl }}/images/projects/caster_cut_pipeline_zh.png" alt="CASTER CUT 流程：集锦选取、场地标定、羽毛球与球员跟踪（SAM 3.1）、回合重建、解说（Claude、ElevenLabs）、转播画面与渲染" loading="lazy">
<figcaption><span class="lang-en">The pipeline. Stages with a logo call an external AI model; the others are algorithms in the project's own code. The small drawings are schematic. Logos are trademarks of their owners.</span><span class="lang-ko" lang="ko">전체 파이프라인: 로고가 붙은 단계는 외부 AI 모델을 사용하고, 나머지는 프로젝트 코드로 구현한 알고리즘입니다. 단계 안의 작은 그림은 개념도입니다. 로고는 각 회사의 상표입니다.</span><span class="lang-zh" lang="zh-Hans">整体流程：带标志的阶段调用外部 AI 模型，其余是项目自身代码实现的算法。各阶段中的小图为示意图。各标志为其所有者的商标。</span></figcaption>
</figure>

<h3>1. <span class="lang-en">Highlight selection</span><span class="lang-ko" lang="ko">하이라이트 선정</span><span class="lang-zh" lang="zh-Hans">集锦选取</span></h3>
<ul>
<li><strong><span class="lang-en">What</span><span class="lang-ko" lang="ko">무엇</span><span class="lang-zh" lang="zh-Hans">做什么</span></strong> <span class="lang-en">Rallies are found from hit sounds (sharp high-frequency peaks) together with motion inside the court. Each rally gets a score: a strong finish (0.4), fast exchanges (0.3) and length (0.3). The best ones go in whole, from the serve to the last shot.</span><span class="lang-ko" lang="ko">타구음(날카로운 고음역 소리)과 코트 안 움직임으로 랠리를 찾습니다. 랠리마다 강한 마무리(0.4), 빠른 주고받기(0.3), 길이(0.3)로 점수를 매기고, 점수가 높은 랠리를 서브부터 마지막 타구까지 통째로 담습니다.</span><span class="lang-zh" lang="zh-Hans">用击球声（尖锐的高频声音）和场地内的运动找出回合。每个回合按强力收尾（0.4）、快速对攻（0.3）、长度（0.3）打分，得分高的回合从发球到最后一拍完整收入。</span></li>
<li><strong><span class="lang-en">Why</span><span class="lang-ko" lang="ko">왜</span><span class="lang-zh" lang="zh-Hans">为什么</span></strong> <span class="lang-en">Most of a match video is waiting, and hit sounds come one after another only during a rally. Motion inside our court is checked with them, to tell our rally from the sounds of the next court.</span><span class="lang-ko" lang="ko">경기 영상의 대부분은 쉬는 시간이고, 타구음은 랠리 중에만 연달아 납니다. 옆 코트의 소리와 구분하려고 우리 코트 안의 움직임을 함께 봅니다.</span><span class="lang-zh" lang="zh-Hans">比赛视频的大部分是等待时间，而击球声只在回合中接连出现。同时检查本场地内的运动，以区分邻场的声音。</span></li>
</ul>
<figure class="pvid pvid--single">
<video controls playsinline preload="none" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_step1_rally.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_step1_rally.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Stage 1 (42 s, with sound)</span><span class="pvid__label lang-ko" lang="ko">1단계 영상 (42초, 소리 있음)</span><span class="pvid__label lang-zh" lang="zh-Hans">阶段 1（42 秒，有声音）</span><span class="lang-en">Hit sound strength, motion inside the court, the rallies the two make, and each rally's score when it ends. On-screen labels are in Korean.</span><span class="lang-ko" lang="ko">타구음 세기, 코트 안 움직임, 그 둘로 묶인 랠리, 랠리가 끝날 때의 점수입니다.</span><span class="lang-zh" lang="zh-Hans">击球声强度、场地内的运动、由二者得到的回合，以及回合结束时的得分。画面中的文字为韩语。</span></figcaption>
</figure>

<h3>2. <span class="lang-en">Court calibration</span><span class="lang-ko" lang="ko">코트 보정</span><span class="lang-zh" lang="zh-Hans">场地标定</span></h3>
<ul>
<li><strong><span class="lang-en">What</span><span class="lang-ko" lang="ko">무엇</span><span class="lang-zh" lang="zh-Hans">做什么</span></strong> <span class="lang-en">The median of frames spread over the video gives the hall without players. The white lines are found in it, and the camera pose that best lays the court model over them is searched.</span><span class="lang-ko" lang="ko">영상 곳곳의 프레임에서 중앙값을 내 선수가 없는 체육관 화면을 만듭니다. 거기서 흰 선을 찾고, 코트 규격 모델이 그 선에 가장 잘 겹치는 카메라 위치와 각도를 탐색합니다.</span><span class="lang-zh" lang="zh-Hans">从视频各处的帧取中值，得到没有球员的场馆画面；在其中找出白线，再搜索能让场地模型与白线最吻合的相机位置和角度。</span></li>
<li><strong><span class="lang-en">Why</span><span class="lang-ko" lang="ko">왜</span><span class="lang-zh" lang="zh-Hans">为什么</span></strong> <span class="lang-en">Pixels alone give no distance, height or speed. With every position in court metres, the record can say from where to where and how fast. The court is found automatically, so the user does not have to mark its corners.</span><span class="lang-ko" lang="ko">화면의 픽셀만으로는 거리, 높이, 속도를 말할 수 없습니다. 모든 위치를 코트 위 미터로 바꿔야 어디서 어디로, 얼마나 빠르게 쳤는지가 나옵니다. 사용자가 코트 모서리를 찍지 않아도 되도록 자동으로 찾습니다.</span><span class="lang-zh" lang="zh-Hans">只靠像素无法得到距离、高度和速度。把所有位置换算成场地上的米，才能说出从哪里打到哪里、有多快。场地自动识别，用户不必标出四角。</span></li>
</ul>
<figure class="pvid pvid--single">
<video controls playsinline preload="none" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_step2_court.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_step2_court.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Stage 2 (29 s, no sound)</span><span class="pvid__label lang-ko" lang="ko">2단계 영상 (29초, 소리 없음)</span><span class="pvid__label lang-zh" lang="zh-Hans">阶段 2（29 秒，无声音）</span><span class="lang-en">Sampled frames, the background without players, the white lines, straight lines and vanishing point, the fitted court model, then the players' feet on a court map in metres. On-screen labels are in Korean.</span><span class="lang-ko" lang="ko">프레임 뽑기, 선수가 사라진 배경, 흰 선, 직선과 소실점, 맞춰진 코트 모델, 그리고 미터 단위 코트 지도에 찍힌 선수 발 위치입니다.</span><span class="lang-zh" lang="zh-Hans">抽取的帧、去掉球员的背景、白线、直线与消失点、拟合后的场地模型，以及以米为单位的场地图上的球员脚位。画面中的文字为韩语。</span></figcaption>
</figure>

<h3>3. <span class="lang-en">Shuttle and player tracking</span><span class="lang-ko" lang="ko">셔틀·선수 추적</span><span class="lang-zh" lang="zh-Hans">羽毛球与球员跟踪</span></h3>
<ul>
<li><strong><span class="lang-en">What</span><span class="lang-ko" lang="ko">무엇</span><span class="lang-zh" lang="zh-Hans">做什么</span></strong> <span class="lang-en">SAM 3.1 segments and tracks the shuttle and the four players. For the shuttle, frame-to-frame motion adds candidates, the smoothest path is picked, and points that do not fit a physical flight (a parabola bent by gravity) are dropped.</span><span class="lang-ko" lang="ko">SAM 3.1이 셔틀과 선수 네 명을 분할하고 추적합니다. 셔틀은 프레임 간 움직임에서 후보를 더 모으고, 가장 매끄러운 경로를 고른 뒤, 물리적인 비행(중력으로 휘는 포물선)에 맞지 않는 점을 버립니다.</span><span class="lang-zh" lang="zh-Hans">SAM 3.1 分割并跟踪羽毛球和四名球员。对羽毛球，再从帧间运动中补充候选点，选出最平滑的路径，并丢弃不符合物理飞行（受重力弯曲的抛物线）的点。</span></li>
<li><strong><span class="lang-en">Why</span><span class="lang-ko" lang="ko">왜</span><span class="lang-zh" lang="zh-Hans">为什么</span></strong> <span class="lang-en">The shuttle is a few pixels wide and fast, so a segmentation model alone loses it or picks up something else, such as a racket head. Instead of training a dedicated model, candidates are filtered by what a shuttle is: a small point that moves alone and flies under gravity.</span><span class="lang-ko" lang="ko">셔틀은 몇 픽셀 크기이고 빨라서, 분할 모델만으로는 놓치거나 라켓 머리 같은 다른 물체를 잡습니다. 전용 모델을 학습시키는 대신, 혼자 움직이는 작은 점이고 중력을 따라 난다는 셔틀의 성질로 후보를 거릅니다.</span><span class="lang-zh" lang="zh-Hans">羽毛球只有几个像素而且很快，只靠分割模型会跟丢，或者抓到球拍头之类的其他物体。这里不训练专用模型，而是用羽毛球本身的性质筛选候选点：单独运动的小点，并且在重力下飞行。</span></li>
</ul>
<figure class="pvid pvid--single">
<video controls playsinline preload="none" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_step3_tracking.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_step3_tracking.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Stage 3 (23 s, with sound)</span><span class="pvid__label lang-ko" lang="ko">3단계 영상 (23초, 소리 있음)</span><span class="pvid__label lang-zh" lang="zh-Hans">阶段 3（23 秒，有声音）</span><span class="lang-en">Boxes from the text prompt (yellow) and the point prompt (blue), points that move alone (white), candidates that were dropped with the reason (red cross), and the final track (lime) with a magnified view. On-screen labels are in Korean.</span><span class="lang-ko" lang="ko">텍스트 프롬프트 상자(노랑), 포인트 프롬프트 상자(하늘), 혼자 움직이는 점(흰색), 걸러낸 후보와 그 이유(빨간 X), 최종 궤적(라임)과 확대 화면입니다.</span><span class="lang-zh" lang="zh-Hans">文字提示的框（黄）、点提示的框（蓝）、单独运动的点（白）、被剔除的候选及原因（红叉）、最终轨迹（黄绿）和放大画面。画面中的文字为韩语。</span></figcaption>
</figure>
<figure class="pfig">
<img style="width:100%;max-width:640px" src="{{ site.baseurl }}/images/detail/caster/caster_cut_physics_code.png" loading="lazy" alt="Reference code: a physics model with gravity and the shuttle terminal velocity, first and second derivatives of a track, and checks for a rising and a falling flight">
<figcaption><span class="lang-en">Reference code for the physics check: gravity and the shuttle's terminal velocity, the first and second derivatives of a track, and the tests for a rising and a falling flight. The tracker's physics check started from these definitions.</span><span class="lang-ko" lang="ko">물리 검사의 참고 코드: 중력 가속도와 셔틀 종단 속도, 궤적의 1차·2차 미분, 상승·하강 궤적 검사입니다. 추적기의 물리 검사는 이 정의에서 출발했습니다.</span><span class="lang-zh" lang="zh-Hans">物理检查的参考代码：重力加速度与羽毛球终端速度、轨迹的一阶和二阶导数、上升与下降轨迹的判断。跟踪器的物理检查由这些定义出发。</span></figcaption>
</figure>

<h3>4. <span class="lang-en">Rally reconstruction</span><span class="lang-ko" lang="ko">랠리 복원</span><span class="lang-zh" lang="zh-Hans">回合重建</span></h3>
<ul>
<li><strong><span class="lang-en">What</span><span class="lang-ko" lang="ko">무엇</span><span class="lang-zh" lang="zh-Hans">做什么</span></strong> <span class="lang-en">Each run of shuttle points is fitted as a flight with gravity and air drag, which gives its direction, speed, angle and peak height in court space. A change of direction is a hit, and the direction tells which side hit it. Every hit becomes one line of a record.</span><span class="lang-ko" lang="ko">셔틀 점 묶음마다 중력과 공기 저항으로 비행을 맞춰, 코트 좌표에서의 방향·속도·각도·최고 높이를 구합니다. 방향이 바뀌는 곳이 타구이고, 방향으로 어느 팀이 쳤는지 알 수 있습니다. 타구마다 기록 한 줄이 나옵니다.</span><span class="lang-zh" lang="zh-Hans">对每一段跟踪点，用重力和空气阻力拟合出一段飞行，得到它在场地坐标中的方向、速度、角度和最高高度。方向改变之处就是一次击球，方向也说明是哪一方打的。每次击球生成一行记录。</span></li>
<li><strong><span class="lang-en">Why</span><span class="lang-ko" lang="ko">왜</span><span class="lang-zh" lang="zh-Hans">为什么</span></strong> <span class="lang-en">One camera gives no depth, so the 3D flight is estimated by assuming the shuttle obeys physics. Hit sounds can be quiet or mixed with the next court's, so hits are found where the flight changes direction rather than from sound.</span><span class="lang-ko" lang="ko">카메라가 한 대라 깊이 정보가 없습니다. 그래서 셔틀이 물리 법칙대로 난다는 가정으로 3차원 비행을 추정합니다. 타구음은 작거나 옆 코트 소리와 섞이므로, 타구는 소리가 아니라 비행 방향이 바뀌는 곳에서 찾습니다.</span><span class="lang-zh" lang="zh-Hans">只有一台相机，没有深度信息，所以假设羽毛球遵循物理规律来估计三维飞行。击球声可能很小或与邻场的声音混在一起，因此击球不是靠声音，而是在飞行方向改变的地方寻找。</span></li>
</ul>
<figure class="pvid pvid--single">
<video controls playsinline preload="none" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_step4_rally_record.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_step4_rally_record.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Stage 4 (23 s, with sound)</span><span class="pvid__label lang-ko" lang="ko">4단계 영상 (23초, 소리 있음)</span><span class="pvid__label lang-zh" lang="zh-Hans">阶段 4（23 秒，有声音）</span><span class="lang-en">Fitted flights coloured by the side that hit them, a mark at each hit, the flights seen from the side in metres, and the record line given to the commentary model. On-screen labels are in Korean.</span><span class="lang-ko" lang="ko">친 팀별로 색을 나눈 비행 곡선, 타구 표시, 옆에서 본 비행(미터), 해설 모델에 주는 기록 한 줄입니다.</span><span class="lang-zh" lang="zh-Hans">按击球方着色的飞行曲线、击球标记、从侧面看的飞行（米），以及交给解说模型的一行记录。画面中的文字为韩语。</span></figcaption>
</figure>

<h3>5. <span class="lang-en">Commentary</span><span class="lang-ko" lang="ko">해설</span><span class="lang-zh" lang="zh-Hans">解说</span></h3>
<ul>
<li><strong><span class="lang-en">What</span><span class="lang-ko" lang="ko">무엇</span><span class="lang-zh" lang="zh-Hans">做什么</span></strong> <span class="lang-en">Claude reads that record as text, judges each shot with a confidence, picks the moments worth a line and writes the lines. Code then checks them: a shot is named only at confidence 0.7 or more, and lines with unsupported scores or rulings, or lines too long for their slot, are dropped. ElevenLabs voices the result.</span><span class="lang-ko" lang="ko">Claude가 이 기록을 글로 읽고, 타구마다 샷과 확신도를 판단하고, 말할 순간을 골라 해설 줄을 씁니다. 그다음 코드가 검사합니다. 확신도 0.7 이상일 때만 샷 이름을 말하고, 근거 없는 점수·판정이나 주어진 시간에 못 들어가는 줄은 뺍니다. 음성은 ElevenLabs가 만듭니다.</span><span class="lang-zh" lang="zh-Hans">Claude 以文字读取这份记录，判断每次击球的类型和置信度，选出值得解说的时刻并写出解说句。随后由代码检查：置信度达到 0.7 才说出击球类型，没有依据的比分或判罚、以及时间内说不完的句子会被剔除。语音由 ElevenLabs 生成。</span></li>
<li><strong><span class="lang-en">Why</span><span class="lang-ko" lang="ko">왜</span><span class="lang-zh" lang="zh-Hans">为什么</span></strong> <span class="lang-en">Shown the video directly, the model's shot names wavered, so it is given the measured values as text instead. A wrong shot name breaks trust in the whole commentary, so when the model is unsure the line describes the play without naming the shot.</span><span class="lang-ko" lang="ko">영상을 직접 보여 주면 샷 이름이 흔들렸기 때문에, 측정한 값을 글로 줍니다. 틀린 샷 이름은 해설 전체의 신뢰를 깨므로, 확신이 낮으면 샷 이름 없이 상황만 말하게 합니다.</span><span class="lang-zh" lang="zh-Hans">直接给模型看视频时，击球类型的判断不稳定，所以改为以文字提供测量值。说错击球类型会破坏整段解说的可信度，因此把握不大时只描述场面，不说类型名称。</span></li>
</ul>
<figure class="pvid pvid--single">
<video controls playsinline preload="none" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_step5_commentary.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_step5_commentary.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Stage 5 (23 s, Korean commentary voice)</span><span class="pvid__label lang-ko" lang="ko">5단계 영상 (23초, 해설 음성 있음)</span><span class="pvid__label lang-zh" lang="zh-Hans">阶段 5（23 秒，有韩语解说语音）</span><span class="lang-en">The shot and confidence the model gave each hit, the 0.7 line, and the commentary lines as they are spoken, with the part of the record each one rests on. On-screen labels are in Korean.</span><span class="lang-ko" lang="ko">타구마다 모델이 판단한 샷과 확신도, 0.7 기준선, 실제로 나가는 해설 줄과 그 근거가 된 기록 구절입니다.</span><span class="lang-zh" lang="zh-Hans">模型对每次击球给出的类型和置信度、0.7 的基准线，以及实际播出的解说句和它所依据的记录片段。画面中的文字为韩语。</span></figcaption>
</figure>

<h3>6. <span class="lang-en">HUD and render</span><span class="lang-ko" lang="ko">중계 화면과 렌더</span><span class="lang-zh" lang="zh-Hans">转播画面与渲染</span></h3>
<ul>
<li><strong><span class="lang-en">What</span><span class="lang-ko" lang="ko">무엇</span><span class="lang-zh" lang="zh-Hans">做什么</span></strong> <span class="lang-en">The HUD (rally time, shot count, name tags, court map, smash speed, slow replay) is drawn with Pillow and composited with FFmpeg. Name tags follow each person across rallies by clothing colour. Every render must pass 7 automatic checks before it is saved.</span><span class="lang-ko" lang="ko">HUD(랠리 시간, 타구 수, 이름표, 코트 맵, 스매시 속도, 슬로 리플레이)는 Pillow로 그리고 FFmpeg으로 합성합니다. 이름표는 옷 색으로 랠리가 바뀌어도 같은 사람을 따라갑니다. 모든 렌더는 자동 검사 7가지를 통과해야 저장됩니다.</span><span class="lang-zh" lang="zh-Hans">HUD（回合时间、击球数、名牌、场地小地图、扣杀球速、慢动作回放）用 Pillow 绘制，用 FFmpeg 合成。名牌依据衣服颜色，在不同回合中跟随同一个人。每次渲染都必须通过 7 项自动检查才会保存。</span></li>
<li><strong><span class="lang-en">Why</span><span class="lang-ko" lang="ko">왜</span><span class="lang-zh" lang="zh-Hans">为什么</span></strong> <span class="lang-en">The values measured in the earlier stages are put where the viewer sees them at once. The automatic checks keep a video with a wrong length, lost sync or black frames from reaching the user.</span><span class="lang-ko" lang="ko">앞 단계에서 측정한 값을 시청자가 화면에서 바로 보게 합니다. 자동 검사는 길이, 싱크, 검은 화면 같은 문제가 있는 영상이 사용자에게 가지 않게 합니다.</span><span class="lang-zh" lang="zh-Hans">把前面各阶段测得的数值放在观众一眼能看到的位置。自动检查可以防止时长不对、音画不同步或出现黑屏的视频交到用户手里。</span></li>
</ul>
<figure class="pvid pvid--single">
<video controls playsinline preload="none" poster="{{ site.baseurl }}/images/detail/caster/caster_cut_step6_hud.jpg"><source src="{{ site.baseurl }}/files/videos/caster_cut_step6_hud.mp4" type="video/mp4"></video>
<figcaption><span class="pvid__label lang-en">Stage 6 (26 s, with sound)</span><span class="pvid__label lang-ko" lang="ko">6단계 영상 (26초, 소리 있음)</span><span class="pvid__label lang-zh" lang="zh-Hans">阶段 6（26 秒，有声音）</span><span class="lang-en">The finished video, with a caption naming each part of the HUD and the stage its value comes from. On-screen labels are in Korean.</span><span class="lang-ko" lang="ko">완성된 영상 위에, HUD의 각 요소가 몇 번째 단계의 결과인지를 차례로 표시했습니다.</span><span class="lang-zh" lang="zh-Hans">成片画面上依次标出 HUD 的各个部分，以及其数值来自哪个阶段。画面中的文字为韩语。</span></figcaption>
</figure>

<h2 class="star-h"><span class="star-tag">R</span><span class="lang-en">Result</span><span class="lang-ko" lang="ko">결과</span><span class="lang-zh" lang="zh-Hans">结果</span></h2>
<ul>
<li><span class="lang-en">One phone video becomes a highlight MP4 with caster commentary, a HUD, music and a slow replay, without marking the court by hand.</span><span class="lang-ko" lang="ko">휴대폰 영상 하나가, 코트를 직접 표시하지 않아도, 캐스터 해설·HUD·음악·슬로 리플레이가 들어간 하이라이트 MP4가 됩니다.</span><span class="lang-zh" lang="zh-Hans">一段手机视频，无需手动标出场地，就能变成带解说、HUD、音乐和慢动作回放的集锦 MP4。</span></li>
<li><span class="lang-en">The length is chosen from 15 s, 30 s, 1 min and 3 min, and each rally is kept from the serve to the last shot.</span><span class="lang-ko" lang="ko">길이는 15초, 30초, 1분, 3분 중에서 고르고, 랠리는 서브부터 마지막 타구까지 담깁니다.</span><span class="lang-zh" lang="zh-Hans">时长可选 15 秒、30 秒、1 分钟、3 分钟，每个回合从发球到最后一拍完整保留。</span></li>
<li><span class="lang-en">The commentary rests on the measured record, and a shot the model is unsure of is not named.</span><span class="lang-ko" lang="ko">해설은 측정한 기록을 근거로 쓰고, 확신이 낮은 샷은 이름을 말하지 않습니다.</span><span class="lang-zh" lang="zh-Hans">解说以测量记录为依据，把握不大的击球不说类型名称。</span></li>
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
