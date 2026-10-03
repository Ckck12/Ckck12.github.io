---
title: "State-of-Flight: Wearable EEG and Motion Context During Flying or Floating"
authors: "Alexander Vicol, Steve Mann, Chris McGale, Gianella Bejar-Alvarez, Michel Herrera Viyella, Darya Zanjanpour, Chan Park, Xiaoming Chen, Xueqi Yang, Bingxuan Yang"
author_list:
  - name: "Alexander Vicol"
    sup: "1"
  - name: "Steve Mann"
    sup: "1"
  - name: "Chris McGale"
    sup: "1"
  - name: "Gianella Bejar-Alvarez"
    sup: "1"
  - name: "Michel Herrera Viyella"
    sup: "1"
  - name: "Darya Zanjanpour"
    sup: "1"
  - name: "Chan Park"
    sup: "2"
    me: true
  - name: "Xiaoming Chen"
    sup: "2"
  - name: "Xueqi Yang"
    sup: "1"
  - name: "Bingxuan Yang"
    sup: "1"
affiliations:
  - "<sup>1</sup>Dept. of ECE, University of Toronto"
  - "<sup>2</sup>Dept. of MIE, University of Toronto"
venue: "IEEE International Symposium on Computer-Based Medical Systems (CBMS) 2026"
status:
year: 2026
date: 2026-05-01   # year-level only; used for ordering
order: 4
teaser: cbms2026.png
teaser_hover:
paper_url:
code_url:
project_url:
tldr: "Wearable EEG and motion context captured while flying or floating."
tldr_en:
  - "State-of-Flight/Float is a hypothesized transition state during taxi, takeoff, cruise, landing or floating."
  - "The paper records it in the field with a 4-channel wearable EEG headband plus IMU, and provides a reusable analysis pipeline."
  - "In a small dataset (5 flight participants, 2 controls), pooled theta/beta ratios are elevated in flight relative to controls; results are preliminary."
tldr_ko:
  - "State-of-Flight/Float은 지상 이동, 이륙, 순항, 착륙, 또는 물 위에 떠 있을 때 나타난다고 가정한 전이 상태입니다."
  - "논문은 4채널 웨어러블 EEG 헤드밴드와 IMU로 이를 현장에서 기록하고, 재사용 가능한 분석 파이프라인을 제시합니다."
  - "소규모 데이터(비행 참가자 5명, 대조군 2명)에서 비행 구간의 theta/beta 비율이 대조군보다 높았으며, 결과는 예비적입니다."
excerpt: "An in-cabin wearable EEG + IMU protocol and analysis pipeline for a hypothesized State-of-Flight/Float transition (IEEE CBMS 2026, co-author)."
meta_in_body: true
featured: false
collection: publications
---

{% include lang-toggle.html %}

{% include tldr.html %}

{% include pub-meta.html %}

<div class="proj-body">

<p class="pnote"><span class="lang-en">Co-authored paper from the Mann Lab, University of Toronto.</span><span class="lang-ko" lang="ko">토론토 대학교 Mann Lab의 공저 논문입니다.</span></p>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">S</span>Situation</h2>
<p>Commercial flights create a distinctive sensory environment: acceleration, vibration, cabin noise and strong vestibular cues around taxi, takeoff and landing, often with anticipatory attention. Yet nearly all EEG studies of arousal, attention and vestibular processing are run in stationary laboratories, leaving a gap between controlled findings and the multisensory reality of flight.</p>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">S</span>상황 (Situation)</h2>
<p>상업 비행은 독특한 감각 환경을 만듭니다. 지상 이동, 이륙, 착륙 때 가속, 진동, 기내 소음, 강한 전정 자극이 나타나고, 종종 긴장된 주의가 함께 따라옵니다. 그런데 각성, 주의, 전정 처리에 관한 EEG 연구는 거의 모두 고정된 실험실에서 이루어져, 통제된 결과와 실제 비행의 다감각 환경 사이에 간극이 있습니다.</p>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">T</span>Task</h2>
<p>Show that a consumer-grade wearable EEG headband, read together with motion data, can record such flight and float transitions in the field, and provide a reproducible analysis scaffold for larger future studies.</p>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">T</span>과제 (Task)</h2>
<p>보급형 웨어러블 EEG 헤드밴드를 움직임 데이터와 함께 읽으면 비행 및 부유 상태의 전이를 현장에서 기록할 수 있음을 보이고, 향후 더 큰 연구를 위한 재현 가능한 분석 틀을 제공하는 것입니다.</p>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">A</span>Action</h2>
<ul>
<li><strong>Recording:</strong> a Muse S/Athena-class headband (TP9, AF7, AF8, TP10 at 256 Hz) with its on-board IMU, logged by a MuseLog/MuseAI app that writes time-synchronized, crash-tolerant CSV files per device; sessions are labeled by flight phase (takeoff, cruise, landing).</li>
<li><strong>Pipeline:</strong> demeaning, a 60 Hz notch, 0.5–30 Hz band-pass, 5 s epochs with amplitude-based rejection, Welch power spectra, theta/beta ratio (TBR) and frontal alpha asymmetry (FAA), plus EEG + IMU correlation maps that treat motion as context rather than only as noise.</li>
<li><strong>Comparisons:</strong> flight phases against non-flight controls and a water-based float dataset (warm and cold water).</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">A</span>행동 (Action)</h2>
<ul>
<li><strong>기록:</strong> Muse S/Athena급 헤드밴드(TP9, AF7, AF8, TP10, 256 Hz)와 내장 IMU를 사용했고, MuseLog/MuseAI 앱이 기기별로 시간 동기화된 CSV를 중단에 강한 방식으로 기록합니다. 세션은 비행 구간(이륙, 순항, 착륙)별로 구분됩니다.</li>
<li><strong>파이프라인:</strong> 평균 제거, 60 Hz 노치 필터, 0.5–30 Hz 대역 통과 필터, 진폭 기준 제거를 거친 5초 에폭, Welch 파워 스펙트럼, theta/beta 비율(TBR)과 전두엽 알파 비대칭(FAA), 그리고 움직임을 잡음이 아닌 맥락으로 다루는 EEG + IMU 상관 지도를 계산합니다.</li>
<li><strong>비교:</strong> 비행 구간을 비행이 아닌 대조군 기록, 그리고 수상 부유 데이터(따뜻한 물/차가운 물)와 비교합니다.</li>
</ul>
</div>

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/detail/sof/fig5a_pooled_tbr.png" alt="Bar chart of pooled theta/beta ratio for control, three flight phases and two float conditions" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 5a.</strong> Pooled theta/beta ratio across control, flight phases and float conditions (n = retained 5 s epochs). Error bars show epoch-level variability for visualization, not inferential testing.</span><span class="lang-ko" lang="ko"><strong>그림 5a.</strong> 대조군, 비행 구간, 부유 조건별 theta/beta 비율(n = 유지된 5초 에폭 수). 오차 막대는 시각화를 위한 에폭 단위 변동이며 통계적 검정 결과가 아닙니다.</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">R</span>Result</h2>
<ul>
<li>Across a small field dataset (n = 5 flight participants, n = 2 control participants, plus a float comparison dataset), pooled TBR is clearly elevated in flight phases relative to the non-flight control bucket, with float conditions in an intermediate range.</li>
<li>FAA also varies across conditions but is interpreted conservatively because of motion, fit variability and sign-convention sensitivity.</li>
<li>The results are descriptive proof-of-concept findings: they demonstrate feasibility, instrumentation and a reusable analysis scaffold rather than population-level effects.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">R</span>결과 (Result)</h2>
<ul>
<li>소규모 현장 데이터(비행 참가자 n = 5, 대조군 n = 2, 부유 비교 데이터)에서 비행 구간의 통합 TBR이 비행이 아닌 대조군보다 뚜렷하게 높았고, 부유 조건은 그 중간 범위에 있었습니다.</li>
<li>FAA도 조건에 따라 달라지지만, 움직임, 착용 상태 변화, 부호 규약에 민감하기 때문에 보수적으로 해석합니다.</li>
<li>이 결과는 기술적(descriptive) 개념 증명으로, 모집단 수준의 효과가 아니라 측정 가능성, 장비 구성, 재사용 가능한 분석 틀을 보여 줍니다.</li>
</ul>
</div>

</div>
