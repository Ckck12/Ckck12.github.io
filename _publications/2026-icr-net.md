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
tldr: "Predicts per-frame reliability and selectively corrects corrupted features, keeping video-level accuracy at or above 92.9% on FF++ under every one of 8 live-streaming corruptions (97.9% clean)."
tldr_en:
  - "Live-streamed deepfakes suffer packet loss, bit errors and heavy compression, and these temporal corruptions break existing detectors."
  - "We built DF-TCB (8 streaming corruptions × 3 severities) and ICR-Net, which scores each frame's integrity and corrects only unreliable frames."
  - "On FF++, ICR-Net keeps 97.9% accuracy on clean video and at least 92.9% under each of the 8 corruptions."
tldr_ko:
  - "실시간 스트리밍 딥페이크에는 패킷 손실, 비트 오류, 강한 압축 같은 시간적 손상이 생기고, 기존 탐지기는 여기에 크게 흔들립니다."
  - "스트리밍 손상 8종 × 강도 3단계의 벤치마크 DF-TCB와, 프레임별 신뢰도를 추정해 신뢰도가 낮은 프레임만 보정하는 ICR-Net을 만들었습니다."
  - "ICR-Net은 FF++에서 깨끗한 영상 97.9%, 손상 8종 각각에서 92.9% 이상의 정확도를 유지합니다."
excerpt: "DF-TCB benchmark and ICR-Net: deepfake detection that stays accurate under live-streaming temporal corruptions (PAKDD 2026, first author)."
meta_in_body: true
featured: true
collection: publications
---

{% include lang-toggle.html %}

{% include tldr.html %}

{% include pub-meta.html %}

<div class="proj-body">

<div class="kpis">
<div class="kpi"><span class="kpi__value">97.9%</span><span class="kpi__label lang-en">accuracy on clean FF++</span><span class="kpi__label lang-ko" lang="ko">깨끗한 FF++ 정확도</span></div>
<div class="kpi"><span class="kpi__value">&ge; 92.9%</span><span class="kpi__label lang-en">under every one of the 8 corruptions (FF++-C)</span><span class="kpi__label lang-ko" lang="ko">손상 8종 각각에서의 정확도 (FF++-C)</span></div>
<div class="kpi"><span class="kpi__value">8 / 8</span><span class="kpi__label lang-en">DFDC-C corruption types where ICR-Net is the most accurate (cross-dataset)</span><span class="kpi__label lang-ko" lang="ko">교차 데이터셋(DFDC-C) 손상 유형 중 최고 정확도를 기록한 유형 수</span></div>
<div class="kpi"><span class="kpi__value">8 &times; 3</span><span class="kpi__label lang-en">streaming corruption types &times; severity levels in DF-TCB</span><span class="kpi__label lang-ko" lang="ko">DF-TCB의 스트리밍 손상 유형 &times; 강도 단계</span></div>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">S</span>Situation</h2>
<p>Deepfakes increasingly reach people through live streams and video calls. On the way, unstable networks corrupt the video <em>over time</em>: packets are dropped, bits flip, frames go black, and encoders compress aggressively. Deepfake detectors had been tested against spatial corruptions (noise, blur on single images), but not against these temporal, streaming-style failures.</p>
<p>When we measured it, existing detectors turned out to be fragile. Trained on clean FaceForensics++ (FF++), the video-based detector FTCN dropped by 38.72% from its 99.65% clean accuracy, and on corrupted DFDC videos all nine detectors dropped sharply, most of them to near chance level.</p>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">S</span>상황 (Situation)</h2>
<p>딥페이크는 점점 더 실시간 스트리밍과 영상 통화를 통해 전달됩니다. 이 과정에서 불안정한 네트워크는 영상을 <em>시간 축으로</em> 망가뜨립니다. 패킷이 빠지고, 비트가 뒤집히고, 프레임이 검게 나오고, 인코더는 영상을 강하게 압축합니다. 기존 딥페이크 탐지기는 노이즈나 블러 같은 공간적 손상에 대해서는 검증되어 왔지만, 이런 스트리밍형 시간적 손상에 대해서는 거의 검증되지 않았습니다.</p>
<p>직접 측정해 보니 기존 탐지기는 매우 취약했습니다. 깨끗한 FaceForensics++(FF++)로 학습한 비디오 기반 탐지기 FTCN은 깨끗한 영상 정확도 99.65%에서 38.72% 하락했고, 손상된 DFDC 영상에서는 9개 탐지기 모두 정확도가 크게 떨어져 대부분 우연 수준에 가까워졌습니다.</p>
</div>

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/detail/icrnet/fig1_scenario.jpg" alt="A deepfake video degraded by unstable web streaming, with examples of eight temporal corruption types" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 1.</strong> Deepfake videos with temporal corruptions in a real-world streaming scenario.</span><span class="lang-ko" lang="ko"><strong>그림 1.</strong> 실제 스트리밍 환경에서 시간적 손상이 섞인 딥페이크 영상.</span></figcaption>
</figure>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/icrnet/fig3_detector_robustness.png" alt="Bar charts of clean versus corrupted accuracy for nine existing detectors on FF++ and DFDC" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 3.</strong> Existing detectors trained on clean FF++ and evaluated intra-dataset (FF++ / FF++-C, top) and cross-dataset (DFDC / DFDC-C, bottom). Red labels give the drop from clean to corrupted video.</span><span class="lang-ko" lang="ko"><strong>그림 3.</strong> 깨끗한 FF++로 학습한 기존 탐지기를 데이터셋 내부(FF++ / FF++-C, 위)와 교차 데이터셋(DFDC / DFDC-C, 아래)에서 평가한 결과. 빨간 숫자는 깨끗한 영상 대비 손상 영상에서의 정확도 하락폭입니다.</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">T</span>Task</h2>
<ul>
<li><strong>Measure the problem:</strong> build a benchmark that reproduces realistic streaming failures on standard deepfake datasets.</li>
<li><strong>Fix it:</strong> design a detector that stays accurate when part of a clip is corrupted, without giving up accuracy on clean videos.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">T</span>과제 (Task)</h2>
<ul>
<li><strong>문제 측정:</strong> 표준 딥페이크 데이터셋 위에서 실제 스트리밍 장애를 재현하는 벤치마크를 만든다.</li>
<li><strong>문제 해결:</strong> 클립의 일부 프레임이 손상되어도 정확도를 유지하면서, 깨끗한 영상에서의 정확도는 잃지 않는 탐지기를 설계한다.</li>
</ul>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">A</span>Action</h2>
<p><strong>1. DF-TCB benchmark.</strong> On FF++ and DFDC, I simulated 8 temporal corruptions at 3 severity levels each: black frames, motion blur, packet loss (over a controlled Mininet network with UDP), bit errors, and H.264 / H.265 compression in both constant-rate-factor and average-bitrate modes. By default 16 consecutive frames of each 32-frame clip are corrupted. This gives the corrupted splits FF++-C (intra-dataset) and DFDC-C (cross-dataset).</p>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">A</span>행동 (Action)</h2>
<p><strong>1. DF-TCB 벤치마크.</strong> FF++와 DFDC 위에 시간적 손상 8종을 각각 3단계 강도로 시뮬레이션했습니다. 검은 프레임, 모션 블러, 패킷 손실(Mininet 가상 네트워크와 UDP로 재현), 비트 오류, 그리고 H.264 / H.265 압축(CRF와 평균 비트레이트 두 방식)입니다. 기본 설정에서는 32프레임 클립마다 연속 16프레임을 손상시킵니다. 이렇게 데이터셋 내부 평가용 FF++-C와 교차 데이터셋 평가용 DFDC-C를 만들었습니다.</p>
</div>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/icrnet/fig2_dftcb.jpg" alt="Overview of DF-TCB: corruption types and severities, consecutive and distributed corrupted frames" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 2.</strong> DF-TCB: (a) eight temporal corruption types, evaluated with (b) consecutive and (c) distributed corrupted frames.</span><span class="lang-ko" lang="ko"><strong>그림 2.</strong> DF-TCB 개요: (a) 시간적 손상 8종, (b) 연속 손상 프레임과 (c) 분산 손상 프레임 시나리오.</span></figcaption>
</figure>

<div class="lang-en">
<p><strong>2. ICR-Net.</strong> The idea: a corrupted frame is one that the model cannot predict from its neighbours, so correct only those frames and leave reliable frames untouched.</p>
<ul>
<li><strong>Integrity scoring:</strong> a GRU predicts each frame embedding from the preceding frames; a large prediction error means low integrity.</li>
<li><strong>Selective correction:</strong> a 1D-CNN branch estimates a residual correction, which is applied in proportion to how unreliable the frame is, so trustworthy frames keep their forensic cues.</li>
<li><strong>Contrastive alignment:</strong> clean and corrupted views of the same video are pulled together and the opposite class is pushed apart, giving features that are corruption-invariant but still separate real from fake.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<p><strong>2. ICR-Net.</strong> 핵심 아이디어는 “손상된 프레임은 주변 프레임으로부터 예측되지 않는 프레임”이라는 것입니다. 그래서 그런 프레임만 보정하고 신뢰할 수 있는 프레임은 그대로 둡니다.</p>
<ul>
<li><strong>무결성 점수:</strong> GRU가 이전 프레임들로부터 현재 프레임 임베딩을 예측하고, 예측 오차가 크면 무결성이 낮다고 판단합니다.</li>
<li><strong>선택적 보정:</strong> 1D-CNN 브랜치가 잔차 보정값을 추정하고, 프레임의 신뢰도가 낮을수록 더 크게 적용합니다. 신뢰할 수 있는 프레임은 위조 단서를 그대로 유지합니다.</li>
<li><strong>대조 정렬:</strong> 같은 영상의 깨끗한 버전과 손상된 버전은 가깝게, 반대 클래스는 멀게 학습해 손상에는 둔감하면서도 진짜/가짜는 잘 구분하는 특징을 얻습니다.</li>
</ul>
</div>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/icrnet/fig4_icrnet.png" alt="ICR-Net architecture: paired encoding, temporal integrity and correction, contrastive alignment, classification" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 4.</strong> ICR-Net: (1) paired encoding of clean and corrupted clips, (2) GRU-based integrity assessment and selective correction, (3) clean–corrupted contrastive alignment, (4) classification.</span><span class="lang-ko" lang="ko"><strong>그림 4.</strong> ICR-Net 구조: (1) 깨끗한/손상된 클립 쌍 인코딩, (2) GRU 기반 무결성 평가와 선택적 보정, (3) 깨끗한-손상 대조 정렬, (4) 분류.</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">R</span>Result</h2>
<ul>
<li>On clean FF++, ICR-Net keeps <strong>97.86%</strong> video-level accuracy; under each of the 8 corruptions it stays at or above <strong>92.93%</strong> and is the most accurate model on 7 of the 8 corruption types.</li>
<li>Cross-dataset (trained on FF++, tested on corrupted DFDC), it is the most accurate model on <strong>all 8</strong> corruption types.</li>
<li>Each training objective contributes: the full model reaches 95.67% on corrupted data vs. 86.38% for the classification-only baseline (Table 3).</li>
<li>Published at PAKDD 2026 (first author); a Korean patent application on the method was filed in 2026.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">R</span>결과 (Result)</h2>
<ul>
<li>깨끗한 FF++에서 비디오 단위 정확도 <strong>97.86%</strong>를 유지했고, 손상 8종 각각에서 <strong>92.93% 이상</strong>을 기록했으며, 8종 중 7종에서 비교 모델 중 가장 높았습니다.</li>
<li>교차 데이터셋 평가(FF++로 학습, 손상된 DFDC로 평가)에서는 손상 8종 <strong>모두</strong>에서 가장 높은 정확도를 보였습니다.</li>
<li>학습 목적함수 각각이 성능에 기여합니다. 전체 모델은 손상 데이터에서 95.67%로, 분류 손실만 쓴 기준 모델(86.38%)보다 높았습니다(표 3).</li>
<li>PAKDD 2026에 제1저자로 게재되었고, 이 방법으로 2026년 국내 특허를 출원했습니다.</li>
</ul>
</div>

<p class="table-caption"><span class="lang-en">Table 1. Intra-dataset robustness (FF++ / FF++-C), video-level accuracy (%)</span><span class="lang-ko" lang="ko">표 1. 데이터셋 내부 강건성 (FF++ / FF++-C), 비디오 단위 정확도 (%)</span></p>
<div class="table-wrap">
<table>
<thead>
<tr><th>Model</th><th>Clean</th><th>Black Frame</th><th>Motion Blur</th><th>Packet Loss</th><th>Bit Error</th><th>H.264 CRF</th><th>H.264 ABR</th><th>H.265 CRF</th><th>H.265 ABR</th></tr>
</thead>
<tbody>
<tr class="group"><td colspan="10">Frame-based</td></tr>
<tr><td>FFD</td><td>98.14</td><td>85.89</td><td>91.23</td><td><u>90.98</u></td><td>90.83</td><td><u>91.80</u></td><td>88.70</td><td>91.44</td><td>90.63</td></tr>
<tr><td>F3-Net</td><td>98.10</td><td>85.84</td><td>88.16</td><td>90.22</td><td>90.47</td><td>90.47</td><td>88.33</td><td>89.21</td><td><u>92.05</u></td></tr>
<tr><td>SPSL</td><td><strong>98.29</strong></td><td>84.76</td><td>91.24</td><td>86.94</td><td>91.84</td><td>85.64</td><td>85.31</td><td>91.89</td><td>91.81</td></tr>
<tr><td>SRM</td><td>97.87</td><td>86.11</td><td>88.27</td><td>90.85</td><td><u>92.09</u></td><td>91.45</td><td>91.17</td><td>90.04</td><td>90.21</td></tr>
<tr><td>CORE</td><td><u>98.27</u></td><td>85.23</td><td>85.14</td><td>90.18</td><td>91.43</td><td>86.22</td><td>87.93</td><td>89.10</td><td>90.66</td></tr>
<tr><td>Effort</td><td>94.40</td><td>84.36</td><td>85.52</td><td>86.75</td><td>87.26</td><td>85.80</td><td>85.61</td><td>85.52</td><td>86.67</td></tr>
<tr class="group"><td colspan="10">Video-based</td></tr>
<tr><td>FTCN</td><td>87.62</td><td>86.37</td><td>82.64</td><td>82.97</td><td>82.65</td><td>83.48</td><td>83.69</td><td>83.69</td><td>83.54</td></tr>
<tr><td>STIL</td><td>97.35</td><td>86.00</td><td><u>91.31</u></td><td>86.83</td><td>89.58</td><td>88.35</td><td>89.15</td><td>92.16</td><td>89.28</td></tr>
<tr><td>AltFreezing</td><td>97.21</td><td><u>91.54</u></td><td>90.71</td><td>85.73</td><td>90.77</td><td>89.40</td><td><strong>94.58</strong></td><td><u>92.55</u></td><td>90.47</td></tr>
<tr class="ours"><td>ICR-Net (ours)</td><td>97.86</td><td><strong>94.92</strong></td><td><strong>94.88</strong></td><td><strong>96.03</strong></td><td><strong>96.67</strong></td><td><strong>96.55</strong></td><td><u>92.93</u></td><td><strong>97.50</strong></td><td><strong>95.83</strong></td></tr>
</tbody>
</table>
</div>

<p class="table-caption"><span class="lang-en">Table 2. Cross-dataset robustness (DFDC / DFDC-C), video-level accuracy (%)</span><span class="lang-ko" lang="ko">표 2. 교차 데이터셋 강건성 (DFDC / DFDC-C), 비디오 단위 정확도 (%)</span></p>
<div class="table-wrap">
<table>
<thead>
<tr><th>Model</th><th>Clean</th><th>Black Frame</th><th>Motion Blur</th><th>Packet Loss</th><th>Bit Error</th><th>H.264 CRF</th><th>H.264 ABR</th><th>H.265 CRF</th><th>H.265 ABR</th></tr>
</thead>
<tbody>
<tr class="group"><td colspan="10">Frame-based</td></tr>
<tr><td>FFD</td><td>61.41</td><td>50.32</td><td>50.12</td><td>50.49</td><td>50.22</td><td>50.38</td><td>50.19</td><td>51.02</td><td>49.73</td></tr>
<tr><td>F3-Net</td><td>72.02</td><td>49.93</td><td>49.77</td><td>50.01</td><td>49.92</td><td>50.13</td><td>49.68</td><td>49.33</td><td>49.59</td></tr>
<tr><td>SPSL</td><td>72.15</td><td>50.41</td><td>50.18</td><td>50.25</td><td>49.97</td><td>50.03</td><td>49.83</td><td>50.31</td><td>50.28</td></tr>
<tr><td>SRM</td><td>69.91</td><td>50.11</td><td>50.34</td><td>49.95</td><td>50.21</td><td>49.87</td><td>49.72</td><td>49.54</td><td>49.61</td></tr>
<tr><td>CORE</td><td>71.28</td><td>50.23</td><td>49.91</td><td>50.17</td><td>50.33</td><td>49.88</td><td>49.52</td><td>49.78</td><td>49.44</td></tr>
<tr><td>Effort</td><td><strong>79.85</strong></td><td><u>55.13</u></td><td><u>54.88</u></td><td><u>54.02</u></td><td><u>54.71</u></td><td><u>54.96</u></td><td><u>53.87</u></td><td>55.43</td><td><u>53.69</u></td></tr>
<tr class="group"><td colspan="10">Video-based</td></tr>
<tr><td>FTCN</td><td>61.21</td><td>53.11</td><td>52.89</td><td>53.39</td><td>54.12</td><td>53.02</td><td>52.07</td><td>53.34</td><td>53.55</td></tr>
<tr><td>STIL</td><td>67.71</td><td>49.15</td><td>49.98</td><td>48.50</td><td>50.05</td><td>48.61</td><td>48.01</td><td>50.24</td><td>49.82</td></tr>
<tr><td>AltFreezing</td><td>72.95</td><td>51.82</td><td>50.73</td><td>51.23</td><td>50.15</td><td>51.35</td><td>49.22</td><td><u>57.11</u></td><td>51.47</td></tr>
<tr class="ours"><td>ICR-Net (ours)</td><td><u>78.56</u></td><td><strong>58.14</strong></td><td><strong>59.23</strong></td><td><strong>59.51</strong></td><td><strong>58.98</strong></td><td><strong>59.12</strong></td><td><strong>57.98</strong></td><td><strong>59.88</strong></td><td><strong>58.33</strong></td></tr>
</tbody>
</table>
</div>
<p class="table-note"><span class="lang-en">All models use the same corruption-aware training (each training video contributes a clean clip and a clip with one corruption at severity level 3). Corrupted columns are the mean over the three severity levels. <strong>Bold</strong> = best, <u>underline</u> = second best, as marked in the paper.</span><span class="lang-ko" lang="ko">모든 모델은 동일한 손상 인지 학습(학습 영상마다 깨끗한 클립 1개와 강도 3단계 손상 클립 1개)을 사용했습니다. 손상 열은 강도 3단계의 평균입니다. <strong>굵게</strong> = 1위, <u>밑줄</u> = 2위(논문 표기 그대로).</span></p>

<p class="table-caption"><span class="lang-en">Table 3. Ablation of training objectives (accuracy, %)</span><span class="lang-ko" lang="ko">표 3. 학습 목적함수 제거 실험 (정확도, %)</span></p>
<div class="table-wrap">
<table>
<thead>
<tr><th>L<sub>pred</sub></th><th>L<sub>reg</sub></th><th>L<sub>con</sub></th><th>Clean</th><th>Corrupted</th></tr>
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
<p class="table-note"><span class="lang-en">Row 1 (classification loss only) is the baseline. L<sub>reg</sub> alone hurts accuracy because the model can trivially minimize the integrity scores; combined with the other terms it gives the best result.</span><span class="lang-ko" lang="ko">1행(분류 손실만 사용)이 기준 모델입니다. L<sub>reg</sub>만 단독으로 쓰면 모델이 무결성 점수를 쉽게 최소화해 버려 정확도가 떨어지지만, 다른 항과 함께 쓰면 가장 좋은 결과를 냅니다.</span></p>

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
