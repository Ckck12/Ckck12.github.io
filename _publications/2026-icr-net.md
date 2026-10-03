---
title: "ICR-Net: Robust Deepfake Detection under Temporal Corruption"
authors: "Chan Park, Hyeongjun Choi, Muhammad Shahid Muneer, Binh Minh Le, Simon S. Woo"
author_list:
  - name: "Chan Park"
    me: true
  - name: "Hyeongjun Choi"
  - name: "Muhammad Shahid Muneer"
  - name: "Binh Minh Le"
  - name: "Simon S. Woo"
    sup: "*"
affiliations:
  - "College of Computing and Informatics, Sungkyunkwan University, Suwon, South Korea"
author_notes: "* Corresponding author"
venue: "Pacific-Asia Conference on Knowledge Discovery and Data Mining (PAKDD) 2026"
status:
year: 2026
date: 2026-04-01   # year-level only; used for ordering
order: 5
teaser: icrnet_corruptions.jpg
teaser_hover: icrnet_method.jpg
paper_url: https://doi.org/10.1007/978-981-92-1465-5_24
code_url: https://github.com/Ckck12/ICR-Net
project_url: https://ckck12.github.io/ICR-Net/
slides_url:
tldr: "Per-frame integrity scoring and selective correction keep FF++ accuracy at or above 92.9% under each of 8 streaming corruptions (97.9% clean)."
tldr_list_ko: "프레임별 무결성 점수와 선택적 보정으로, FF++ 스트리밍 손상 8종 각각에서 정확도 92.9% 이상 유지 (손상 없음 97.9%)."
tldr_list_zh: "逐帧完整性评分与选择性校正：在 FF++ 上 8 种流媒体损坏下准确率均不低于 92.9%（无损坏 97.9%）。"
tldr_en:
  - "Live streams add packet loss, bit errors and heavy compression; these temporal corruptions break existing deepfake detectors."
  - "We built DF-TCB (8 streaming corruptions × 3 severities) and ICR-Net, which corrects only unreliable frames."
  - "On FF++, ICR-Net keeps 97.9% clean accuracy and at least 92.9% under each of the 8 corruptions."
tldr_ko:
  - "실시간 스트리밍의 패킷 손실, 비트 오류, 강한 압축 같은 시간적 손상은 기존 딥페이크 탐지기를 무너뜨립니다."
  - "DF-TCB(스트리밍 손상 8종 × 강도 3단계)와, 신뢰도가 낮은 프레임만 보정하는 ICR-Net을 만들었습니다."
  - "ICR-Net은 FF++에서 손상 없는 영상 97.9%, 손상 8종 각각에서 92.9% 이상의 정확도를 유지합니다."
tldr_zh:
  - "直播中的丢包、比特错误和强压缩等时序损坏，会让现有深度伪造检测器失效。"
  - "我们构建了 DF-TCB（8 种流媒体损坏 × 3 个严重程度）和只校正不可靠帧的 ICR-Net。"
  - "在 FF++ 上，ICR-Net 无损坏准确率为 97.9%，8 种损坏下均不低于 92.9%。"
excerpt: "DF-TCB benchmark and ICR-Net: deepfake detection that stays accurate under live-streaming temporal corruptions (PAKDD 2026, first author)."
meta_in_body: true
featured: true
collection: publications
---

{% include tldr.html %}

{% include pub-meta.html %}

<div class="proj-body">

<div class="kpis">
<div class="kpi"><span class="kpi__value">97.9%</span><span class="kpi__label lang-en">accuracy on clean FF++</span><span class="kpi__label lang-ko" lang="ko">손상 없는 FF++ 정확도</span><span class="kpi__label lang-zh" lang="zh-Hans">无损坏 FF++ 准确率</span></div>
<div class="kpi"><span class="kpi__value">&ge; 92.9%</span><span class="kpi__label lang-en">under every one of the 8 corruptions (FF++-C)</span><span class="kpi__label lang-ko" lang="ko">손상 8종 각각에서의 정확도 (FF++-C)</span><span class="kpi__label lang-zh" lang="zh-Hans">8 种损坏中每一种下的准确率（FF++-C）</span></div>
<div class="kpi"><span class="kpi__value">8 / 8</span><span class="kpi__label lang-en">cross-dataset (DFDC-C) corruption types where ICR-Net is most accurate</span><span class="kpi__label lang-ko" lang="ko">교차 데이터셋(DFDC-C) 손상 유형 중 최고 정확도를 기록한 유형 수</span><span class="kpi__label lang-zh" lang="zh-Hans">跨数据集（DFDC-C）中 ICR-Net 准确率最高的损坏类型数</span></div>
<div class="kpi"><span class="kpi__value">8 &times; 3</span><span class="kpi__label lang-en">streaming corruption types &times; severity levels in DF-TCB</span><span class="kpi__label lang-ko" lang="ko">DF-TCB의 스트리밍 손상 유형 &times; 강도 단계</span><span class="kpi__label lang-zh" lang="zh-Hans">DF-TCB 的流媒体损坏类型 &times; 严重程度</span></div>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">S</span>Situation</h2>
<p>Deepfakes increasingly reach people through live streams and video calls.</p>
<ul>
<li>Unstable networks corrupt video <em>over time</em>: dropped packets, flipped bits, black frames, heavy compression.</li>
<li>Detectors had been tested on spatial corruptions (noise, blur), not on these streaming failures.</li>
<li>We measured it: the video-based detector FTCN, trained on clean FaceForensics++ (FF++), dropped 38.72% from 99.65% clean accuracy.</li>
<li>On corrupted DFDC, all 9 detectors dropped sharply, most to near chance level.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">S</span>상황</h2>
<p>딥페이크는 점점 더 실시간 스트리밍과 영상 통화로 전달됩니다.</p>
<ul>
<li>불안정한 네트워크는 영상을 <em>시간 축으로</em> 망가뜨립니다. 패킷 손실, 비트 반전, 검은 프레임, 강한 압축이 생깁니다.</li>
<li>기존 탐지기는 노이즈·블러 같은 공간적 손상으로만 검증되었고, 이런 스트리밍 장애는 다루지 않았습니다.</li>
<li>직접 측정해 보니, 깨끗한 FaceForensics++(FF++)로 학습한 비디오 기반 탐지기 FTCN은 정확도 99.65%에서 38.72% 하락했습니다.</li>
<li>손상된 DFDC에서는 9개 탐지기 모두 크게 떨어졌고, 대부분 우연 수준에 가까워졌습니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">S</span>情境</h2>
<p>深度伪造越来越多地借助直播和视频通话传播。</p>
<ul>
<li>不稳定的网络会让视频<em>在时间维度上</em>受损：丢包、比特翻转、黑帧和强压缩。</li>
<li>现有检测器只在噪声、模糊等空间损坏下测试过，没有覆盖这类流媒体故障。</li>
<li>我们实测发现：在干净的 FaceForensics++（FF++）上训练的视频检测器 FTCN，准确率从 99.65% 下降了 38.72%。</li>
<li>在受损的 DFDC 上，9 个检测器全部大幅下降，多数接近随机水平。</li>
</ul>
</div>

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/detail/icrnet/fig1_scenario.jpg" alt="A deepfake video degraded by unstable web streaming, with examples of eight temporal corruption types" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 1.</strong> Deepfake videos with temporal corruptions in a real-world streaming scenario.</span><span class="lang-ko" lang="ko"><strong>그림 1.</strong> 실제 스트리밍 환경에서 시간적 손상이 섞인 딥페이크 영상.</span><span class="lang-zh" lang="zh-Hans"><strong>图 1.</strong> 真实流媒体场景中带有时序损坏的深度伪造视频。</span></figcaption>
</figure>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/icrnet/fig3_detector_robustness.png" alt="Bar charts of clean versus corrupted accuracy for nine existing detectors on FF++ and DFDC" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 3.</strong> Existing detectors trained on clean FF++. Top: intra-dataset (FF++ / FF++-C). Bottom: cross-dataset (DFDC / DFDC-C). Red labels: drop from clean to corrupted.</span><span class="lang-ko" lang="ko"><strong>그림 3.</strong> 깨끗한 FF++로 학습한 기존 탐지기. 위: 데이터셋 내부(FF++ / FF++-C). 아래: 교차 데이터셋(DFDC / DFDC-C). 빨간 숫자: 손상 시 정확도 하락폭.</span><span class="lang-zh" lang="zh-Hans"><strong>图 3.</strong> 在干净 FF++ 上训练的现有检测器。上：数据集内（FF++ / FF++-C）；下：跨数据集（DFDC / DFDC-C）。红色数字为损坏后的准确率降幅。</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">T</span>Task</h2>
<ul>
<li><strong>Measure:</strong> build a benchmark of realistic streaming failures on standard deepfake datasets.</li>
<li><strong>Fix:</strong> design a detector that stays accurate on partly corrupted clips, without losing clean accuracy.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">T</span>과제</h2>
<ul>
<li><strong>측정:</strong> 표준 딥페이크 데이터셋 위에 실제 스트리밍 장애를 재현한 벤치마크를 만든다.</li>
<li><strong>해결:</strong> 클립 일부가 손상되어도 정확하고, 손상 없는 영상의 정확도도 지키는 탐지기를 설계한다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">T</span>任务</h2>
<ul>
<li><strong>度量：</strong>在标准深度伪造数据集上构建模拟真实流媒体故障的基准。</li>
<li><strong>解决：</strong>设计一个检测器，片段部分受损时仍保持准确，且不牺牲无损坏视频上的准确率。</li>
</ul>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">A</span>Action</h2>
<p><strong>1. DF-TCB benchmark.</strong></p>
<ul>
<li>I simulated 8 temporal corruptions on FF++ and DFDC, each at 3 severity levels.</li>
<li>Types: black frames, motion blur, packet loss (Mininet network with UDP), bit errors, and H.264 / H.265 compression.</li>
<li>Compression uses both constant-rate-factor (CRF) and average-bitrate (ABR) modes.</li>
<li>By default, 16 consecutive frames of each 32-frame clip are corrupted.</li>
<li>This yields the corrupted splits FF++-C (intra-dataset) and DFDC-C (cross-dataset).</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">A</span>행동</h2>
<p><strong>1. DF-TCB 벤치마크.</strong></p>
<ul>
<li>FF++와 DFDC 위에 시간적 손상 8종을 각각 강도 3단계로 시뮬레이션했습니다.</li>
<li>유형: 검은 프레임, 모션 블러, 패킷 손실(Mininet 가상 네트워크 + UDP), 비트 오류, H.264 / H.265 압축.</li>
<li>압축은 CRF와 평균 비트레이트(ABR) 두 방식을 모두 사용합니다.</li>
<li>기본 설정에서는 32프레임 클립마다 연속 16프레임을 손상시킵니다.</li>
<li>그 결과 FF++-C(데이터셋 내부)와 DFDC-C(교차 데이터셋) 손상 분할을 만들었습니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">A</span>行动</h2>
<p><strong>1. DF-TCB 基准。</strong></p>
<ul>
<li>在 FF++ 和 DFDC 上模拟 8 种时序损坏，每种 3 个严重程度。</li>
<li>类型：黑帧、运动模糊、丢包（Mininet 虚拟网络 + UDP）、比特错误，以及 H.264 / H.265 压缩。</li>
<li>压缩同时采用 CRF 和平均码率（ABR）两种模式。</li>
<li>默认每个 32 帧片段中连续 16 帧受损。</li>
<li>由此得到损坏数据划分 FF++-C（数据集内）和 DFDC-C（跨数据集）。</li>
</ul>
</div>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/icrnet/fig2_dftcb.jpg" alt="Overview of DF-TCB: corruption types and severities, consecutive and distributed corrupted frames" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 2.</strong> DF-TCB: (a) eight temporal corruption types, evaluated with (b) consecutive and (c) distributed corrupted frames.</span><span class="lang-ko" lang="ko"><strong>그림 2.</strong> DF-TCB: (a) 시간적 손상 8종, (b) 연속 손상 프레임, (c) 분산 손상 프레임 시나리오.</span><span class="lang-zh" lang="zh-Hans"><strong>图 2.</strong> DF-TCB：(a) 8 种时序损坏，分别在 (b) 连续损坏帧和 (c) 分散损坏帧下评估。</span></figcaption>
</figure>

<div class="lang-en">
<p><strong>2. ICR-Net.</strong> Idea: a corrupted frame is one the model cannot predict from its neighbours. Correct only those frames; leave reliable ones untouched.</p>
<ul>
<li><strong>Integrity scoring:</strong> a GRU predicts each frame embedding from the preceding frames. A large error means low integrity.</li>
<li><strong>Selective correction:</strong> a 1D-CNN branch estimates a residual correction, scaled by how unreliable the frame is. Trustworthy frames keep their forensic cues.</li>
<li><strong>Contrastive alignment:</strong> clean and corrupted views of one video are pulled together; the opposite class is pushed apart. Features become corruption-invariant yet still separate real from fake.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<p><strong>2. ICR-Net.</strong> 핵심 아이디어: 손상된 프레임은 주변 프레임으로 예측되지 않는 프레임입니다. 그런 프레임만 보정하고, 신뢰할 수 있는 프레임은 그대로 둡니다.</p>
<ul>
<li><strong>무결성 점수:</strong> GRU가 이전 프레임들로 현재 프레임 임베딩을 예측합니다. 오차가 크면 무결성이 낮습니다.</li>
<li><strong>선택적 보정:</strong> 1D-CNN 브랜치가 잔차 보정값을 추정하고, 신뢰도가 낮은 프레임일수록 크게 적용합니다. 신뢰할 수 있는 프레임은 위조 단서를 유지합니다.</li>
<li><strong>대조 정렬:</strong> 같은 영상의 깨끗한 버전과 손상 버전은 가깝게, 반대 클래스는 멀게 학습합니다. 손상에 둔감하면서도 진짜/가짜를 구분하는 특징을 얻습니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<p><strong>2. ICR-Net。</strong>核心思路：受损帧就是无法由相邻帧预测的帧。只校正这些帧，可靠帧保持不变。</p>
<ul>
<li><strong>完整性评分：</strong>GRU 根据前面的帧预测当前帧嵌入；预测误差越大，完整性越低。</li>
<li><strong>选择性校正：</strong>1D-CNN 分支估计残差校正量，帧越不可靠，校正越强；可靠帧保留伪造线索。</li>
<li><strong>对比对齐：</strong>拉近同一视频的干净视图与受损视图，推远相反类别，得到对损坏不敏感、仍能区分真伪的特征。</li>
</ul>
</div>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/icrnet/fig4_icrnet.png" alt="ICR-Net architecture: paired encoding, temporal integrity and correction, contrastive alignment, classification" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 4.</strong> ICR-Net: (1) paired encoding of clean and corrupted clips, (2) GRU-based integrity assessment and selective correction, (3) clean–corrupted contrastive alignment, (4) classification.</span><span class="lang-ko" lang="ko"><strong>그림 4.</strong> ICR-Net 구조: (1) 깨끗한/손상 클립 쌍 인코딩, (2) GRU 기반 무결성 평가와 선택적 보정, (3) 깨끗한-손상 대조 정렬, (4) 분류.</span><span class="lang-zh" lang="zh-Hans"><strong>图 4.</strong> ICR-Net 结构：(1) 干净/受损片段成对编码，(2) 基于 GRU 的完整性评估与选择性校正，(3) 干净–受损对比对齐，(4) 分类。</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">R</span>Result</h2>
<ul>
<li>On clean FF++, ICR-Net keeps <strong>97.86%</strong> video-level accuracy.</li>
<li>It stays at or above <strong>92.93%</strong> under each of the 8 corruptions.</li>
<li>It is the most accurate model on 7 of the 8 corruption types.</li>
<li>Cross-dataset (trained on FF++, tested on corrupted DFDC): most accurate on <strong>all 8</strong> corruption types.</li>
<li>Every training objective helps: 95.67% on corrupted data vs. 86.38% for the classification-only baseline (Table 3).</li>
<li>Published at PAKDD 2026 (first author). A Korean patent application on the method was filed in 2026.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">R</span>결과</h2>
<ul>
<li>손상 없는 FF++에서 비디오 단위 정확도 <strong>97.86%</strong>를 유지합니다.</li>
<li>손상 8종 각각에서 <strong>92.93% 이상</strong>이며, 8종 중 7종에서 비교 모델 중 가장 높습니다.</li>
<li>교차 데이터셋(FF++ 학습, 손상된 DFDC 평가)에서는 손상 8종 <strong>모두</strong>에서 가장 높습니다.</li>
<li>모든 학습 목적함수가 기여합니다. 손상 데이터에서 95.67%로, 분류 손실만 쓴 기준 모델(86.38%)보다 높습니다(표 3).</li>
<li>PAKDD 2026 제1저자 논문. 이 방법으로 2026년 국내 특허를 출원했습니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">R</span>结果</h2>
<ul>
<li>在无损坏的 FF++ 上，视频级准确率为 <strong>97.86%</strong>。</li>
<li>8 种损坏下均不低于 <strong>92.93%</strong>，其中 7 种在对比模型中最高。</li>
<li>跨数据集（FF++ 训练，受损 DFDC 测试）：在全部 <strong>8</strong> 种损坏上准确率最高。</li>
<li>每个训练目标都有贡献：完整模型在受损数据上达 95.67%，仅用分类损失的基线为 86.38%（表 3）。</li>
<li>发表于 PAKDD 2026（第一作者）；该方法已于 2026 年提交韩国专利申请。</li>
</ul>
</div>

<p class="table-caption"><span class="lang-en">Table 1. Intra-dataset robustness (FF++ / FF++-C), video-level accuracy (%)</span><span class="lang-ko" lang="ko">표 1. 데이터셋 내부 강건성 (FF++ / FF++-C), 비디오 단위 정확도 (%)</span><span class="lang-zh" lang="zh-Hans">表 1. 数据集内鲁棒性（FF++ / FF++-C），视频级准确率（%）</span></p>
<div class="table-wrap">
<table>
<thead>
<tr><th><span class="lang-en">Model</span><span class="lang-ko" lang="ko">모델</span><span class="lang-zh" lang="zh-Hans">模型</span></th><th><span class="lang-en">Clean</span><span class="lang-ko" lang="ko">손상 없음</span><span class="lang-zh" lang="zh-Hans">无损坏</span></th><th>Black Frame</th><th>Motion Blur</th><th>Packet Loss</th><th>Bit Error</th><th>H.264 CRF</th><th>H.264 ABR</th><th>H.265 CRF</th><th>H.265 ABR</th></tr>
</thead>
<tbody>
<tr class="group"><td colspan="10"><span class="lang-en">Frame-based</span><span class="lang-ko" lang="ko">프레임 기반</span><span class="lang-zh" lang="zh-Hans">基于帧</span></td></tr>
<tr><td>FFD</td><td>98.14</td><td>85.89</td><td>91.23</td><td><u>90.98</u></td><td>90.83</td><td><u>91.80</u></td><td>88.70</td><td>91.44</td><td>90.63</td></tr>
<tr><td>F3-Net</td><td>98.10</td><td>85.84</td><td>88.16</td><td>90.22</td><td>90.47</td><td>90.47</td><td>88.33</td><td>89.21</td><td><u>92.05</u></td></tr>
<tr><td>SPSL</td><td><strong>98.29</strong></td><td>84.76</td><td>91.24</td><td>86.94</td><td>91.84</td><td>85.64</td><td>85.31</td><td>91.89</td><td>91.81</td></tr>
<tr><td>SRM</td><td>97.87</td><td>86.11</td><td>88.27</td><td>90.85</td><td><u>92.09</u></td><td>91.45</td><td>91.17</td><td>90.04</td><td>90.21</td></tr>
<tr><td>CORE</td><td><u>98.27</u></td><td>85.23</td><td>85.14</td><td>90.18</td><td>91.43</td><td>86.22</td><td>87.93</td><td>89.10</td><td>90.66</td></tr>
<tr><td>Effort</td><td>94.40</td><td>84.36</td><td>85.52</td><td>86.75</td><td>87.26</td><td>85.80</td><td>85.61</td><td>85.52</td><td>86.67</td></tr>
<tr class="group"><td colspan="10"><span class="lang-en">Video-based</span><span class="lang-ko" lang="ko">비디오 기반</span><span class="lang-zh" lang="zh-Hans">基于视频</span></td></tr>
<tr><td>FTCN</td><td>87.62</td><td>86.37</td><td>82.64</td><td>82.97</td><td>82.65</td><td>83.48</td><td>83.69</td><td>83.69</td><td>83.54</td></tr>
<tr><td>STIL</td><td>97.35</td><td>86.00</td><td><u>91.31</u></td><td>86.83</td><td>89.58</td><td>88.35</td><td>89.15</td><td>92.16</td><td>89.28</td></tr>
<tr><td>AltFreezing</td><td>97.21</td><td><u>91.54</u></td><td>90.71</td><td>85.73</td><td>90.77</td><td>89.40</td><td><strong>94.58</strong></td><td><u>92.55</u></td><td>90.47</td></tr>
<tr class="ours"><td>ICR-Net <span class="lang-en">(ours)</span><span class="lang-ko" lang="ko">(제안)</span><span class="lang-zh" lang="zh-Hans">（本文）</span></td><td>97.86</td><td><strong>94.92</strong></td><td><strong>94.88</strong></td><td><strong>96.03</strong></td><td><strong>96.67</strong></td><td><strong>96.55</strong></td><td><u>92.93</u></td><td><strong>97.50</strong></td><td><strong>95.83</strong></td></tr>
</tbody>
</table>
</div>

<p class="table-caption"><span class="lang-en">Table 2. Cross-dataset robustness (DFDC / DFDC-C), video-level accuracy (%)</span><span class="lang-ko" lang="ko">표 2. 교차 데이터셋 강건성 (DFDC / DFDC-C), 비디오 단위 정확도 (%)</span><span class="lang-zh" lang="zh-Hans">表 2. 跨数据集鲁棒性（DFDC / DFDC-C），视频级准确率（%）</span></p>
<div class="table-wrap">
<table>
<thead>
<tr><th><span class="lang-en">Model</span><span class="lang-ko" lang="ko">모델</span><span class="lang-zh" lang="zh-Hans">模型</span></th><th><span class="lang-en">Clean</span><span class="lang-ko" lang="ko">손상 없음</span><span class="lang-zh" lang="zh-Hans">无损坏</span></th><th>Black Frame</th><th>Motion Blur</th><th>Packet Loss</th><th>Bit Error</th><th>H.264 CRF</th><th>H.264 ABR</th><th>H.265 CRF</th><th>H.265 ABR</th></tr>
</thead>
<tbody>
<tr class="group"><td colspan="10"><span class="lang-en">Frame-based</span><span class="lang-ko" lang="ko">프레임 기반</span><span class="lang-zh" lang="zh-Hans">基于帧</span></td></tr>
<tr><td>FFD</td><td>61.41</td><td>50.32</td><td>50.12</td><td>50.49</td><td>50.22</td><td>50.38</td><td>50.19</td><td>51.02</td><td>49.73</td></tr>
<tr><td>F3-Net</td><td>72.02</td><td>49.93</td><td>49.77</td><td>50.01</td><td>49.92</td><td>50.13</td><td>49.68</td><td>49.33</td><td>49.59</td></tr>
<tr><td>SPSL</td><td>72.15</td><td>50.41</td><td>50.18</td><td>50.25</td><td>49.97</td><td>50.03</td><td>49.83</td><td>50.31</td><td>50.28</td></tr>
<tr><td>SRM</td><td>69.91</td><td>50.11</td><td>50.34</td><td>49.95</td><td>50.21</td><td>49.87</td><td>49.72</td><td>49.54</td><td>49.61</td></tr>
<tr><td>CORE</td><td>71.28</td><td>50.23</td><td>49.91</td><td>50.17</td><td>50.33</td><td>49.88</td><td>49.52</td><td>49.78</td><td>49.44</td></tr>
<tr><td>Effort</td><td><strong>79.85</strong></td><td><u>55.13</u></td><td><u>54.88</u></td><td><u>54.02</u></td><td><u>54.71</u></td><td><u>54.96</u></td><td><u>53.87</u></td><td>55.43</td><td><u>53.69</u></td></tr>
<tr class="group"><td colspan="10"><span class="lang-en">Video-based</span><span class="lang-ko" lang="ko">비디오 기반</span><span class="lang-zh" lang="zh-Hans">基于视频</span></td></tr>
<tr><td>FTCN</td><td>61.21</td><td>53.11</td><td>52.89</td><td>53.39</td><td>54.12</td><td>53.02</td><td>52.07</td><td>53.34</td><td>53.55</td></tr>
<tr><td>STIL</td><td>67.71</td><td>49.15</td><td>49.98</td><td>48.50</td><td>50.05</td><td>48.61</td><td>48.01</td><td>50.24</td><td>49.82</td></tr>
<tr><td>AltFreezing</td><td>72.95</td><td>51.82</td><td>50.73</td><td>51.23</td><td>50.15</td><td>51.35</td><td>49.22</td><td><u>57.11</u></td><td>51.47</td></tr>
<tr class="ours"><td>ICR-Net <span class="lang-en">(ours)</span><span class="lang-ko" lang="ko">(제안)</span><span class="lang-zh" lang="zh-Hans">（本文）</span></td><td><u>78.56</u></td><td><strong>58.14</strong></td><td><strong>59.23</strong></td><td><strong>59.51</strong></td><td><strong>58.98</strong></td><td><strong>59.12</strong></td><td><strong>57.98</strong></td><td><strong>59.88</strong></td><td><strong>58.33</strong></td></tr>
</tbody>
</table>
</div>
<p class="table-note"><span class="lang-en">All models use the same corruption-aware training: each training video gives a clean clip and one clip with a corruption at severity level 3. Corrupted columns average the three severity levels. <strong>Bold</strong> = best, <u>underline</u> = second best, as marked in the paper.</span><span class="lang-ko" lang="ko">모든 모델은 동일한 손상 인지 학습을 사용합니다. 학습 영상마다 깨끗한 클립 1개와 강도 3단계 손상 클립 1개를 씁니다. 손상 열은 강도 3단계의 평균입니다. <strong>굵게</strong> = 1위, <u>밑줄</u> = 2위(논문 표기 그대로).</span><span class="lang-zh" lang="zh-Hans">所有模型采用相同的损坏感知训练：每个训练视频提供 1 个干净片段和 1 个严重程度为 3 的损坏片段。损坏列为三个严重程度的平均值。<strong>粗体</strong> = 最佳，<u>下划线</u> = 次佳（按论文标注）。</span></p>

<p class="table-caption"><span class="lang-en">Table 3. Ablation of training objectives (accuracy, %)</span><span class="lang-ko" lang="ko">표 3. 학습 목적함수 제거 실험 (정확도, %)</span><span class="lang-zh" lang="zh-Hans">表 3. 训练目标消融实验（准确率，%）</span></p>
<div class="table-wrap">
<table>
<thead>
<tr><th>L<sub>pred</sub></th><th>L<sub>reg</sub></th><th>L<sub>con</sub></th><th><span class="lang-en">Clean</span><span class="lang-ko" lang="ko">손상 없음</span><span class="lang-zh" lang="zh-Hans">无损坏</span></th><th><span class="lang-en">Corrupted</span><span class="lang-ko" lang="ko">손상</span><span class="lang-zh" lang="zh-Hans">损坏</span></th></tr>
</thead>
<tbody>
<tr><td>&times;</td><td>&times;</td><td>&times;</td><td>91.57</td><td>86.38</td></tr>
<tr><td>&#10003;</td><td>&times;</td><td>&times;</td><td>96.50</td><td>88.38</td></tr>
<tr><td>&times;</td><td>&#10003;</td><td>&times;</td><td>82.54</td><td>77.61</td></tr>
<tr><td>&times;</td><td>&times;</td><td>&#10003;</td><td>95.31</td><td>85.20</td></tr>
<tr><td>&#10003;</td><td>&#10003;</td><td>&times;</td><td>97.33</td><td>89.96</td></tr>
<tr><td>&times;</td><td>&#10003;</td><td>&#10003;</td><td>84.33</td><td>78.14</td></tr>
<tr><td>&#10003;</td><td>&times;</td><td>&#10003;</td><td>96.55</td><td>91.15</td></tr>
<tr class="ours"><td>&#10003;</td><td>&#10003;</td><td>&#10003;</td><td><strong>97.86</strong></td><td><strong>95.67</strong></td></tr>
</tbody>
</table>
</div>
<p class="table-note"><span class="lang-en">Row 1 (classification loss only) is the baseline. L<sub>reg</sub> alone hurts accuracy, because the model can trivially minimize the integrity scores. Combined with the other terms, it gives the best result.</span><span class="lang-ko" lang="ko">1행(분류 손실만 사용)이 기준 모델입니다. L<sub>reg</sub>만 쓰면 모델이 무결성 점수를 쉽게 최소화해 정확도가 떨어집니다. 다른 항과 함께 쓰면 가장 좋은 결과를 냅니다.</span><span class="lang-zh" lang="zh-Hans">第 1 行（仅分类损失）为基线。单独使用 L<sub>reg</sub> 会降低准确率，因为模型可以轻易把完整性分数降到最低；与其他项结合时效果最佳。</span></p>

<h2 class="sec-h">BibTeX</h2>
{% raw %}<pre class="bibtex">@inproceedings{park2026icrnet,
  title     = {{ICR-Net}: Robust Deepfake Detection under Temporal Corruption},
  author    = {Park, Chan and Choi, Hyeongjun and Muneer, Muhammad Shahid
               and Le, Binh Minh and Woo, Simon S.},
  booktitle = {Proceedings of the 30th Pacific-Asia Conference on Knowledge
               Discovery and Data Mining (PAKDD 2026)},
  year      = {2026},
  doi       = {10.1007/978-981-92-1465-5_24}
}</pre>{% endraw %}

</div>
