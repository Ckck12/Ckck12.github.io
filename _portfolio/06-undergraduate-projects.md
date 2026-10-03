---
title: "Undergraduate Projects"
order: 6
redirect_from:
  - /portfolio/05-undergraduate-projects/
excerpt: "Four projects from my B.S. in Industrial Engineering at Inha University (2017–2023): an autonomous RC car, hydrogen-station site evaluation, a Korean-food classifier app, and a 3D figure generation platform.<br/><img src='/images/projects/thumb_undergrad.png' alt='Undergraduate projects'>"
excerpt_ko: "Inha University 산업공학 학사 과정(2017–2023)의 네 프로젝트: 자율주행 RC카, 수소충전소 입지 평가, 한식 분류 앱, 3D 피규어 생성 플랫폼.<br/><img src='/images/projects/thumb_undergrad.png' alt='Undergraduate projects'>"
excerpt_zh: "我在 Inha University 工业工程本科期间（2017–2023）的四个项目：自动驾驶 RC 小车、加氢站选址评估、韩餐分类应用和 3D 手办生成平台。<br/><img src='/images/projects/thumb_undergrad.png' alt='Undergraduate projects'>"
tldr_en:
  - "Four hands-on projects from my B.S. in Industrial Engineering at Inha University (2017–2023)."
  - "An Arduino obstacle-avoiding RC car (lightest car to finish the course) and an ML study picking 59 hydrogen-station sites among 168 rest areas."
  - "A MobileNet Korean-food classifier app, and a PIFuHD platform turning one photo into a printable 3D figure."
tldr_ko:
  - "Inha University 산업공학 학사 과정(2017–2023)에서 진행한 실습형 프로젝트 네 가지."
  - "코스 완주 차량 중 가장 가벼운 Arduino 장애물 회피 RC카, 휴게소 168곳 중 수소충전소 적합지 59곳을 고른 ML 연구."
  - "MobileNet 한식 분류 앱, 사진 한 장을 3D 프린팅용 피규어로 바꾸는 PIFuHD 플랫폼."
tldr_zh:
  - "在 Inha University 工业工程本科期间（2017–2023）完成的四个实践项目。"
  - "Arduino 避障 RC 小车（完赛车辆中最轻），以及从 168 个高速服务区中选出 59 个加氢站适宜站点的机器学习研究。"
  - "MobileNet 韩餐分类应用，以及用 PIFuHD 把一张照片变成可 3D 打印手办的平台。"
collection: portfolio
---

{% include tldr.html %}

<div class="proj-body">

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/projects/undergrad.png" alt="Undergraduate projects" loading="lazy">
</figure>

<div class="lang-en">
<h2 class="sec-h">3D Figure Generation Platform — Primer GenAI Hackathon, Apr. 2023</h2>
<ul>
<li><strong>Situation / Task:</strong> turn one 2D photo into a printable 3D figure; connect users with print vendors.</li>
<li><strong>Action:</strong> PM and back-end developer. Served PIFuHD to generate a 3D figure from a 2D photo; matched users with 3D-printing vendors.</li>
<li><strong>Result:</strong> live demo at <a href="https://aahg.netlify.app">aahg.netlify.app</a>.</li>
</ul>
<h2 class="sec-h">Hydrogen Refueling Station Site Evaluation with Supervised Learning — Fall 2022</h2>
<ul>
<li><strong>Situation / Task:</strong> Industrial Engineering capstone, an entry to the 2022 Fall Korean Institute of Industrial Engineers undergraduate competition. Rank highway rest areas as hydrogen-station sites.</li>
<li><strong>Action:</strong> collected data with QGIS and web crawling; compared logistic regression, SVC, decision tree and XGBoost.</li>
<li><strong>Result:</strong> identified 59 suitable sites among 168 highway rest areas.</li>
</ul>
<h2 class="sec-h">Korean Food Classification App — Yangjae AI Hub bootcamp, Jul. – Sep. 2022</h2>
<ul>
<li><strong>Situation / Task:</strong> classify Korean dishes from photos in a mobile app.</li>
<li><strong>Action:</strong> fine-tuned MobileNet; built the front-end UI and the model deployment environment.</li>
<li><strong>Result:</strong> a working end-to-end app, from camera input to prediction.</li>
</ul>
<h2 class="sec-h">Autonomous RC-Car Competition — Creative Engineering Design course, Fall 2017</h2>
<ul>
<li><strong>Situation / Task:</strong> build an Arduino + C++ obstacle-avoiding car for a 3 m course under size and weight limits.</li>
<li><strong>Action:</strong> a lighter car lost steering grip. Fixed it with a center hinge that loads the drive wheels, plus rubber-banded wheels.</li>
<li><strong>Result:</strong> the lightest car to complete the course.</li>
</ul>
<p>Also on GitHub: <a href="https://github.com/Ckck12/Sign_Language_aivle">Sign_Language_aivle</a> (KT AIVLE School, May 2023). A team Django service that turns sign-language input into ChatGPT queries; I contributed front-end views.</p>
</div>
<div class="lang-ko" lang="ko">
<h2 class="sec-h">3D Figure Generation Platform — Primer GenAI Hackathon, 2023년 4월</h2>
<ul>
<li><strong>상황 / 과제:</strong> 2D 사진 한 장을 3D 프린팅 가능한 피규어로 바꾸고, 출력 업체와 사용자를 연결.</li>
<li><strong>행동:</strong> PM 겸 백엔드 개발. PIFuHD를 서빙해 2D 사진에서 3D 피규어를 생성하고, 사용자와 3D 프린팅 업체를 매칭했습니다.</li>
<li><strong>결과:</strong> 라이브 데모 <a href="https://aahg.netlify.app">aahg.netlify.app</a>.</li>
</ul>
<h2 class="sec-h">Hydrogen Refueling Station Site Evaluation with Supervised Learning — 2022년 2학기</h2>
<ul>
<li><strong>상황 / 과제:</strong> 산업공학 캡스톤(2022 추계 Korean Institute of Industrial Engineers 학부생 경진대회 출품작). 고속도로 휴게소를 수소충전소 입지로 평가·순위화.</li>
<li><strong>행동:</strong> QGIS와 웹 크롤링으로 데이터를 수집하고, 로지스틱 회귀·SVC·의사결정나무·XGBoost를 비교했습니다.</li>
<li><strong>결과:</strong> 고속도로 휴게소 168곳 중 적합지 59곳 선정.</li>
</ul>
<h2 class="sec-h">Korean Food Classification App — Yangjae AI Hub 부트캠프, 2022년 7월 – 9월</h2>
<ul>
<li><strong>상황 / 과제:</strong> 모바일 앱에서 사진으로 한식 메뉴 분류.</li>
<li><strong>행동:</strong> MobileNet을 미세조정하고, 프런트엔드 UI와 모델 배포 환경을 만들었습니다.</li>
<li><strong>결과:</strong> 카메라 입력부터 예측까지 동작하는 엔드투엔드 앱.</li>
</ul>
<h2 class="sec-h">Autonomous RC-Car Competition — 창의공학설계 수업, 2017년 2학기</h2>
<ul>
<li><strong>상황 / 과제:</strong> 크기·무게 제한 아래 3 m 코스를 달리는 Arduino + C++ 장애물 회피 자동차 제작.</li>
<li><strong>행동:</strong> 차가 가벼울수록 조향 접지력이 떨어졌습니다. 구동 바퀴에 하중을 싣는 중앙 힌지와 고무줄을 감은 바퀴로 해결했습니다.</li>
<li><strong>결과:</strong> 코스를 완주한 차량 중 가장 가벼운 차.</li>
</ul>
<p>GitHub 추가 프로젝트: <a href="https://github.com/Ckck12/Sign_Language_aivle">Sign_Language_aivle</a> (KT AIVLE School, 2023년 5월). 수어 입력을 ChatGPT 질의로 바꾸는 팀 Django 서비스이며, 저는 프런트엔드 뷰를 맡았습니다.</p>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="sec-h">3D Figure Generation Platform — Primer GenAI Hackathon，2023年4月</h2>
<ul>
<li><strong>情境 / 任务：</strong>把一张 2D 照片变成可 3D 打印的手办，并为用户对接打印商家。</li>
<li><strong>行动：</strong>担任 PM 兼后端开发。部署 PIFuHD，从 2D 照片生成 3D 手办模型，并为用户匹配 3D 打印商家。</li>
<li><strong>结果：</strong>在线演示 <a href="https://aahg.netlify.app">aahg.netlify.app</a>。</li>
</ul>
<h2 class="sec-h">Hydrogen Refueling Station Site Evaluation with Supervised Learning — 2022年秋季学期</h2>
<ul>
<li><strong>情境 / 任务：</strong>工业工程毕业设计，参加 2022 年秋季 Korean Institute of Industrial Engineers 本科生竞赛。评估并排序作为加氢站候选的高速公路服务区。</li>
<li><strong>行动：</strong>用 QGIS 和网络爬虫收集数据；比较逻辑回归、SVC、决策树和 XGBoost。</li>
<li><strong>结果：</strong>在 168 个高速公路服务区中选出 59 个适宜站点。</li>
</ul>
<h2 class="sec-h">Korean Food Classification App — Yangjae AI Hub 训练营，2022年7月–9月</h2>
<ul>
<li><strong>情境 / 任务：</strong>在手机应用中根据照片识别韩餐。</li>
<li><strong>行动：</strong>微调 MobileNet；搭建前端 UI 与模型部署环境。</li>
<li><strong>结果：</strong>从相机输入到预测结果的端到端可用应用。</li>
</ul>
<h2 class="sec-h">Autonomous RC-Car Competition — 创意工程设计课程，2017年秋季学期</h2>
<ul>
<li><strong>情境 / 任务：</strong>在尺寸和重量限制下，制作能跑完 3 m 赛道的 Arduino + C++ 避障小车。</li>
<li><strong>行动：</strong>车越轻，转向抓地力越差。用让驱动轮承重的中央铰链和缠橡皮筋的车轮解决了这一问题。</li>
<li><strong>结果：</strong>完赛车辆中最轻的一辆。</li>
</ul>
<p>GitHub 上的其他项目：<a href="https://github.com/Ckck12/Sign_Language_aivle">Sign_Language_aivle</a>（KT AIVLE School，2023年5月）。这是把手语输入转为 ChatGPT 查询的团队 Django 网络服务；我负责前端视图。</p>
</div>

</div>
