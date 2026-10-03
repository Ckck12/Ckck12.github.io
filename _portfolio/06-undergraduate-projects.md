---
title: "Undergraduate Projects"
order: 6
redirect_from:
  - /portfolio/05-undergraduate-projects/
excerpt: "Four projects from my B.S. in Industrial Engineering at Inha University (2017–2023): an autonomous RC car, hydrogen-station site evaluation, a Korean food classifier app, and a 3D figure generation platform.<br/><img src='/images/projects/undergrad.png' alt='Undergraduate projects'>"
tldr_en:
  - "Four hands-on projects from my B.S. in Industrial Engineering at Inha University (2017–2023)."
  - "An Arduino obstacle-avoiding RC car (the lightest car to finish the course) and an ML study that picked 59 hydrogen-station sites among 168 rest areas."
  - "A MobileNet Korean-food classifier app and a PIFuHD platform that turns one photo into a printable 3D figure."
tldr_ko:
  - "인하대학교 산업공학과 학부 과정(2017–2023)에서 진행한 네 가지 실습형 프로젝트입니다."
  - "코스를 완주한 차 중 가장 가벼운 아두이노 장애물 회피 RC카, 휴게소 168곳 중 수소충전소 적합지 59곳을 고른 머신러닝 연구."
  - "MobileNet 기반 한식 분류 앱, 사진 한 장을 3D 프린팅용 피규어로 바꾸는 PIFuHD 기반 플랫폼."
collection: portfolio
---

{% include lang-toggle.html %}

{% include tldr.html %}

<div class="proj-body">

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/projects/undergrad.png" alt="Undergraduate projects" loading="lazy">
</figure>

<div class="lang-en">
<h2 class="sec-h">3D Figure Generation Platform — Primer GenAI Hackathon, Apr. 2023</h2>
<ul>
<li><strong>Situation / Task:</strong> turn a single 2D photo into a printable 3D figure and connect users with vendors who can print it.</li>
<li><strong>Action:</strong> PM and back-end developer; served PIFuHD to generate a 3D figure model from a 2D photo and matched users with 3D-printing vendors.</li>
<li><strong>Result:</strong> live demo at <a href="https://aahg.netlify.app">aahg.netlify.app</a>.</li>
</ul>
<h2 class="sec-h">Hydrogen Refueling Station Site Evaluation with Supervised Learning — Fall 2022</h2>
<ul>
<li><strong>Situation / Task:</strong> Industrial Engineering capstone (entry to the 2022 Fall Korean Institute of Industrial Engineers undergraduate competition): rank candidate highway rest areas for hydrogen refueling stations.</li>
<li><strong>Action:</strong> collected data with QGIS and web crawling; compared logistic regression, SVC, decision tree and XGBoost.</li>
<li><strong>Result:</strong> identified 59 suitable sites among 168 highway rest areas.</li>
</ul>
<h2 class="sec-h">Korean Food Classification App — Yangjae AI Hub bootcamp, Jul. – Sep. 2022</h2>
<ul>
<li><strong>Situation / Task:</strong> classify Korean dishes from photos in a mobile app.</li>
<li><strong>Action:</strong> fine-tuned MobileNet for Korean-food image classification; built the front-end UI and the model deployment environment.</li>
<li><strong>Result:</strong> a working end-to-end app from camera input to prediction.</li>
</ul>
<h2 class="sec-h">Autonomous RC-Car Competition — Creative Engineering Design course, Fall 2017</h2>
<ul>
<li><strong>Situation / Task:</strong> build an Arduino + C++ obstacle-avoiding car for a 3 m course under size and weight limits.</li>
<li><strong>Action:</strong> resolved the weight-vs-traction trade-off (a lighter car lost steering grip) with a center hinge that loads the drive wheels and rubber-banded wheels.</li>
<li><strong>Result:</strong> finished as the lightest car to complete the course.</li>
</ul>
<p>Also on GitHub: <a href="https://github.com/Ckck12/Sign_Language_aivle">Sign_Language_aivle</a> (KT AIVLE School, May 2023), a team Django web service that turns sign-language input into ChatGPT queries; I contributed front-end views.</p>
</div>
<div class="lang-ko" lang="ko">
<h2 class="sec-h">3D 피규어 생성 플랫폼 — Primer GenAI 해커톤, 2023년 4월</h2>
<ul>
<li><strong>상황 / 과제:</strong> 2D 사진 한 장을 3D 프린팅 가능한 피규어로 바꾸고, 출력해 줄 업체와 사용자를 연결한다.</li>
<li><strong>행동:</strong> PM 겸 백엔드 개발자로 PIFuHD를 서빙해 2D 사진에서 3D 피규어 모델을 생성하고, 사용자와 3D 프린팅 업체를 매칭했습니다.</li>
<li><strong>결과:</strong> 라이브 데모 <a href="https://aahg.netlify.app">aahg.netlify.app</a>.</li>
</ul>
<h2 class="sec-h">지도학습 기반 수소충전소 입지 평가 — 2022년 2학기</h2>
<ul>
<li><strong>상황 / 과제:</strong> 산업공학 캡스톤 프로젝트(2022 추계 대한산업공학회 학부생 경진대회 출품): 고속도로 휴게소 후보지를 수소충전소 입지로 평가·순위화한다.</li>
<li><strong>행동:</strong> QGIS와 웹 크롤링으로 데이터를 수집하고, 로지스틱 회귀·SVC·의사결정나무·XGBoost를 비교했습니다.</li>
<li><strong>결과:</strong> 고속도로 휴게소 168곳 중 적합지 59곳을 선정했습니다.</li>
</ul>
<h2 class="sec-h">한식 분류 앱 — 양재 AI 허브 부트캠프, 2022년 7월–9월</h2>
<ul>
<li><strong>상황 / 과제:</strong> 모바일 앱에서 사진으로 한식 메뉴를 분류한다.</li>
<li><strong>행동:</strong> 한식 이미지 분류를 위해 MobileNet을 미세조정하고, 프런트엔드 UI와 모델 배포 환경을 만들었습니다.</li>
<li><strong>결과:</strong> 카메라 입력부터 예측까지 동작하는 엔드투엔드 앱.</li>
</ul>
<h2 class="sec-h">자율주행 RC카 경진대회 — 창의공학설계 수업, 2017년 2학기</h2>
<ul>
<li><strong>상황 / 과제:</strong> 크기·무게 제한 아래 3 m 코스를 달리는 아두이노 + C++ 장애물 회피 자동차를 만든다.</li>
<li><strong>행동:</strong> 무게와 접지력의 상충(차가 가벼울수록 조향 접지력이 떨어짐)을, 구동 바퀴에 하중을 싣는 중앙 힌지와 고무줄을 감은 바퀴로 해결했습니다.</li>
<li><strong>결과:</strong> 코스를 완주한 차량 중 가장 가벼운 차로 완주했습니다.</li>
</ul>
<p>GitHub 추가 프로젝트: <a href="https://github.com/Ckck12/Sign_Language_aivle">Sign_Language_aivle</a> (KT AIVLE School, 2023년 5월). 수어 입력을 ChatGPT 질의로 바꾸는 팀 Django 웹 서비스이며, 저는 프런트엔드 뷰를 맡았습니다.</p>
</div>

</div>
