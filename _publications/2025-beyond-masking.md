---
title: "Beyond Masking: Landmark-based Representation Learning and Knowledge-Distillation for Audio-Visual Deepfake Detection"
authors: "Chan Park, Muhammad Shahid Muneer, Simon S. Woo"
author_list:
  - name: "Chan Park"
    sup: "1"
    me: true
  - name: "Muhammad Shahid Muneer"
    sup: "2"
  - name: "Simon S. Woo"
    sup: "2,*"
affiliations:
  - "<sup>1</sup>Department of Artificial Intelligence, Sungkyunkwan University, Suwon, Republic of Korea"
  - "<sup>2</sup>Department of Computer Science &amp; Engineering, Sungkyunkwan University, Suwon, Republic of Korea"
author_notes: "* Corresponding author"
venue: "ACM International Conference on Information and Knowledge Management (CIKM) 2025, Short Paper"
status:
year: 2025
date: 2025-11-01   # year-level only; used for ordering
order: 6
teaser: cikm2025.png
teaser_hover:
paper_url: https://doi.org/10.1145/3746252.3760853
code_url: https://github.com/Ckck12/Beyond-Masking
project_url: https://ckck12.github.io/Beyond-Masking/
slides_url:
tldr: "Uses facial landmarks as a distillation target so the video and audio encoders focus on facial geometry, plus audio-visual temporal alignment; highest cross-dataset accuracy among the compared methods on DFDC and on 500+ real-world web deepfakes, with 32.4 M parameters."
tldr_en:
  - "Audio-visual deepfake detectors often look at the background instead of the face, and fail on real deepfakes from social media."
  - "Beyond Masking distills facial-landmark geometry into both the video and the audio encoder, and aligns the two modalities over time."
  - "Cross-dataset, it reaches 78.12% on DFDC and 75.12% on 500+ web deepfakes, the highest among the compared methods, with 32.4 M parameters."
tldr_ko:
  - "오디오-비주얼 딥페이크 탐지기는 얼굴 대신 배경을 보는 경우가 많아, 소셜 미디어의 실제 딥페이크 앞에서 성능이 크게 떨어집니다."
  - "Beyond Masking은 얼굴 랜드마크의 기하 정보를 비디오·오디오 인코더 양쪽에 증류하고, 두 모달리티를 시간 축에서 정렬합니다."
  - "교차 데이터셋에서 DFDC 78.12%, 웹 딥페이크 500여 개 75.12%로 비교 방법 중 가장 높았고, 파라미터는 3,240만 개입니다."
excerpt: "Landmark-based distillation and audio-visual temporal alignment for deepfake detection that generalizes to real-world web deepfakes (CIKM 2025 short paper, first author)."
meta_in_body: true
featured: true
collection: publications
---

{% include lang-toggle.html %}

{% include tldr.html %}

{% include pub-meta.html %}

<div class="proj-body">

<div class="kpis">
<div class="kpi"><span class="kpi__value">78.12%</span><span class="kpi__label lang-en">cross-dataset accuracy on unseen DFDC (AUC 78.82)</span><span class="kpi__label lang-ko" lang="ko">처음 보는 DFDC에서의 교차 데이터셋 정확도 (AUC 78.82)</span></div>
<div class="kpi"><span class="kpi__value">75.12%</span><span class="kpi__label lang-en">accuracy on 500+ real-world web deepfakes (AUC 75.65)</span><span class="kpi__label lang-ko" lang="ko">실제 웹 딥페이크 500여 개에서의 정확도 (AUC 75.65)</span></div>
<div class="kpi"><span class="kpi__value">92.38%</span><span class="kpi__label lang-en">intra-dataset accuracy on FakeAVCeleb (AUC 93.25)</span><span class="kpi__label lang-ko" lang="ko">FakeAVCeleb 데이터셋 내부 정확도 (AUC 93.25)</span></div>
<div class="kpi"><span class="kpi__value">32.4 M</span><span class="kpi__label lang-en">parameters, fewer than every multimodal baseline with a reported size</span><span class="kpi__label lang-ko" lang="ko">파라미터 수 (크기가 보고된 모든 멀티모달 기준 모델보다 적음)</span></div>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">S</span>Situation</h2>
<p>Audio-visual deepfakes, where the face, the voice, or both are fabricated, are spreading on social media, including in political propaganda. Detectors trained on academic datasets such as DFDC, FakeAVCeleb and KoDF report strong numbers, but they degrade sharply and raise many false alarms on real-world deepfakes.</p>
<p>One reason is <em>what</em> the models look at. Grad-CAM maps show that a standard backbone spreads its attention over the background, hair and neck, which are spurious cues tied to how a particular dataset was generated, rather than the face and mouth where manipulation actually happens.</p>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">S</span>상황 (Situation)</h2>
<p>얼굴이나 목소리, 혹은 둘 다를 조작한 오디오-비주얼 딥페이크가 정치 선전을 포함해 소셜 미디어에서 빠르게 퍼지고 있습니다. DFDC, FakeAVCeleb, KoDF 같은 학술 데이터셋으로 학습한 탐지기는 높은 수치를 보고하지만, 실제 딥페이크 앞에서는 성능이 크게 떨어지고 오탐도 많아집니다.</p>
<p>원인 중 하나는 모델이 <em>어디를</em> 보느냐입니다. Grad-CAM으로 확인하면 일반적인 백본은 조작이 실제로 일어나는 얼굴과 입 대신, 배경·머리카락·목처럼 특정 데이터셋의 생성 방식에 묶인 부수적인 단서에 주의를 분산시킵니다.</p>
</div>

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/detail/bm/fig1_realworld_samples.jpg" alt="Grid of eight audio-visual deepfake samples collected from social media" loading="lazy">
<figcaption><span class="lang-en"><strong>Figure 1.</strong> Audio-visual deepfake samples collected from social media platforms such as Meta, YouTube, X and Reddit. These form the Real-World (RW) test set.</span><span class="lang-ko" lang="ko"><strong>그림 1.</strong> Meta, YouTube, X, Reddit 등 소셜 미디어에서 수집한 오디오-비주얼 딥페이크 예시. 이 영상들로 실제 환경(RW) 평가셋을 구성했습니다.</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">T</span>Task</h2>
<ul>
<li>Force both the video and the audio encoder to attend to <strong>facial geometry</strong> (eyes, lips, face shape) instead of the background.</li>
<li>Check that what the mouth does and what the audio says stay <strong>synchronized over time</strong>.</li>
<li>Show that this generalizes to <strong>unseen datasets and real deepfakes from the web</strong>, while keeping the model small.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">T</span>과제 (Task)</h2>
<ul>
<li>비디오 인코더와 오디오 인코더가 배경 대신 <strong>얼굴의 기하 구조</strong>(눈, 입술, 얼굴 형태)에 집중하도록 만든다.</li>
<li>입 모양과 음성이 <strong>시간적으로 맞물려 있는지</strong>를 확인한다.</li>
<li>모델을 가볍게 유지하면서 <strong>처음 보는 데이터셋과 웹의 실제 딥페이크</strong>로 일반화됨을 보인다.</li>
</ul>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">A</span>Action</h2>
<p><strong>1. Landmarks as a teacher, not a mask.</strong> Instead of masking the input, we extract 478 facial landmarks per frame with MediaPipe and turn them into a target representation with a small landmark projector. A video landmark predictor and an audio landmark predictor are trained to reproduce this representation from the encoders' features, using a KL-divergence loss (an idea motivated by I-JEPA). To reconstruct facial geometry, the encoders have to look at the face, including the audio encoder, which must infer mouth shape from sound. We call this Landmark-based Distillation (LBD).</p>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">A</span>행동 (Action)</h2>
<p><strong>1. 마스크가 아니라 교사로서의 랜드마크.</strong> 입력을 가리는 대신, MediaPipe로 프레임마다 478개의 얼굴 랜드마크를 추출하고 작은 랜드마크 프로젝터로 목표 표현을 만듭니다. 비디오 랜드마크 예측기와 오디오 랜드마크 예측기는 인코더 특징으로부터 이 표현을 재현하도록 KL 발산 손실로 학습합니다(I-JEPA에서 착안). 얼굴 기하를 재구성하려면 인코더가 얼굴을 봐야 하고, 오디오 인코더는 소리로부터 입 모양을 추론해야 합니다. 이를 랜드마크 기반 증류(LBD)라고 부릅니다.</p>
</div>

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/detail/bm/face_landmarks.jpg" alt="A cropped face and the same face with MediaPipe landmarks drawn as green points" loading="lazy">
<figcaption><span class="lang-en">A cropped face (left) and its MediaPipe facial landmarks (right), which serve as the distillation target.</span><span class="lang-ko" lang="ko">잘라낸 얼굴(왼쪽)과 증류 목표로 쓰이는 MediaPipe 얼굴 랜드마크(오른쪽).</span></figcaption>
</figure>

<div class="lang-en">
<p><strong>2. Audio-visual temporal alignment.</strong> Video and audio features pass through transformer encoders with positional encoding. A cross-attention block (video as query, audio as key and value) feeds the real/fake classifier, and a contrastive loss pulls matching audio-video pairs together and pushes mismatched ones apart. We call this Multimodal Temporal Information Alignment (MTIA).</p>
<p><strong>3. A real-world test set.</strong> We collected over 500 viral audio-visual deepfakes from major social media platforms, chosen for video quality and audio realism and including high-profile figures such as presidents and military personnel, and paired them with 500 real VoxCeleb videos.</p>
</div>
<div class="lang-ko" lang="ko">
<p><strong>2. 오디오-비주얼 시간 정렬.</strong> 비디오와 오디오 특징은 위치 인코딩을 더한 트랜스포머 인코더를 거칩니다. 비디오를 쿼리, 오디오를 키·값으로 쓰는 교차 어텐션이 진짜/가짜 분류기로 이어지고, 대조 손실은 짝이 맞는 오디오-비디오 쌍은 가깝게, 맞지 않는 쌍은 멀게 만듭니다. 이를 멀티모달 시간 정보 정렬(MTIA)이라고 부릅니다.</p>
<p><strong>3. 실제 환경 평가셋.</strong> 주요 소셜 미디어에서 화제가 된 오디오-비주얼 딥페이크 500여 개를 영상 품질과 음성의 사실감을 기준으로 수집했고(대통령, 군 관계자 등 주요 인물 포함), 실제 영상으로는 VoxCeleb 영상 500개를 짝지었습니다.</p>
</div>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/bm/fig2_framework.png" alt="Framework: 3D CNN video encoder, 1D CNN audio encoder, landmark projector and predictors (LBD), transformer encoders and MTIA, cross-attention classifier" loading="lazy">
<figcaption><span class="lang-en"><strong>Figure 2.</strong> The landmark-guided framework: video and audio landmark predictors (P<sup>v</sup><sub>&phi;</sub>, P<sup>a</sup><sub>&phi;</sub>) and the landmark projector P<sub>&theta;</sub> implement LBD; transformer encoders, contrastive A-V alignment (MTIA) and cross-attention feed the classifier.</span><span class="lang-ko" lang="ko"><strong>그림 2.</strong> 랜드마크 유도 프레임워크: 비디오·오디오 랜드마크 예측기(P<sup>v</sup><sub>&phi;</sub>, P<sup>a</sup><sub>&phi;</sub>)와 랜드마크 프로젝터 P<sub>&theta;</sub>가 LBD를 구성하고, 트랜스포머 인코더, 대조 기반 오디오-비디오 정렬(MTIA), 교차 어텐션이 분류기로 이어집니다.</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">R</span>Result</h2>
<ul>
<li><strong>Generalization:</strong> trained on FakeAVCeleb and KoDF, the model reaches <strong>78.12% ACC / 78.82 AUC on unseen DFDC</strong> and <strong>75.12% ACC / 75.65 AUC on the real-world web set</strong>, the highest among all methods compared in Table 2 (next best: 74.65 / 75.18 on DFDC, 68.15 / 67.87 on the web set).</li>
<li><strong>Intra-dataset:</strong> 92.38% ACC / 93.25 AUC on FakeAVCeleb, where both modalities can be manipulated, vs. 83.42% ACC for the next-best method; best ACC on both DF-TIMIT splits.</li>
<li><strong>Efficiency:</strong> 32.4 M parameters, fewer than every multimodal baseline with a reported size (42.7&ndash;47.4 M).</li>
<li><strong>Every component helps:</strong> on FakeAVCeleb, accuracy rises from 72.12% (no landmarks, no MTIA) to 92.38% with the full model (Table 3), and Grad-CAM shows attention moving from the background to the eyes and mouth.</li>
<li>Published as a CIKM 2025 short paper (first author), with code released.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">R</span>결과 (Result)</h2>
<ul>
<li><strong>일반화:</strong> FakeAVCeleb과 KoDF로 학습했을 때 <strong>처음 보는 DFDC에서 정확도 78.12% / AUC 78.82</strong>, <strong>실제 웹 평가셋에서 75.12% / 75.65</strong>로, 표 2의 비교 방법 중 가장 높았습니다(차순위: DFDC 74.65 / 75.18, 웹 평가셋 68.15 / 67.87).</li>
<li><strong>데이터셋 내부 평가:</strong> 두 모달리티 모두 조작될 수 있는 FakeAVCeleb에서 정확도 92.38% / AUC 93.25로, 차순위 방법(정확도 83.42%)을 크게 앞섰고, DF-TIMIT 두 분할 모두에서 정확도 1위였습니다.</li>
<li><strong>효율성:</strong> 파라미터 3,240만 개로, 크기가 보고된 모든 멀티모달 기준 모델(4,270만~4,740만 개)보다 적습니다.</li>
<li><strong>모든 구성 요소가 기여:</strong> FakeAVCeleb에서 정확도가 72.12%(랜드마크·MTIA 없음)에서 전체 모델 92.38%까지 올랐고(표 3), Grad-CAM에서도 주의가 배경에서 눈과 입으로 옮겨 갔습니다.</li>
<li>CIKM 2025 숏페이퍼로 제1저자 게재, 코드 공개.</li>
</ul>
</div>

<p class="table-caption"><span class="lang-en">Table 2. Cross-dataset evaluation (trained on FakeAVCeleb + KoDF), ACC / AUC (%)</span><span class="lang-ko" lang="ko">표 2. 교차 데이터셋 평가 (FakeAVCeleb + KoDF로 학습), 정확도 / AUC (%)</span></p>
<div class="table-wrap">
<table>
<thead>
<tr><th rowspan="2">Methods</th><th rowspan="2">Params</th><th colspan="2">DFDC</th><th colspan="2">RW (web)</th></tr>
<tr><th>ACC</th><th>AUC</th><th>ACC</th><th>AUC</th></tr>
</thead>
<tbody>
<tr class="group"><td colspan="6">Visual unimodal</td></tr>
<tr><td>SPSL (Xception)</td><td>45.7 M</td><td>72.62</td><td>73.58</td><td>61.28</td><td>61.93</td></tr>
<tr><td>PRRNet</td><td>62.1 M</td><td><u>74.65</u></td><td><u>75.18</u></td><td>65.85</td><td>65.43</td></tr>
<tr><td>LipForensics</td><td>36.0 M</td><td>71.26</td><td>74.30</td><td>63.37</td><td>63.82</td></tr>
<tr><td>STIL</td><td>32.5 M</td><td>70.29</td><td>71.34</td><td>65.48</td><td>65.18</td></tr>
<tr><td>FTCN</td><td><strong>26.6 M</strong></td><td>73.18</td><td>74.03</td><td>64.19</td><td>64.80</td></tr>
<tr class="group"><td colspan="6">Multimodal</td></tr>
<tr><td>Emotions</td><td>42.7 M</td><td>69.14</td><td>71.44</td><td>67.28</td><td>67.72</td></tr>
<tr><td>MDS</td><td>47.4 M</td><td>70.29</td><td>70.30</td><td>66.84</td><td>67.32</td></tr>
<tr><td>Joint Audio-Visual</td><td>46.1 M</td><td>71.22</td><td>72.69</td><td><u>68.15</u></td><td><u>67.87</u></td></tr>
<tr class="ours"><td>Ours</td><td><u>32.4 M</u></td><td><strong>78.12</strong></td><td><strong>78.82</strong></td><td><strong>75.12</strong></td><td><strong>75.65</strong></td></tr>
</tbody>
</table>
</div>

<p class="table-caption"><span class="lang-en">Table 1. Intra-dataset evaluation (80% / 20% split per benchmark), ACC / AUC (%)</span><span class="lang-ko" lang="ko">표 1. 데이터셋 내부 평가 (벤치마크별 80% / 20% 분할), 정확도 / AUC (%)</span></p>
<div class="table-wrap">
<table>
<thead>
<tr><th rowspan="2">Methods</th><th rowspan="2">Params</th><th colspan="2">DF-TIMIT (LQ)</th><th colspan="2">DF-TIMIT (HQ)</th><th colspan="2">DFDC</th><th colspan="2">FakeAVCeleb</th><th colspan="2">KoDF</th></tr>
<tr><th>ACC</th><th>AUC</th><th>ACC</th><th>AUC</th><th>ACC</th><th>AUC</th><th>ACC</th><th>AUC</th><th>ACC</th><th>AUC</th></tr>
</thead>
<tbody>
<tr class="group"><td colspan="12">Visual unimodal</td></tr>
<tr><td>SPSL (Xception)</td><td>45.7 M</td><td>91.17</td><td>90.03</td><td>92.29</td><td>91.78</td><td>92.04</td><td>94.74</td><td>77.94</td><td>79.83</td><td>92.36</td><td>93.19</td></tr>
<tr><td>PRRNet</td><td>62.1 M</td><td>93.86</td><td><u>95.30</u></td><td><u>98.72</u></td><td><u>98.97</u></td><td><strong>95.24</strong></td><td><u>96.37</u></td><td>78.17</td><td>79.54</td><td><strong>94.52</strong></td><td><strong>94.83</strong></td></tr>
<tr><td>LipForensics</td><td>36.0 M</td><td>92.77</td><td>93.06</td><td>97.74</td><td>96.51</td><td>82.47</td><td>65.06</td><td>76.22</td><td>78.43</td><td>93.16</td><td>93.72</td></tr>
<tr><td>STIL</td><td>32.5 M</td><td><u>95.76</u></td><td>94.18</td><td>98.24</td><td><strong>99.03</strong></td><td>92.17</td><td>95.07</td><td>79.80</td><td>78.29</td><td><u>94.14</u></td><td>93.92</td></tr>
<tr><td>FTCN</td><td><strong>26.6 M</strong></td><td>93.64</td><td>94.41</td><td>96.62</td><td>95.39</td><td><u>94.92</u></td><td><strong>97.88</strong></td><td>78.98</td><td>79.21</td><td>93.22</td><td>92.96</td></tr>
<tr class="group"><td colspan="12">Multimodal</td></tr>
<tr><td>DST-Net</td><td>&ndash;</td><td>&ndash;</td><td>&ndash;</td><td>&ndash;</td><td>&ndash;</td><td>82.80</td><td>86.20</td><td>78.40</td><td>80.40</td><td>&ndash;</td><td>&ndash;</td></tr>
<tr><td>VFD</td><td>&ndash;</td><td>&ndash;</td><td>&ndash;</td><td>&ndash;</td><td>&ndash;</td><td>80.90</td><td>85.10</td><td>81.50</td><td><u>86.10</u></td><td>&ndash;</td><td>&ndash;</td></tr>
<tr><td>Emotions</td><td>42.7 M</td><td>92.76</td><td>92.95</td><td>94.69</td><td>95.42</td><td>82.36</td><td>85.00</td><td><u>83.42</u></td><td>84.81</td><td>93.26</td><td>94.41</td></tr>
<tr><td>MDS</td><td>47.4 M</td><td>93.14</td><td>93.14</td><td>94.77</td><td>95.02</td><td>82.36</td><td>84.47</td><td>81.53</td><td>83.24</td><td>93.26</td><td>94.41</td></tr>
<tr><td>Joint Audio-Visual</td><td>46.1 M</td><td>75.22</td><td>75.69</td><td>81.65</td><td>83.47</td><td>90.44</td><td>89.94</td><td>81.20</td><td>82.45</td><td>92.96</td><td>93.59</td></tr>
<tr class="ours"><td>Ours</td><td><u>32.4 M</u></td><td><strong>96.82</strong></td><td><strong>97.35</strong></td><td><strong>98.98</strong></td><td>97.72</td><td>89.75</td><td>90.07</td><td><strong>92.38</strong></td><td><strong>93.25</strong></td><td>93.48</td><td><u>94.75</u></td></tr>
</tbody>
</table>
</div>
<p class="table-note"><span class="lang-en">Values as reported in the paper (Tables 1&ndash;2). <strong>Bold</strong> = best, <u>underline</u> = second best (fewest parameters counts as best). For DF-TIMIT (HQ) AUC the second-best value is PRRNet's 98.97.</span><span class="lang-ko" lang="ko">값은 논문(표 1&ndash;2)에 보고된 그대로입니다. <strong>굵게</strong> = 1위, <u>밑줄</u> = 2위(파라미터는 적을수록 1위). DF-TIMIT (HQ) AUC의 2위는 PRRNet의 98.97입니다.</span></p>

<p class="table-caption"><span class="lang-en">Table 3. Ablation of LBD components and MTIA (FakeAVCeleb, %)</span><span class="lang-ko" lang="ko">표 3. LBD 구성 요소와 MTIA 제거 실험 (FakeAVCeleb, %)</span></p>
<div class="table-wrap">
<table>
<thead>
<tr><th>P<sup>v</sup><sub>&phi;</sub></th><th>P<sup>a</sup><sub>&phi;</sub></th><th>P<sub>&theta;</sub></th><th>MTIA</th><th>ACC</th><th>AUC</th></tr>
</thead>
<tbody>
<tr><td>&#10007;</td><td>&#10007;</td><td>&#10007;</td><td>&#10007;</td><td>72.12</td><td>71.81</td></tr>
<tr><td>&#10003;</td><td>&#10007;</td><td>&#10003;</td><td>&#10007;</td><td>82.08</td><td>81.57</td></tr>
<tr><td>&#10007;</td><td>&#10003;</td><td>&#10003;</td><td>&#10007;</td><td>75.89</td><td>75.14</td></tr>
<tr><td>&#10003;</td><td>&#10003;</td><td>&#10007;</td><td>&#10007;</td><td>84.25</td><td>85.60</td></tr>
<tr><td>&#10003;</td><td>&#10003;</td><td>&#10003;</td><td>&#10007;</td><td>89.43</td><td>90.15</td></tr>
<tr class="ours"><td>&#10003;</td><td>&#10003;</td><td>&#10003;</td><td>&#10003;</td><td><strong>92.38</strong></td><td><strong>93.25</strong></td></tr>
</tbody>
</table>
</div>
<p class="table-note"><span class="lang-en">Row 1 encodes each modality separately and concatenates the features. The visual landmark predictor helps more than the audio one (rows 2&ndash;3); combining both, adding the projector, and then MTIA each add accuracy.</span><span class="lang-ko" lang="ko">1행은 각 모달리티를 따로 인코딩해 특징을 이어 붙인 기준 모델입니다. 비디오 랜드마크 예측기가 오디오 쪽보다 효과가 크고(2&ndash;3행), 두 예측기 결합, 프로젝터 추가, MTIA 추가가 각각 정확도를 높입니다.</span></p>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/bm/fig3_gradcam.jpg" alt="Grad-CAM maps of models trained with and without landmarks on real and fake faces" loading="lazy">
<figcaption><span class="lang-en"><strong>Figure 3.</strong> Grad-CAM of models trained with and without landmarks (green frame: real, red frame: fake). With landmark-based distillation, attention concentrates on the eyes and mouth instead of the whole frame.</span><span class="lang-ko" lang="ko"><strong>그림 3.</strong> 랜드마크 사용 여부에 따른 Grad-CAM 비교(초록 테두리: 진짜, 빨간 테두리: 가짜). 랜드마크 기반 증류를 쓰면 주의가 화면 전체가 아니라 눈과 입에 집중됩니다.</span></figcaption>
</figure>

<h2 class="sec-h">BibTeX</h2>
{% raw %}<pre class="bibtex">@inproceedings{park2025beyondmasking,
  title     = {Beyond Masking: Landmark-based Representation Learning and
               Knowledge-Distillation for Audio-Visual Deepfake Detection},
  author    = {Park, Chan and Muneer, Muhammad Shahid and Woo, Simon S.},
  booktitle = {Proceedings of the 34th ACM International Conference on
               Information and Knowledge Management (CIKM '25)},
  year      = {2025},
  publisher = {Association for Computing Machinery},
  address   = {New York, NY, USA},
  location  = {Seoul, Republic of Korea},
  doi       = {10.1145/3746252.3760853}
}</pre>{% endraw %}

</div>
