---
title: "Weighted Contrastive Learning for Robust Audio-Visual Deepfake Detection"
authors:
venue: ""
status: "Under review"
year:
date: 2025-01-01   # placeholder for ordering only; the submission date is not public
order: 8
teaser: hashformer_teaser.png
teaser_hover: hashformer_hover.png
paper_url:
code_url: https://anonymous.4open.science/r/hashformer-4CE6/
project_url:
tldr: "HashFormer: a lightweight audio-visual deepfake detector with a weighted contrastive-style loss in a binary hash space, designed to stay robust when a modality is missing."
tldr_list_ko: "HashFormer: 이진 해시 공간의 가중 대조 학습형 손실을 쓰는 경량 음성-영상 딥페이크 탐지기로, 한 모달리티가 빠져도 견고하도록 설계되었습니다."
tldr_list_zh: "HashFormer：在二值哈希空间中使用加权对比式损失的轻量级音视频深度伪造检测器，设计上可在缺失某一模态时保持稳健。"
tldr_en:
  - "Audio-visual deepfakes mix face swapping, lip-syncing and voice cloning; global alignment misses fine-grained local cues."
  - "HashFormer learns fine-grained cross- and within-modality features with a weighted contrastive-style loss in a binary hash space."
  - "Modality-specific tokens focus on unimodal clues. The model is designed to stay robust when a modality is missing."
tldr_ko:
  - "음성-영상 딥페이크에는 얼굴 교체, 립싱크, 음성 복제가 섞여 있고, 전역 정렬은 세밀한 국소 단서를 놓칩니다."
  - "HashFormer는 이진 해시 공간의 가중 대조 학습형 손실로 모달리티 간·내부의 세밀한 특징을 학습합니다."
  - "모달리티별 토큰으로 단일 모달리티 단서에 집중합니다. 한 모달리티가 빠져도 견고하도록 설계되었습니다."
tldr_zh:
  - "音视频深度伪造混合了换脸、唇形同步和声音克隆；全局对齐会漏掉细粒度的局部线索。"
  - "HashFormer 在二值哈希空间中用加权对比式损失，学习模态间与模态内的细粒度特征。"
  - "模态专属 token 聚焦单模态线索；模型设计上可在缺失某一模态时保持稳健。"
excerpt: "HashFormer: weighted contrastive alignment in a binary hash space for audio-visual deepfake detection, designed for missing-modality robustness. Under review."
meta_in_body: true
featured: false
collection: publications
---

{% include tldr.html %}

{% include pub-meta.html %}

<div class="proj-body">

<div class="lang-en">
<p><strong>Under review.</strong> This page summarizes the submitted abstract and shows the paper's figures. Authors, venue and results will be added after review. Anonymized code (HashFormer) is available for reviewers.</p>
<h2 class="sec-h">Problem</h2>
<ul>
<li>Audio-visual deepfakes mix face swapping, lip-syncing, reenactment and voice cloning.</li>
<li>Many detectors align audio and video with a standard contrastive loss on <em>global</em> features.</li>
<li>That loss overlooks fine-grained local cues, such as facial attributes and acoustic patterns.</li>
<li>Two-stream models fuse audio and video only at the end, so links within and across modalities are lost.</li>
<li>Most multimodal detectors need every stream and cannot work when one is missing.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<p><strong>심사 중.</strong> 이 페이지는 제출본 초록을 요약하고 논문 그림을 보여 줍니다. 저자, 게재처, 결과는 심사 후 추가합니다. 심사용 익명 코드(HashFormer)가 공개되어 있습니다.</p>
<h2 class="sec-h">문제</h2>
<ul>
<li>음성-영상 딥페이크에는 얼굴 교체, 립싱크, 재연(reenactment), 음성 복제가 섞여 있습니다.</li>
<li>많은 탐지기는 표준 대조 손실로 오디오와 비디오의 <em>전역</em> 특징을 정렬합니다.</li>
<li>이 방식은 얼굴 속성이나 음향 패턴 같은 세밀한 국소 단서를 놓칩니다.</li>
<li>2-스트림 모델은 오디오와 비디오를 마지막에만 결합해, 모달리티 내부·간의 관계를 잃습니다.</li>
<li>대부분의 멀티모달 탐지기는 모든 스트림이 필요하고, 하나가 빠지면 동작하지 못합니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<p><strong>审稿中。</strong>本页概述投稿摘要并展示论文图示。作者、发表处和结果将在审稿结束后补充。已提供供审稿使用的匿名代码（HashFormer）。</p>
<h2 class="sec-h">问题</h2>
<ul>
<li>音视频深度伪造混合了换脸、唇形同步、表情重演（reenactment）和声音克隆。</li>
<li>许多检测器用标准对比损失对齐音频与视频的<em>全局</em>特征。</li>
<li>这种做法会忽略面部属性、声学模式等细粒度局部线索。</li>
<li>双流模型只在最后融合音频与视频，丢失了模态内与模态间的关系。</li>
<li>多数多模态检测器需要所有输入流，缺少其中一路就无法工作。</li>
</ul>
</div>

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/detail/hashformer/fig1_concept.png" alt="Comparison: existing methods use a contrastive loss with global alignment and separate audio and video transformers; HashFormer uses weighted forgery alignment and one modality-agnostic transformer" loading="lazy">
<figcaption><span class="lang-en"><strong>Figure 1.</strong> (a) Existing methods: a contrastive loss with global alignment, and separate audio and video encoders fused at the end. (b) HashFormer: weighted labels <em>W</em> for fine-grained alignment, and one transformer with Selective Forgery Alignment (SFA) that tolerates a missing modality.</span><span class="lang-ko" lang="ko"><strong>그림 1.</strong> (a) 기존 방법: 전역 정렬 대조 손실과, 마지막에 결합하는 별도의 오디오·비디오 인코더. (b) HashFormer: 세밀한 정렬을 위한 가중 라벨 <em>W</em>와, 모달리티가 빠져도 동작하는 Selective Forgery Alignment(SFA) 단일 트랜스포머.</span><span class="lang-zh" lang="zh-Hans"><strong>图 1.</strong> (a) 现有方法：全局对齐的对比损失，加上最后才融合的独立音频、视频编码器。(b) HashFormer：用于细粒度对齐的加权标签 <em>W</em>，以及带 Selective Forgery Alignment（SFA）、可容忍模态缺失的单一 transformer。</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="sec-h">Approach</h2>
<ul>
<li><strong>One temporal transformer, three inputs:</strong> video frames (3D patches), facial landmarks from MediaPipe (1D sequences), and the audio spectrogram (2D patches).</li>
<li><strong>Weighted Forgery Alignment (WFA):</strong> a contrastive-like loss with weighted labels.
<ul>
<li>It compares modality pairs in a binary hash space, which limits noise and outliers from high-bit embeddings.</li>
<li>The weights favor forgery-relevant pairs, so global similarity does not dominate.</li>
<li>Hash codes come from a sign function trained with a straight-through estimator.</li>
</ul></li>
<li><strong>Selective Forgery Alignment (SFA):</strong> a binary attention mask inside the transformer.
<ul>
<li>Modality tokens attend only to their own modality.</li>
<li>Fusion tokens attend to everything and learn cross-modal links.</li>
</ul></li>
<li><strong>Feature distillation:</strong> the model reconstructs masked token embeddings and cross-modal token embeddings (MSE loss).</li>
<li><strong>Missing modalities:</strong> an absent stream becomes a zero-like token, and streams are dropped at random during training.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="sec-h">방법</h2>
<ul>
<li><strong>세 입력을 받는 하나의 시간 트랜스포머:</strong> 비디오 프레임(3D 패치), MediaPipe 얼굴 랜드마크(1D 시퀀스), 오디오 스펙트로그램(2D 패치).</li>
<li><strong>Weighted Forgery Alignment (WFA):</strong> 가중 라벨을 쓰는 대조 학습형 손실입니다.
<ul>
<li>모달리티 쌍을 이진 해시 공간에서 비교해, 고비트 임베딩의 노이즈와 이상치 영향을 줄입니다.</li>
<li>가중치가 위조와 관련된 쌍을 우선하므로, 전역 유사도가 학습을 지배하지 않습니다.</li>
<li>해시 코드는 sign 함수로 만들고, straight-through estimator로 학습합니다.</li>
</ul></li>
<li><strong>Selective Forgery Alignment (SFA):</strong> 트랜스포머 안의 이진 어텐션 마스크입니다.
<ul>
<li>모달리티 토큰은 자기 모달리티에만 어텐션합니다.</li>
<li>융합(fusion) 토큰은 모든 토큰에 어텐션하며 모달리티 간 관계를 학습합니다.</li>
</ul></li>
<li><strong>특징 증류:</strong> 마스킹된 토큰 임베딩과 모달리티 간 토큰 임베딩을 복원합니다(MSE 손실).</li>
<li><strong>모달리티 결손 대응:</strong> 빠진 스트림은 zero-like 토큰으로 대체하고, 학습 중 스트림을 무작위로 제거합니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="sec-h">方法</h2>
<ul>
<li><strong>一个时序 transformer，三路输入：</strong>视频帧（3D patch）、MediaPipe 面部关键点（1D 序列）和音频频谱图（2D patch）。</li>
<li><strong>Weighted Forgery Alignment（WFA）：</strong>带加权标签的类对比损失。
<ul>
<li>在二值哈希空间中比较模态对，减少高比特嵌入中噪声和离群点的影响。</li>
<li>权重偏向与伪造相关的样本对，避免全局相似度主导学习。</li>
<li>哈希码由 sign 函数生成，并用 straight-through estimator 训练。</li>
</ul></li>
<li><strong>Selective Forgery Alignment（SFA）：</strong>transformer 内部的二值注意力掩码。
<ul>
<li>模态 token 只关注本模态。</li>
<li>融合（fusion）token 可关注全部 token，学习跨模态关系。</li>
</ul></li>
<li><strong>特征蒸馏：</strong>重建被遮挡的 token 嵌入和跨模态 token 嵌入（MSE 损失）。</li>
<li><strong>模态缺失：</strong>缺失的输入流以近似零的 token 代替，训练时随机丢弃输入流。</li>
</ul>
</div>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/hashformer/fig2_overview.png" alt="HashFormer overview: (a) training pipeline with video, landmark and audio tokens, hash heads and a binary classifier; (b) weighted forgery alignment in hash space; (c) feature distillation by token reconstruction" loading="lazy">
<figcaption><span class="lang-en"><strong>Figure 2.</strong> HashFormer overview. (a) Training pipeline: video, landmark and audio tokens pass through the SFA transformer, then hash heads and a binary real/fake classifier. (b) WFA: weighted labels built from high-bit similarities supervise low-bit hash similarities. (c) Feature distillation: masked and cross-modal token reconstruction.</span><span class="lang-ko" lang="ko"><strong>그림 2.</strong> HashFormer 개요. (a) 학습 파이프라인: 비디오·랜드마크·오디오 토큰이 SFA 트랜스포머를 거쳐 해시 헤드와 진짜/가짜 이진 분류기로 갑니다. (b) WFA: 고비트 유사도로 만든 가중 라벨이 저비트 해시 유사도를 지도합니다. (c) 특징 증류: 마스킹 토큰 복원과 모달리티 간 토큰 복원.</span><span class="lang-zh" lang="zh-Hans"><strong>图 2.</strong> HashFormer 概览。(a) 训练流程：视频、关键点和音频 token 经过 SFA transformer，再进入哈希头和真/假二分类器。(b) WFA：由高比特相似度构建的加权标签监督低比特哈希相似度。(c) 特征蒸馏：遮挡 token 重建与跨模态 token 重建。</span></figcaption>
</figure>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/hashformer/fig3_sfa.png" alt="SFA attention: (a) attention map where fusion-token rows attend to all keys and modality rows attend only within their modality; (b) attention flow between audio, video and landmark tokens" loading="lazy">
<figcaption><span class="lang-en"><strong>Figure 3.</strong> SFA attention. (a) Attention mask: red cells may attend. Fusion-token rows see every key; modality rows see only their own modality. (b) Flow: fusion tokens exchange information across audio, video and landmarks; modality tokens stay within themselves.</span><span class="lang-ko" lang="ko"><strong>그림 3.</strong> SFA 어텐션. (a) 어텐션 마스크: 빨간 칸만 어텐션할 수 있습니다. 융합 토큰 행은 모든 키를, 모달리티 행은 자기 모달리티만 봅니다. (b) 흐름: 융합 토큰은 오디오·비디오·랜드마크 사이에서 정보를 주고받고, 모달리티 토큰은 자기 안에서만 어텐션합니다.</span><span class="lang-zh" lang="zh-Hans"><strong>图 3.</strong> SFA 注意力。(a) 注意力掩码：红色格子表示可以关注。融合 token 行可看到所有 key，模态行只看到本模态。(b) 信息流：融合 token 在音频、视频和关键点之间交换信息，模态 token 只在本模态内部关注。</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="sec-h">Qualitative figures</h2>
<ul>
<li><strong>Figure 4</strong> compares learned features under four training losses, using t-SNE.</li>
<li><strong>Figure 5</strong> shows where the video fusion tokens attend on face-swap and lip-sync samples.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="sec-h">정성적 그림</h2>
<ul>
<li><strong>그림 4</strong>는 네 가지 학습 손실로 얻은 특징을 t-SNE로 비교합니다.</li>
<li><strong>그림 5</strong>는 얼굴 교체·립싱크 샘플에서 비디오 융합 토큰이 어디에 어텐션하는지 보여 줍니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="sec-h">定性图示</h2>
<ul>
<li><strong>图 4</strong>用 t-SNE 比较四种训练损失下学到的特征。</li>
<li><strong>图 5</strong>展示在换脸和唇形同步样本上，视频融合 token 的注意力落在何处。</li>
</ul>
</div>

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/detail/hashformer/fig4_tsne.png" alt="t-SNE plots of learned features under four losses, colored by real samples and fakes from DF-TIMIT, DFDC, FakeAVCeleb, DeepSpeak and KoDF" loading="lazy">
<figcaption><span class="lang-en"><strong>Figure 4.</strong> t-SNE of learned features. (a) Standard cross-entropy. (b) Contrastive loss. (c) Weighted contrastive loss without hashing. (d) The proposed relaxed-hash weighted contrastive loss. Dark blue: real; other colors: fakes from DF-TIMIT, DFDC, FakeAVCeleb, DeepSpeak and KoDF.</span><span class="lang-ko" lang="ko"><strong>그림 4.</strong> 학습된 특징의 t-SNE. (a) 표준 cross-entropy. (b) 대조 손실. (c) 해싱 없는 가중 대조 손실. (d) 제안한 relaxed-hash 가중 대조 손실. 진한 파랑: 진짜, 나머지 색: DF-TIMIT, DFDC, FakeAVCeleb, DeepSpeak, KoDF의 가짜.</span><span class="lang-zh" lang="zh-Hans"><strong>图 4.</strong> 所学特征的 t-SNE。(a) 标准 cross-entropy。(b) 对比损失。(c) 不带哈希的加权对比损失。(d) 本文提出的 relaxed-hash 加权对比损失。深蓝：真实样本；其他颜色：来自 DF-TIMIT、DFDC、FakeAVCeleb、DeepSpeak 和 KoDF 的伪造样本。</span></figcaption>
</figure>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/hashformer/fig5_attention.jpg" alt="Attention heat maps over face crops: row (a) face-swap samples, row (b) lip-sync samples" loading="lazy">
<figcaption><span class="lang-en"><strong>Figure 5.</strong> Attention maps of the video fusion tokens. Row (a): face-swap samples. Row (b): lip-sync samples. Warmer colors mean higher attention.</span><span class="lang-ko" lang="ko"><strong>그림 5.</strong> 비디오 융합 토큰의 어텐션 맵. (a)행: 얼굴 교체 샘플. (b)행: 립싱크 샘플. 따뜻한 색일수록 어텐션이 큽니다.</span><span class="lang-zh" lang="zh-Hans"><strong>图 5.</strong> 视频融合 token 的注意力图。(a) 行：换脸样本。(b) 行：唇形同步样本。颜色越暖，注意力越高。</span></figcaption>
</figure>

</div>
