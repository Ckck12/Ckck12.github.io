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
tldr: "Wearable EEG and motion context captured while flying or floating (preliminary field study)."
tldr_list_ko: "비행 또는 부유 중 웨어러블 EEG와 움직임 맥락을 기록한 예비 현장 연구입니다."
tldr_list_zh: "在飞行或漂浮时采集可穿戴 EEG 与运动情境数据的初步现场研究。"
tldr_en:
  - "State-of-Flight/Float is a hypothesized transition state during taxi, takeoff, cruise, landing or floating."
  - "A 4-channel wearable EEG headband plus IMU records it in the field, with a reusable analysis pipeline."
  - "Small dataset (5 flight participants, 2 controls): pooled theta/beta ratio is higher in flight than in controls. Results are preliminary."
tldr_ko:
  - "State-of-Flight/Float은 지상 이동, 이륙, 순항, 착륙, 부유 중에 나타난다고 가정한 전이 상태입니다."
  - "4채널 웨어러블 EEG 헤드밴드와 IMU로 현장에서 기록하고, 재사용 가능한 분석 파이프라인을 제공합니다."
  - "소규모 데이터(비행 참가자 5명, 대조군 2명)에서 비행 중 통합 theta/beta 비율이 대조군보다 높았습니다. 결과는 예비적입니다."
tldr_zh:
  - "State-of-Flight/Float 是假设在滑行、起飞、巡航、降落或漂浮时出现的过渡状态。"
  - "用 4 通道可穿戴 EEG 头带加 IMU 在现场记录，并提供可复用的分析流程。"
  - "小规模数据（飞行参与者 5 人，对照 2 人）中，飞行时的合并 theta/beta 比高于对照组；结果为初步结论。"
excerpt: "An in-cabin wearable EEG + IMU protocol and analysis pipeline for a hypothesized State-of-Flight/Float transition (IEEE CBMS 2026, co-author)."
meta_in_body: true
featured: false
collection: publications
---

{% include tldr.html %}

{% include pub-meta.html %}

<div class="proj-body">

<p class="pnote"><span class="lang-en">Co-authored paper from the Mann Lab, University of Toronto.</span><span class="lang-ko" lang="ko">University of Toronto Mann Lab의 공저 논문입니다.</span><span class="lang-zh" lang="zh-Hans">University of Toronto Mann Lab 的合著论文。</span></p>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">S</span>Situation</h2>
<ul>
<li>Taxi, takeoff and landing bring acceleration, vibration, cabin noise and strong vestibular cues, often with anticipatory attention.</li>
<li>Yet most EEG studies of arousal, attention and vestibular processing run in stationary labs.</li>
<li>This leaves a gap between lab findings and the multisensory reality of flight.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">S</span>상황</h2>
<ul>
<li>지상 이동, 이륙, 착륙 때는 가속, 진동, 기내 소음, 강한 전정 자극이 나타나고, 긴장된 주의가 따르기도 합니다.</li>
<li>그러나 각성·주의·전정 처리에 관한 EEG 연구는 대부분 고정된 실험실에서 이루어집니다.</li>
<li>그래서 실험실 결과와 실제 비행의 다감각 환경 사이에 간극이 있습니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">S</span>情境</h2>
<ul>
<li>滑行、起飞和降落时伴随加速度、振动、舱内噪声和强烈的前庭刺激，还常伴有预期性注意。</li>
<li>但关于唤醒、注意和前庭处理的 EEG 研究几乎都在固定实验室中进行。</li>
<li>因此，实验室结论与真实飞行的多感官环境之间存在差距。</li>
</ul>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">T</span>Task</h2>
<ul>
<li>Show that a consumer-grade EEG headband, read with motion data, can record flight and float transitions in the field.</li>
<li>Provide a reproducible analysis scaffold for larger future studies.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">T</span>과제</h2>
<ul>
<li>보급형 EEG 헤드밴드와 움직임 데이터로 비행·부유 전이를 현장에서 기록할 수 있음을 보입니다.</li>
<li>향후 더 큰 연구를 위한 재현 가능한 분석 틀을 제공합니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">T</span>任务</h2>
<ul>
<li>证明消费级 EEG 头带结合运动数据，可在现场记录飞行与漂浮过渡。</li>
<li>为后续更大规模的研究提供可复现的分析框架。</li>
</ul>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">A</span>Action</h2>
<ul>
<li><strong>Recording:</strong> a Muse S/Athena-class headband (TP9, AF7, AF8, TP10 at 256 Hz) with its on-board IMU. A MuseLog/MuseAI app writes time-synchronized, crash-tolerant CSV files per device. Sessions are labeled by flight phase (takeoff, cruise, landing).</li>
<li><strong>Pipeline:</strong> demeaning, 60 Hz notch, 0.5–30 Hz band-pass, 5 s epochs with amplitude-based rejection, Welch power spectra. Metrics: theta/beta ratio (TBR) and frontal alpha asymmetry (FAA). EEG + IMU correlation maps treat motion as context, not only noise.</li>
<li><strong>Comparisons:</strong> flight phases vs. non-flight controls and a water-based float dataset (warm and cold water).</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">A</span>행동</h2>
<ul>
<li><strong>기록:</strong> Muse S/Athena급 헤드밴드(TP9, AF7, AF8, TP10, 256 Hz)와 내장 IMU를 썼습니다. MuseLog/MuseAI 앱이 기기별로 시간 동기화된 CSV를 중단에 강하게 기록합니다. 세션은 비행 구간(이륙, 순항, 착륙)별로 구분합니다.</li>
<li><strong>파이프라인:</strong> 평균 제거, 60 Hz 노치, 0.5–30 Hz 대역 통과, 진폭 기준 제거를 거친 5초 에폭, Welch 파워 스펙트럼. 지표: theta/beta 비율(TBR)과 전두 알파 비대칭(FAA). EEG + IMU 상관 지도는 움직임을 잡음만이 아닌 맥락으로 다룹니다.</li>
<li><strong>비교:</strong> 비행 구간 vs 비행이 아닌 대조군, 그리고 수상 부유 데이터(따뜻한 물/차가운 물).</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">A</span>行动</h2>
<ul>
<li><strong>记录：</strong>Muse S/Athena 级头带（TP9、AF7、AF8、TP10，256 Hz）及其内置 IMU。MuseLog/MuseAI 应用按设备写入时间同步、抗崩溃的 CSV 文件。会话按飞行阶段（起飞、巡航、降落）标注。</li>
<li><strong>处理流程：</strong>去均值、60 Hz 陷波、0.5–30 Hz 带通、按幅值剔除的 5 s 分段、Welch 功率谱。指标：theta/beta 比（TBR）与额叶 alpha 不对称（FAA）。EEG + IMU 相关图把运动视为情境，而不只是噪声。</li>
<li><strong>对比：</strong>飞行阶段 vs 非飞行对照，以及水上漂浮数据（温水与冷水）。</li>
</ul>
</div>

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/detail/sof/fig5a_pooled_tbr.png" alt="Bar chart of pooled theta/beta ratio for control, three flight phases and two float conditions" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 5a.</strong> Pooled theta/beta ratio for control, flight phases and float conditions (n = retained 5 s epochs). Error bars show epoch-level variability for visualization, not inferential testing.</span><span class="lang-ko" lang="ko"><strong>그림 5a.</strong> 대조군, 비행 구간, 부유 조건별 통합 theta/beta 비율(n = 유지된 5초 에폭 수). 오차 막대는 시각화용 에폭 단위 변동이며 통계 검정이 아닙니다.</span><span class="lang-zh" lang="zh-Hans"><strong>图 5a.</strong> 对照、各飞行阶段与漂浮条件下的合并 theta/beta 比（n = 保留的 5 s 分段数）。误差条仅用于展示分段级波动，不代表统计推断。</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">R</span>Result</h2>
<ul>
<li>Small field dataset (n = 5 flight participants, n = 2 controls, plus a float dataset). Pooled TBR is clearly higher in flight phases than in controls; float conditions fall in between.</li>
<li>FAA also varies across conditions. It is read conservatively, since motion, fit variability and sign conventions affect it.</li>
<li>These are descriptive proof-of-concept results. They show feasibility, instrumentation and a reusable pipeline, not population-level effects.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">R</span>결과</h2>
<ul>
<li>소규모 현장 데이터(비행 참가자 n = 5, 대조군 n = 2, 부유 비교 데이터). 비행 구간의 통합 TBR이 대조군보다 뚜렷하게 높았고, 부유 조건은 그 중간이었습니다.</li>
<li>FAA도 조건별로 달라지지만, 움직임·착용 상태·부호 규약에 민감해 보수적으로 해석합니다.</li>
<li>기술적(descriptive) 개념 증명 결과입니다. 모집단 수준 효과가 아니라 측정 가능성, 장비 구성, 재사용 가능한 분석 틀을 보여 줍니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">R</span>结果</h2>
<ul>
<li>小规模现场数据（飞行参与者 n = 5，对照 n = 2，另有漂浮对比数据）。飞行阶段的合并 TBR 明显高于对照组，漂浮条件介于两者之间。</li>
<li>FAA 也随条件变化，但易受运动、佩戴差异和符号约定影响，因此保守解读。</li>
<li>这些是描述性的概念验证结果：展示的是可行性、测量配置和可复用流程，而非总体层面的效应。</li>
</ul>
</div>

</div>
