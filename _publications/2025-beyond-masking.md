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
teaser: cikm_teaser.jpg
teaser_hover: cikm_hover.png
paper_url: https://doi.org/10.1145/3746252.3760853
code_url: https://github.com/Ckck12/Beyond-Masking
project_url: https://ckck12.github.io/Beyond-Masking/
slides_url:
tldr: "Facial-landmark distillation plus audio-visual temporal alignment; highest cross-dataset accuracy among compared methods on DFDC and 500+ web deepfakes, with 32.4 M parameters."
tldr_list_ko: "얼굴 랜드마크 증류 + 오디오-비주얼 시간 정렬. DFDC와 웹 딥페이크 500여 개 교차 데이터셋 평가에서 비교 방법 중 최고 정확도, 파라미터 32.4 M."
tldr_list_zh: "人脸关键点蒸馏 + 音视频时序对齐；在 DFDC 和 500 多个网络深度伪造视频的跨数据集测试中，准确率在对比方法中最高，参数量 32.4 M。"
tldr_en:
  - "Audio-visual deepfake detectors often watch the background, not the face, and fail on real social-media deepfakes."
  - "Beyond Masking distills facial-landmark geometry into both video and audio encoders, and aligns the two over time."
  - "Cross-dataset: 78.12% on DFDC and 75.12% on 500+ web deepfakes, highest among compared methods, with 32.4 M parameters."
tldr_ko:
  - "오디오-비주얼 딥페이크 탐지기는 얼굴보다 배경을 보는 경우가 많아, 소셜 미디어의 실제 딥페이크에서 실패합니다."
  - "Beyond Masking은 얼굴 랜드마크 기하 정보를 비디오·오디오 인코더 모두에 증류하고, 두 모달리티를 시간 축에서 정렬합니다."
  - "교차 데이터셋: DFDC 78.12%, 웹 딥페이크 500여 개 75.12%로 비교 방법 중 최고이며, 파라미터는 32.4 M입니다."
tldr_zh:
  - "音视频深度伪造检测器常常关注背景而非人脸，面对社交媒体上的真实深度伪造时失效。"
  - "Beyond Masking 将人脸关键点几何信息蒸馏到视频和音频编码器中，并在时间上对齐两种模态。"
  - "跨数据集：DFDC 78.12%，500 多个网络深度伪造视频 75.12%，在对比方法中最高；参数量 32.4 M。"
excerpt: "Landmark-based distillation and audio-visual temporal alignment for deepfake detection that generalizes to real-world web deepfakes (CIKM 2025 short paper, first author)."
meta_in_body: true
featured: true
collection: publications
---

{% include tldr.html %}

{% include pub-meta.html %}

<div class="proj-body">

<div class="kpis">
<div class="kpi"><span class="kpi__value">78.12%</span><span class="kpi__label lang-en">cross-dataset accuracy on unseen DFDC (AUC 78.82)</span><span class="kpi__label lang-ko" lang="ko">처음 보는 DFDC 교차 데이터셋 정확도 (AUC 78.82)</span><span class="kpi__label lang-zh" lang="zh-Hans">未见过的 DFDC 上的跨数据集准确率（AUC 78.82）</span></div>
<div class="kpi"><span class="kpi__value">75.12%</span><span class="kpi__label lang-en">accuracy on 500+ real-world web deepfakes (AUC 75.65)</span><span class="kpi__label lang-ko" lang="ko">실제 웹 딥페이크 500여 개 정확도 (AUC 75.65)</span><span class="kpi__label lang-zh" lang="zh-Hans">500 多个真实网络深度伪造视频上的准确率（AUC 75.65）</span></div>
<div class="kpi"><span class="kpi__value">92.38%</span><span class="kpi__label lang-en">intra-dataset accuracy on FakeAVCeleb (AUC 93.25)</span><span class="kpi__label lang-ko" lang="ko">FakeAVCeleb 데이터셋 내부 정확도 (AUC 93.25)</span><span class="kpi__label lang-zh" lang="zh-Hans">FakeAVCeleb 数据集内准确率（AUC 93.25）</span></div>
<div class="kpi"><span class="kpi__value">32.4 M</span><span class="kpi__label lang-en">parameters, fewer than every multimodal baseline with a reported size</span><span class="kpi__label lang-ko" lang="ko">파라미터 수 (크기가 보고된 모든 멀티모달 기준 모델보다 적음)</span><span class="kpi__label lang-zh" lang="zh-Hans">参数量（少于所有报告了规模的多模态基线）</span></div>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">S</span>Situation</h2>
<ul>
<li>Audio-visual deepfakes fake the face, the voice, or both. They spread on social media, including political propaganda.</li>
<li>Detectors trained on academic datasets (DFDC, FakeAVCeleb, KoDF) score well. On real-world deepfakes they degrade sharply, with many false alarms.</li>
<li>One reason is <em>where</em> models look. Grad-CAM shows a standard backbone attending to background, hair and neck.</li>
<li>These are spurious, dataset-specific cues. Manipulation actually happens on the face and mouth.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">S</span>상황</h2>
<ul>
<li>오디오-비주얼 딥페이크는 얼굴, 목소리, 또는 둘 다를 조작합니다. 정치 선전을 포함해 소셜 미디어에 퍼지고 있습니다.</li>
<li>학술 데이터셋(DFDC, FakeAVCeleb, KoDF)으로 학습한 탐지기는 수치가 높습니다. 그러나 실제 딥페이크에서는 성능이 크게 떨어지고 오탐이 많습니다.</li>
<li>원인 중 하나는 모델이 <em>어디를</em> 보느냐입니다. Grad-CAM으로 보면 일반 백본은 배경·머리카락·목에 주의를 둡니다.</li>
<li>이는 특정 데이터셋에 묶인 부수적 단서입니다. 실제 조작은 얼굴과 입에서 일어납니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">S</span>情境</h2>
<ul>
<li>音视频深度伪造会伪造人脸、声音或两者，正在社交媒体上传播，包括政治宣传。</li>
<li>在学术数据集（DFDC、FakeAVCeleb、KoDF）上训练的检测器分数很高，但面对真实深度伪造时性能骤降，误报很多。</li>
<li>原因之一是模型<em>看哪里</em>。Grad-CAM 显示，常规骨干网络把注意力放在背景、头发和脖子上。</li>
<li>这些是与特定数据集绑定的虚假线索，而篡改实际发生在人脸和嘴部。</li>
</ul>
</div>

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/detail/bm/fig1_realworld_samples.jpg" alt="Grid of eight audio-visual deepfake samples collected from social media" loading="lazy">
<figcaption><span class="lang-en"><strong>Figure 1.</strong> Audio-visual deepfakes collected from social media (Meta, YouTube, X, Reddit). They form the Real-World (RW) test set.</span><span class="lang-ko" lang="ko"><strong>그림 1.</strong> Meta, YouTube, X, Reddit 등 소셜 미디어에서 수집한 오디오-비주얼 딥페이크. 실제 환경(RW) 평가셋을 구성합니다.</span><span class="lang-zh" lang="zh-Hans"><strong>图 1.</strong> 从社交媒体（Meta、YouTube、X、Reddit）收集的音视频深度伪造样本，构成真实场景（RW）测试集。</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">T</span>Task</h2>
<ul>
<li>Make both video and audio encoders attend to <strong>facial geometry</strong> (eyes, lips, face shape), not the background.</li>
<li>Check that mouth motion and speech stay <strong>synchronized over time</strong>.</li>
<li>Generalize to <strong>unseen datasets and real web deepfakes</strong> with a small model.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">T</span>과제</h2>
<ul>
<li>비디오·오디오 인코더가 배경 대신 <strong>얼굴 기하 구조</strong>(눈, 입술, 얼굴 형태)에 집중하게 한다.</li>
<li>입 모양과 음성이 <strong>시간적으로 맞물리는지</strong> 확인한다.</li>
<li>작은 모델로 <strong>처음 보는 데이터셋과 실제 웹 딥페이크</strong>에 일반화한다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">T</span>任务</h2>
<ul>
<li>让视频和音频编码器关注<strong>人脸几何结构</strong>（眼睛、嘴唇、脸型），而不是背景。</li>
<li>检查嘴部动作与语音是否<strong>在时间上同步</strong>。</li>
<li>用小模型泛化到<strong>未见过的数据集和真实网络深度伪造</strong>。</li>
</ul>
</div>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">A</span>Action</h2>
<p><strong>1. Landmark-based Distillation (LBD): landmarks as a teacher, not a mask.</strong></p>
<ul>
<li>Instead of masking the input, we extract 478 facial landmarks per frame with MediaPipe.</li>
<li>A small landmark projector turns them into a target representation.</li>
<li>Video and audio landmark predictors learn to reproduce it from encoder features, with a KL-divergence loss (motivated by I-JEPA).</li>
<li>To reconstruct facial geometry, the encoders must look at the face. The audio encoder must infer mouth shape from sound.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">A</span>행동</h2>
<p><strong>1. Landmark-based Distillation (LBD): 마스크가 아니라 교사로서의 랜드마크.</strong></p>
<ul>
<li>입력을 가리는 대신, MediaPipe로 프레임마다 얼굴 랜드마크 478개를 추출합니다.</li>
<li>작은 랜드마크 프로젝터가 이를 목표 표현으로 바꿉니다.</li>
<li>비디오·오디오 랜드마크 예측기는 인코더 특징으로 이 표현을 재현하도록 KL 발산 손실로 학습합니다(I-JEPA에서 착안).</li>
<li>얼굴 기하를 재구성하려면 인코더가 얼굴을 봐야 합니다. 오디오 인코더는 소리로 입 모양을 추론해야 합니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">A</span>行动</h2>
<p><strong>1. Landmark-based Distillation（LBD）：把关键点当作教师，而不是掩码。</strong></p>
<ul>
<li>不遮挡输入，而是用 MediaPipe 每帧提取 478 个人脸关键点。</li>
<li>一个小型关键点投影器将其转换为目标表示。</li>
<li>视频和音频关键点预测器以 KL 散度损失，从编码器特征中重建该表示（受 I-JEPA 启发）。</li>
<li>要重建人脸几何，编码器必须看人脸；音频编码器则必须从声音推断嘴型。</li>
</ul>
</div>

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/detail/bm/face_landmarks.jpg" alt="A cropped face and the same face with MediaPipe landmarks drawn as green points" loading="lazy">
<figcaption><span class="lang-en">A cropped face (left) and its MediaPipe facial landmarks (right), used as the distillation target.</span><span class="lang-ko" lang="ko">잘라낸 얼굴(왼쪽)과 증류 목표로 쓰는 MediaPipe 얼굴 랜드마크(오른쪽).</span><span class="lang-zh" lang="zh-Hans">裁剪后的人脸（左）及其 MediaPipe 人脸关键点（右），用作蒸馏目标。</span></figcaption>
</figure>

<div class="lang-en">
<p><strong>2. Multimodal Temporal Information Alignment (MTIA).</strong></p>
<ul>
<li>Video and audio features pass through transformer encoders with positional encoding.</li>
<li>Cross-attention (video = query, audio = key and value) feeds the real/fake classifier.</li>
<li>A contrastive loss pulls matching audio-video pairs together and pushes mismatched pairs apart.</li>
</ul>
<p><strong>3. A real-world test set.</strong></p>
<ul>
<li>Over 500 viral audio-visual deepfakes from major social media, chosen for video quality and audio realism.</li>
<li>Includes high-profile figures such as presidents and military personnel. Paired with 500 real VoxCeleb videos.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<p><strong>2. Multimodal Temporal Information Alignment (MTIA).</strong></p>
<ul>
<li>비디오·오디오 특징은 위치 인코딩을 더한 트랜스포머 인코더를 거칩니다.</li>
<li>교차 어텐션(비디오 = 쿼리, 오디오 = 키·값)이 진짜/가짜 분류기로 이어집니다.</li>
<li>대조 손실은 짝이 맞는 오디오-비디오 쌍을 가깝게, 맞지 않는 쌍을 멀게 만듭니다.</li>
</ul>
<p><strong>3. 실제 환경 평가셋.</strong></p>
<ul>
<li>주요 소셜 미디어에서 화제가 된 오디오-비주얼 딥페이크 500여 개를 영상 품질과 음성 사실감 기준으로 수집했습니다.</li>
<li>대통령, 군 관계자 등 주요 인물이 포함됩니다. 실제 영상으로 VoxCeleb 500개를 짝지었습니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<p><strong>2. Multimodal Temporal Information Alignment（MTIA）。</strong></p>
<ul>
<li>视频与音频特征经过带位置编码的 Transformer 编码器。</li>
<li>交叉注意力（视频为 query，音频为 key 和 value）连接真伪分类器。</li>
<li>对比损失拉近匹配的音视频对，推远不匹配的对。</li>
</ul>
<p><strong>3. 真实场景测试集。</strong></p>
<ul>
<li>从主流社交媒体收集 500 多个热门音视频深度伪造，按视频质量和音频真实感筛选。</li>
<li>包含总统、军方人员等知名人物，并配对 500 个真实 VoxCeleb 视频。</li>
</ul>
</div>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/bm/fig2_framework.png" alt="Framework: 3D CNN video encoder, 1D CNN audio encoder, landmark projector and predictors (LBD), transformer encoders and MTIA, cross-attention classifier" loading="lazy">
<figcaption><span class="lang-en"><strong>Figure 2.</strong> The framework. Landmark predictors (P<sup>v</sup><sub>&phi;</sub>, P<sup>a</sup><sub>&phi;</sub>) and the projector P<sub>&theta;</sub> implement LBD. Transformer encoders, contrastive A-V alignment (MTIA) and cross-attention feed the classifier.</span><span class="lang-ko" lang="ko"><strong>그림 2.</strong> 프레임워크. 랜드마크 예측기(P<sup>v</sup><sub>&phi;</sub>, P<sup>a</sup><sub>&phi;</sub>)와 프로젝터 P<sub>&theta;</sub>가 LBD를 구성합니다. 트랜스포머 인코더, 대조 기반 오디오-비디오 정렬(MTIA), 교차 어텐션이 분류기로 이어집니다.</span><span class="lang-zh" lang="zh-Hans"><strong>图 2.</strong> 整体框架。关键点预测器（P<sup>v</sup><sub>&phi;</sub>、P<sup>a</sup><sub>&phi;</sub>）与投影器 P<sub>&theta;</sub> 构成 LBD；Transformer 编码器、对比式音视频对齐（MTIA）和交叉注意力连接分类器。</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">R</span>Result</h2>
<ul>
<li><strong>Generalization:</strong> trained on FakeAVCeleb + KoDF, it reaches <strong>78.12% ACC / 78.82 AUC on unseen DFDC</strong> and <strong>75.12% / 75.65 on the real-world web set</strong>.</li>
<li>Both are the highest among methods compared in Table 2 (next best: 74.65 / 75.18 on DFDC, 68.15 / 67.87 on the web set).</li>
<li><strong>Intra-dataset:</strong> 92.38% ACC / 93.25 AUC on FakeAVCeleb, where both modalities can be faked; next best is 83.42% ACC. Best ACC on both DF-TIMIT splits.</li>
<li><strong>Efficiency:</strong> 32.4 M parameters, fewer than every multimodal baseline with a reported size (42.7&ndash;47.4 M).</li>
<li><strong>Every component helps:</strong> FakeAVCeleb accuracy rises from 72.12% (no landmarks, no MTIA) to 92.38% (Table 3). Grad-CAM attention moves from the background to the eyes and mouth.</li>
<li>CIKM 2025 short paper (first author), with code released.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">R</span>결과</h2>
<ul>
<li><strong>일반화:</strong> FakeAVCeleb + KoDF로 학습해 <strong>처음 보는 DFDC에서 ACC 78.12% / AUC 78.82</strong>, <strong>실제 웹 평가셋에서 75.12% / 75.65</strong>를 기록했습니다.</li>
<li>둘 다 표 2의 비교 방법 중 최고입니다(차순위: DFDC 74.65 / 75.18, 웹 평가셋 68.15 / 67.87).</li>
<li><strong>데이터셋 내부:</strong> 두 모달리티 모두 조작될 수 있는 FakeAVCeleb에서 ACC 92.38% / AUC 93.25이며, 차순위는 ACC 83.42%입니다. DF-TIMIT 두 분할 모두 ACC 1위입니다.</li>
<li><strong>효율성:</strong> 파라미터 32.4 M으로, 크기가 보고된 모든 멀티모달 기준 모델(42.7&ndash;47.4 M)보다 적습니다.</li>
<li><strong>모든 구성 요소가 기여:</strong> FakeAVCeleb 정확도가 72.12%(랜드마크·MTIA 없음)에서 92.38%로 올랐습니다(표 3). Grad-CAM 주의도 배경에서 눈과 입으로 옮겨 갔습니다.</li>
<li>CIKM 2025 숏페이퍼(제1저자), 코드 공개.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">R</span>结果</h2>
<ul>
<li><strong>泛化：</strong>在 FakeAVCeleb + KoDF 上训练，<strong>未见过的 DFDC 上 ACC 78.12% / AUC 78.82</strong>，<strong>真实网络测试集上 75.12% / 75.65</strong>。</li>
<li>两者均为表 2 对比方法中最高（次佳：DFDC 74.65 / 75.18，网络测试集 68.15 / 67.87）。</li>
<li><strong>数据集内：</strong>在两种模态都可能被篡改的 FakeAVCeleb 上 ACC 92.38% / AUC 93.25，次佳方法 ACC 为 83.42%；在 DF-TIMIT 两个划分上 ACC 均为第一。</li>
<li><strong>效率：</strong>参数量 32.4 M，少于所有报告了规模的多模态基线（42.7&ndash;47.4 M）。</li>
<li><strong>每个组件都有贡献：</strong>FakeAVCeleb 准确率从 72.12%（无关键点、无 MTIA）提升到 92.38%（表 3）；Grad-CAM 注意力从背景转向眼睛和嘴部。</li>
<li>CIKM 2025 短论文（第一作者），代码已开源。</li>
</ul>
</div>

<p class="table-caption"><span class="lang-en">Table 2. Cross-dataset evaluation (trained on FakeAVCeleb + KoDF), ACC / AUC (%)</span><span class="lang-ko" lang="ko">표 2. 교차 데이터셋 평가 (FakeAVCeleb + KoDF로 학습), ACC / AUC (%)</span><span class="lang-zh" lang="zh-Hans">表 2. 跨数据集评估（在 FakeAVCeleb + KoDF 上训练），ACC / AUC（%）</span></p>
<div class="table-wrap">
<table>
<thead>
<tr><th rowspan="2"><span class="lang-en">Methods</span><span class="lang-ko" lang="ko">방법</span><span class="lang-zh" lang="zh-Hans">方法</span></th><th rowspan="2"><span class="lang-en">Params</span><span class="lang-ko" lang="ko">파라미터</span><span class="lang-zh" lang="zh-Hans">参数量</span></th><th colspan="2">DFDC</th><th colspan="2">RW <span class="lang-en">(web)</span><span class="lang-ko" lang="ko">(웹)</span><span class="lang-zh" lang="zh-Hans">（网络）</span></th></tr>
<tr><th>ACC</th><th>AUC</th><th>ACC</th><th>AUC</th></tr>
</thead>
<tbody>
<tr class="group"><td colspan="6"><span class="lang-en">Visual unimodal</span><span class="lang-ko" lang="ko">시각 단일 모달</span><span class="lang-zh" lang="zh-Hans">视觉单模态</span></td></tr>
<tr><td>SPSL (Xception)</td><td>45.7 M</td><td>72.62</td><td>73.58</td><td>61.28</td><td>61.93</td></tr>
<tr><td>PRRNet</td><td>62.1 M</td><td><u>74.65</u></td><td><u>75.18</u></td><td>65.85</td><td>65.43</td></tr>
<tr><td>LipForensics</td><td>36.0 M</td><td>71.26</td><td>74.30</td><td>63.37</td><td>63.82</td></tr>
<tr><td>STIL</td><td>32.5 M</td><td>70.29</td><td>71.34</td><td>65.48</td><td>65.18</td></tr>
<tr><td>FTCN</td><td><strong>26.6 M</strong></td><td>73.18</td><td>74.03</td><td>64.19</td><td>64.80</td></tr>
<tr class="group"><td colspan="6"><span class="lang-en">Multimodal</span><span class="lang-ko" lang="ko">멀티모달</span><span class="lang-zh" lang="zh-Hans">多模态</span></td></tr>
<tr><td>Emotions</td><td>42.7 M</td><td>69.14</td><td>71.44</td><td>67.28</td><td>67.72</td></tr>
<tr><td>MDS</td><td>47.4 M</td><td>70.29</td><td>70.30</td><td>66.84</td><td>67.32</td></tr>
<tr><td>Joint Audio-Visual</td><td>46.1 M</td><td>71.22</td><td>72.69</td><td><u>68.15</u></td><td><u>67.87</u></td></tr>
<tr class="ours"><td><span class="lang-en">Ours</span><span class="lang-ko" lang="ko">제안 방법</span><span class="lang-zh" lang="zh-Hans">本文方法</span></td><td><u>32.4 M</u></td><td><strong>78.12</strong></td><td><strong>78.82</strong></td><td><strong>75.12</strong></td><td><strong>75.65</strong></td></tr>
</tbody>
</table>
</div>

<p class="table-caption"><span class="lang-en">Table 1. Intra-dataset evaluation (80% / 20% split per benchmark), ACC / AUC (%)</span><span class="lang-ko" lang="ko">표 1. 데이터셋 내부 평가 (벤치마크별 80% / 20% 분할), ACC / AUC (%)</span><span class="lang-zh" lang="zh-Hans">表 1. 数据集内评估（每个基准按 80% / 20% 划分），ACC / AUC（%）</span></p>
<div class="table-wrap">
<table>
<thead>
<tr><th rowspan="2"><span class="lang-en">Methods</span><span class="lang-ko" lang="ko">방법</span><span class="lang-zh" lang="zh-Hans">方法</span></th><th rowspan="2"><span class="lang-en">Params</span><span class="lang-ko" lang="ko">파라미터</span><span class="lang-zh" lang="zh-Hans">参数量</span></th><th colspan="2">DF-TIMIT (LQ)</th><th colspan="2">DF-TIMIT (HQ)</th><th colspan="2">DFDC</th><th colspan="2">FakeAVCeleb</th><th colspan="2">KoDF</th></tr>
<tr><th>ACC</th><th>AUC</th><th>ACC</th><th>AUC</th><th>ACC</th><th>AUC</th><th>ACC</th><th>AUC</th><th>ACC</th><th>AUC</th></tr>
</thead>
<tbody>
<tr class="group"><td colspan="12"><span class="lang-en">Visual unimodal</span><span class="lang-ko" lang="ko">시각 단일 모달</span><span class="lang-zh" lang="zh-Hans">视觉单模态</span></td></tr>
<tr><td>SPSL (Xception)</td><td>45.7 M</td><td>91.17</td><td>90.03</td><td>92.29</td><td>91.78</td><td>92.04</td><td>94.74</td><td>77.94</td><td>79.83</td><td>92.36</td><td>93.19</td></tr>
<tr><td>PRRNet</td><td>62.1 M</td><td>93.86</td><td><u>95.30</u></td><td><u>98.72</u></td><td><u>98.97</u></td><td><strong>95.24</strong></td><td><u>96.37</u></td><td>78.17</td><td>79.54</td><td><strong>94.52</strong></td><td><strong>94.83</strong></td></tr>
<tr><td>LipForensics</td><td>36.0 M</td><td>92.77</td><td>93.06</td><td>97.74</td><td>96.51</td><td>82.47</td><td>65.06</td><td>76.22</td><td>78.43</td><td>93.16</td><td>93.72</td></tr>
<tr><td>STIL</td><td>32.5 M</td><td><u>95.76</u></td><td>94.18</td><td>98.24</td><td><strong>99.03</strong></td><td>92.17</td><td>95.07</td><td>79.80</td><td>78.29</td><td><u>94.14</u></td><td>93.92</td></tr>
<tr><td>FTCN</td><td><strong>26.6 M</strong></td><td>93.64</td><td>94.41</td><td>96.62</td><td>95.39</td><td><u>94.92</u></td><td><strong>97.88</strong></td><td>78.98</td><td>79.21</td><td>93.22</td><td>92.96</td></tr>
<tr class="group"><td colspan="12"><span class="lang-en">Multimodal</span><span class="lang-ko" lang="ko">멀티모달</span><span class="lang-zh" lang="zh-Hans">多模态</span></td></tr>
<tr><td>DST-Net</td><td>&ndash;</td><td>&ndash;</td><td>&ndash;</td><td>&ndash;</td><td>&ndash;</td><td>82.80</td><td>86.20</td><td>78.40</td><td>80.40</td><td>&ndash;</td><td>&ndash;</td></tr>
<tr><td>VFD</td><td>&ndash;</td><td>&ndash;</td><td>&ndash;</td><td>&ndash;</td><td>&ndash;</td><td>80.90</td><td>85.10</td><td>81.50</td><td><u>86.10</u></td><td>&ndash;</td><td>&ndash;</td></tr>
<tr><td>Emotions</td><td>42.7 M</td><td>92.76</td><td>92.95</td><td>94.69</td><td>95.42</td><td>82.36</td><td>85.00</td><td><u>83.42</u></td><td>84.81</td><td>93.26</td><td>94.41</td></tr>
<tr><td>MDS</td><td>47.4 M</td><td>93.14</td><td>93.14</td><td>94.77</td><td>95.02</td><td>82.36</td><td>84.47</td><td>81.53</td><td>83.24</td><td>93.26</td><td>94.41</td></tr>
<tr><td>Joint Audio-Visual</td><td>46.1 M</td><td>75.22</td><td>75.69</td><td>81.65</td><td>83.47</td><td>90.44</td><td>89.94</td><td>81.20</td><td>82.45</td><td>92.96</td><td>93.59</td></tr>
<tr class="ours"><td><span class="lang-en">Ours</span><span class="lang-ko" lang="ko">제안 방법</span><span class="lang-zh" lang="zh-Hans">本文方法</span></td><td><u>32.4 M</u></td><td><strong>96.82</strong></td><td><strong>97.35</strong></td><td><strong>98.98</strong></td><td>97.72</td><td>89.75</td><td>90.07</td><td><strong>92.38</strong></td><td><strong>93.25</strong></td><td>93.48</td><td><u>94.75</u></td></tr>
</tbody>
</table>
</div>
<p class="table-note"><span class="lang-en">Values as reported in the paper (Tables 1&ndash;2). <strong>Bold</strong> = best, <u>underline</u> = second best; fewest parameters counts as best. For DF-TIMIT (HQ) AUC, the second-best value is PRRNet's 98.97.</span><span class="lang-ko" lang="ko">값은 논문 표 1&ndash;2 그대로입니다. <strong>굵게</strong> = 1위, <u>밑줄</u> = 2위(파라미터는 적을수록 1위). DF-TIMIT (HQ) AUC의 2위는 PRRNet의 98.97입니다.</span><span class="lang-zh" lang="zh-Hans">数值取自论文表 1&ndash;2。<strong>粗体</strong> = 最佳，<u>下划线</u> = 次佳（参数量越少越好）。DF-TIMIT (HQ) AUC 的次佳为 PRRNet 的 98.97。</span></p>

<p class="table-caption"><span class="lang-en">Table 3. Ablation of LBD components and MTIA (FakeAVCeleb, %)</span><span class="lang-ko" lang="ko">표 3. LBD 구성 요소와 MTIA 제거 실험 (FakeAVCeleb, %)</span><span class="lang-zh" lang="zh-Hans">表 3. LBD 组件与 MTIA 消融实验（FakeAVCeleb，%）</span></p>
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
<p class="table-note"><span class="lang-en">Row 1 encodes each modality separately and concatenates the features. The visual landmark predictor helps more than the audio one (rows 2&ndash;3). Combining both, adding the projector, then MTIA each add accuracy.</span><span class="lang-ko" lang="ko">1행은 모달리티별로 따로 인코딩해 특징을 이어 붙인 기준 모델입니다. 비디오 랜드마크 예측기가 오디오 쪽보다 효과가 큽니다(2&ndash;3행). 두 예측기 결합, 프로젝터 추가, MTIA 추가가 각각 정확도를 높입니다.</span><span class="lang-zh" lang="zh-Hans">第 1 行分别编码各模态并拼接特征。视频关键点预测器的作用大于音频预测器（第 2&ndash;3 行）；结合两者、加入投影器、再加入 MTIA，每一步都提升准确率。</span></p>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/bm/fig3_gradcam.jpg" alt="Grad-CAM maps of models trained with and without landmarks on real and fake faces" loading="lazy">
<figcaption><span class="lang-en"><strong>Figure 3.</strong> Grad-CAM with and without landmarks (green frame: real, red frame: fake). With LBD, attention concentrates on the eyes and mouth, not the whole frame.</span><span class="lang-ko" lang="ko"><strong>그림 3.</strong> 랜드마크 사용 여부에 따른 Grad-CAM(초록 테두리: 진짜, 빨간 테두리: 가짜). LBD를 쓰면 주의가 화면 전체가 아니라 눈과 입에 집중됩니다.</span><span class="lang-zh" lang="zh-Hans"><strong>图 3.</strong> 使用与不使用关键点的 Grad-CAM（绿框：真实，红框：伪造）。使用 LBD 后，注意力集中在眼睛和嘴部，而非整个画面。</span></figcaption>
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
