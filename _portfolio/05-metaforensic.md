---
title: "MetaForensic: Partial-Manipulation Deepfake Attacks (KIISE Excellence Award)"
order: 5
redirect_from:
  - /portfolio/04-metaforensic/
excerpt: "KIISE Deepfake Generation & Detection Competition, red-team track (Jun. 2025): a team partial-manipulation deepfake pipeline that won the Excellence Award (Jul. 2025).<br/><img src='/images/projects/thumb_metaforensic.png' alt='Lip-synthesis pipeline figure'>"
excerpt_ko: "KIISE 딥페이크 생성·탐지 경진대회 레드팀 트랙(2025년 6월): 팀으로 만든 부분 조작 딥페이크 파이프라인으로 우수상 수상(2025년 7월).<br/><img src='/images/projects/thumb_metaforensic.png' alt='Lip-synthesis pipeline figure'>"
excerpt_zh: "KIISE 深度伪造生成与检测竞赛红队赛道（2025年6月）：团队构建的局部篡改深度伪造流程，获优秀奖（2025年7月）。<br/><img src='/images/projects/thumb_metaforensic.png' alt='Lip-synthesis pipeline figure'>"
tldr_en:
  - "The KIISE red-team track asked for deepfakes that spread a false message yet stay hard to detect."
  - "Team MetaForensic changed only the few words or short segments that flip the message, across 590 silent videos."
  - "Result: Excellence Award (우수상), Korean Institute of Information Scientists and Engineers (KIISE), Jul. 2025."
tldr_ko:
  - "KIISE 경진대회 레드팀 트랙은 탐지되기 어려우면서 허위 메시지를 퍼뜨리는 딥페이크를 요구했습니다."
  - "MetaForensic 팀은 무음 영상 590개에서 메시지를 뒤집는 몇 단어나 짧은 구간만 바꿨습니다."
  - "결과: KIISE(한국정보과학회) 우수상, 2025년 7월."
tldr_zh:
  - "KIISE 红队赛道要求生成既能传播虚假信息、又难以被检测的深度伪造视频。"
  - "MetaForensic 团队在 590 个无声视频中，只改动足以反转信息的少数词语或短片段。"
  - "结果：KIISE 优秀奖（우수상），2025年7月。"
collection: portfolio
---

{% include tldr.html %}

<div class="proj-body">

<div class="lang-en">
<p><strong>Team MetaForensic · KIISE Deepfake Generation &amp; Detection Competition, red-team track, Jun. 2025 · Excellence Award, Jul. 2025.</strong> A team project: our team co-built the pipeline below.</p>
<h2 class="star-h"><span class="star-tag">S</span>Situation</h2>
<ul>
<li>The red-team track asked for deepfakes that spread a false message yet stay hard to detect.</li>
<li>Most detectors are built for scenarios where the whole image or video is fake. They are weak on partial edits.</li>
<li>Changing a few words, or the face for a moment, can still distort what a video says.</li>
</ul>
<h2 class="star-h"><span class="star-tag">T</span>Task</h2>
<ul>
<li>Input: 590 source videos with no audio track.</li>
<li>Goal: alter only the frames needed to change the message. Keep everything else real.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<p><strong>MetaForensic 팀 · KIISE 딥페이크 생성·탐지 경진대회 레드팀 트랙, 2025년 6월 · 우수상, 2025년 7월.</strong> 팀 프로젝트로, 아래 파이프라인을 팀원들과 함께 만들었습니다.</p>
<h2 class="star-h"><span class="star-tag">S</span>상황</h2>
<ul>
<li>레드팀 트랙은 탐지되기 어려우면서 허위 메시지를 퍼뜨리는 딥페이크를 요구했습니다.</li>
<li>대부분의 탐지기는 이미지나 영상 전체가 조작된 경우를 전제로 설계되어, 부분 조작에 약합니다.</li>
<li>몇 단어나 아주 짧은 순간의 얼굴만 바꿔도 영상의 의미를 왜곡할 수 있습니다.</li>
</ul>
<h2 class="star-h"><span class="star-tag">T</span>과제</h2>
<ul>
<li>입력: 오디오 트랙이 없는 원본 영상 590개.</li>
<li>목표: 메시지를 바꾸는 데 꼭 필요한 프레임만 조작하고, 나머지는 진짜 그대로 둔다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<p><strong>MetaForensic 团队 · KIISE 深度伪造生成与检测竞赛红队赛道，2025年6月 · 优秀奖，2025年7月。</strong>这是团队项目：下面的流程由我们团队共同搭建。</p>
<h2 class="star-h"><span class="star-tag">S</span>情境</h2>
<ul>
<li>红队赛道要求生成既能传播虚假信息、又难以被检测的深度伪造视频。</li>
<li>多数检测器针对整张图像或整段视频被篡改的场景设计，对局部篡改较弱。</li>
<li>只改几个词，或在一瞬间换掉人脸，就足以扭曲视频的含义。</li>
</ul>
<h2 class="star-h"><span class="star-tag">T</span>任务</h2>
<ul>
<li>输入：590 个没有音轨的原始视频。</li>
<li>目标：只篡改改变信息所必需的帧，其余部分保持真实。</li>
</ul>
</div>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/metaforensic/fig1_pipeline.png" alt="MetaForensic overall pipeline: an original video goes either to a lip synthesis module or to an identity swap module, each producing a fake video" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 1.</strong> Overall pipeline. Lip synthesis changes <em>what</em> the speaker says. Identity swap changes <em>who</em> appears. Both output a mostly real video.</span><span class="lang-ko" lang="ko"><strong>그림 1.</strong> 전체 파이프라인. 입술 합성은 화자가 <em>무엇을</em> 말하는지를, 얼굴 교체는 <em>누가</em> 나오는지를 바꿉니다. 두 갈래 모두 대부분이 진짜인 영상을 출력합니다.</span><span class="lang-zh" lang="zh-Hans"><strong>图 1.</strong> 整体流程。唇形合成改变说话人<em>说了什么</em>，换脸改变<em>出现的是谁</em>。两个分支输出的视频大部分仍是真实的。</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">A</span>Action</h2>
<h3>Step 0: read the speech without audio</h3>
<ul>
<li>Auto-AVSR (visual speech recognition, VSR) transcribed each silent video from lip movements.</li>
<li>VSR is less accurate than audio speech recognition, so we reviewed the transcripts.</li>
<li>Unreliable text or too little speech &rarr; identity-swap branch (154 videos, 26.1%).</li>
<li>All others &rarr; lip-synthesis branch (436 videos, 73.9%).</li>
</ul>
<h3>Lip-synthesis branch (436 videos)</h3>
<ol>
<li>An LLM (Claude Sonnet 4) replaces at least 3 key or emotion words with opposite or contrasting words.</li>
<li>Kokoro TTS turns the edited script into speech (WAV).</li>
<li>MuseTalk generates lip motion for the new speech while keeping the speaker's face identity.</li>
<li>Montreal Forced Aligner returns the start and end time of every word.</li>
<li>Only the frames of the changed words are swapped with the synthesized ones.</li>
</ol>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">A</span>행동</h2>
<h3>0단계: 오디오 없이 발화 읽기</h3>
<ul>
<li>Auto-AVSR(시각 음성 인식, VSR)로 무음 영상의 입 모양에서 발화 텍스트를 추출했습니다.</li>
<li>VSR은 오디오 기반 음성 인식보다 정확도가 낮아, 추출된 텍스트를 검토했습니다.</li>
<li>텍스트가 부정확하거나 발화가 부족한 영상 &rarr; 얼굴 교체 브랜치 (154개, 26.1%).</li>
<li>나머지 &rarr; 입술 합성 브랜치 (436개, 73.9%).</li>
</ul>
<h3>입술 합성 브랜치 (436개)</h3>
<ol>
<li>LLM(Claude Sonnet 4)이 핵심 단어나 감정 단어를 3개 이상 반의어·대조 어휘로 바꿉니다.</li>
<li>Kokoro TTS가 수정된 스크립트를 음성(WAV)으로 만듭니다.</li>
<li>MuseTalk이 화자의 얼굴 신원을 유지하면서 새 음성에 맞는 입 모양을 생성합니다.</li>
<li>Montreal Forced Aligner가 단어별 시작·종료 시각을 찾습니다.</li>
<li>바뀐 단어 구간의 프레임만 합성 프레임으로 교체합니다.</li>
</ol>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">A</span>行动</h2>
<h3>第 0 步：在没有音频的情况下读出台词</h3>
<ul>
<li>用 Auto-AVSR（视觉语音识别，VSR）从唇部动作中转写每个无声视频。</li>
<li>VSR 的准确率低于基于音频的语音识别，因此我们检查了转写文本。</li>
<li>文本不可靠或台词太少 &rarr; 换脸分支（154 个视频，26.1%）。</li>
<li>其余 &rarr; 唇形合成分支（436 个视频，73.9%）。</li>
</ul>
<h3>唇形合成分支（436 个视频）</h3>
<ol>
<li>LLM（Claude Sonnet 4）把至少 3 个关键词或情感词替换为反义或语境相反的词。</li>
<li>Kokoro TTS 把修改后的脚本转成语音（WAV）。</li>
<li>MuseTalk 在保留说话人身份的同时，生成与新语音匹配的唇形。</li>
<li>Montreal Forced Aligner 给出每个词的起止时间。</li>
<li>只把被改词语对应的帧替换为合成帧。</li>
</ol>
</div>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/metaforensic/fig2_lip_synthesis.png" alt="Lip-synthesis branch: Auto-AVSR transcript, Claude word edits, Kokoro TTS audio, MuseTalk lip generation, and Montreal Forced Aligner frame swap" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 2.</strong> Lip-synthesis branch. Auto-AVSR reads the script from the silent video. The LLM edits key words (&ldquo;discussion&rdquo; &rarr; &ldquo;fight&rdquo;, &ldquo;question&rdquo; &rarr; &ldquo;insult&rdquo;). Kokoro TTS and MuseTalk create matching lip motion. Montreal Forced Aligner locates the changed words, and only those frames are swapped.</span><span class="lang-ko" lang="ko"><strong>그림 2.</strong> 입술 합성 브랜치. Auto-AVSR이 무음 영상에서 스크립트를 읽고, LLM이 핵심 단어를 바꿉니다(&ldquo;discussion&rdquo; &rarr; &ldquo;fight&rdquo;, &ldquo;question&rdquo; &rarr; &ldquo;insult&rdquo;). Kokoro TTS와 MuseTalk이 맞는 입 모양을 만들고, Montreal Forced Aligner가 바뀐 단어 위치를 찾아 그 프레임만 교체합니다.</span><span class="lang-zh" lang="zh-Hans"><strong>图 2.</strong> 唇形合成分支。Auto-AVSR 从无声视频中读出脚本，LLM 修改关键词（&ldquo;discussion&rdquo; &rarr; &ldquo;fight&rdquo;，&ldquo;question&rdquo; &rarr; &ldquo;insult&rdquo;）。Kokoro TTS 与 MuseTalk 生成匹配的唇形，Montreal Forced Aligner 定位被改的词，只替换这些帧。</span></figcaption>
</figure>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/metaforensic/fig3_lip_result.jpg" alt="Lip-synthesis result: real and fake frame strips where only the frame for the word like, changed to unlike, differs" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 3.</strong> Lip-synthesis result. &ldquo;like&rdquo; becomes &ldquo;unlike&rdquo;. Only the frames of that word change (red border); the rest of the clip stays real.</span><span class="lang-ko" lang="ko"><strong>그림 3.</strong> 입술 합성 결과. &ldquo;like&rdquo;가 &ldquo;unlike&rdquo;로 바뀝니다. 그 단어의 프레임(빨간 테두리)만 바뀌고 나머지는 진짜 그대로입니다.</span><span class="lang-zh" lang="zh-Hans"><strong>图 3.</strong> 唇形合成结果。&ldquo;like&rdquo; 变成 &ldquo;unlike&rdquo;。只有该词对应的帧（红框）被改动，其余片段保持真实。</span></figcaption>
</figure>

<div class="lang-en">
<h3>Identity-swap branch (154 videos)</h3>
<ol>
<li>InsightFace detects and crops the face in every frame.</li>
<li>ArcFace turns each face into a 512-dimensional identity embedding.</li>
<li>Cosine similarity picks representative frames and drops low-quality or duplicate candidates.</li>
<li>SimSwap puts the source identity on the target face. It keeps expression, gaze direction and head angle.</li>
<li>1&ndash;3 swapped segments of 15&ndash;45 frames (about 0.5&ndash;1.5 s) replace the same spans of the real video.</li>
</ol>
<ul>
<li><strong>Why 15&ndash;45 frames:</strong> the length matches natural motions such as lowering or turning the head, coughing, or covering the face.</li>
<li>Segments are spaced apart, so the edits do not form a repeated pattern.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h3>얼굴 교체 브랜치 (154개)</h3>
<ol>
<li>InsightFace로 모든 프레임에서 얼굴을 검출하고 잘라냅니다.</li>
<li>ArcFace가 각 얼굴을 512차원 신원(identity) 임베딩으로 바꿉니다.</li>
<li>코사인 유사도로 대표 프레임을 고르고, 품질이 낮거나 중복된 후보는 버립니다.</li>
<li>SimSwap이 소스 신원을 타깃 얼굴에 입힙니다. 표정, 시선 방향, 고개 각도는 유지됩니다.</li>
<li>15&ndash;45프레임(약 0.5&ndash;1.5초) 길이의 교체 구간 1&ndash;3개로 실제 영상의 같은 구간을 바꿉니다.</li>
</ol>
<ul>
<li><strong>15&ndash;45프레임인 이유:</strong> 고개를 숙이거나 돌리기, 기침하기, 손으로 얼굴 가리기 같은 자연스러운 동작 길이와 맞습니다.</li>
<li>구간 사이에 충분한 간격을 두어, 조작이 반복 패턴으로 드러나지 않게 했습니다.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h3>换脸分支（154 个视频）</h3>
<ol>
<li>InsightFace 在每一帧中检测并裁剪人脸。</li>
<li>ArcFace 把每张人脸转换为 512 维身份（identity）嵌入。</li>
<li>用余弦相似度挑选代表性帧，剔除低质量或重复的候选。</li>
<li>SimSwap 把源身份换到目标人脸上，同时保留表情、视线方向和头部角度。</li>
<li>用 1&ndash;3 段、每段 15&ndash;45 帧（约 0.5&ndash;1.5 s）的换脸片段替换真实视频中的相同区间。</li>
</ol>
<ul>
<li><strong>为什么是 15&ndash;45 帧：</strong>这一长度与低头、转头、咳嗽、用手遮脸等自然动作的时长相符。</li>
<li>片段之间留有足够间隔，避免篡改形成重复模式。</li>
</ul>
</div>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/detail/metaforensic/fig4_identity_swap.png" alt="Identity-swap branch: InsightFace identity matching, SimSwap face swapping, and frame swap of short segments into the original video" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 4.</strong> Identity-swap branch. InsightFace/ArcFace matching selects an identity pair. SimSwap creates fake frames. Frame swap puts only short segments back into the real video.</span><span class="lang-ko" lang="ko"><strong>그림 4.</strong> 얼굴 교체 브랜치. InsightFace/ArcFace 매칭으로 신원 쌍을 고르고, SimSwap이 가짜 프레임을 만들며, 프레임 교체로 짧은 구간만 실제 영상에 넣습니다.</span><span class="lang-zh" lang="zh-Hans"><strong>图 4.</strong> 换脸分支。InsightFace/ArcFace 匹配选出身份对，SimSwap 生成伪造帧，帧替换只把短片段放回真实视频。</span></figcaption>
</figure>

<figure class="pfig pfig--narrow">
<img src="{{ site.baseurl }}/images/detail/metaforensic/fig5_faceswap_result.jpg" alt="Identity-swap results: three rows of real target frame plus source identity giving a fake frame" loading="lazy">
<figcaption><span class="lang-en"><strong>Fig. 5.</strong> Identity-swap results: real target frame + source identity = fake frame (red border).</span><span class="lang-ko" lang="ko"><strong>그림 5.</strong> 얼굴 교체 결과: 실제 타깃 프레임 + 소스 신원 = 가짜 프레임(빨간 테두리).</span><span class="lang-zh" lang="zh-Hans"><strong>图 5.</strong> 换脸结果：真实目标帧 + 源身份 = 伪造帧（红框）。</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">R</span>Result</h2>
<ul>
<li>Excellence Award (우수상), Korean Institute of Information Scientists and Engineers (KIISE), Jul. 2025.</li>
<li>All 590 source videos were turned into partial deepfakes: 436 by lip synthesis and 154 by identity swap.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">R</span>결과</h2>
<ul>
<li>KIISE(한국정보과학회) 우수상, 2025년 7월.</li>
<li>원본 영상 590개를 모두 부분 조작 딥페이크로 만들었습니다: 입술 합성 436개, 얼굴 교체 154개.</li>
</ul>
</div>
<div class="lang-zh" lang="zh-Hans">
<h2 class="star-h"><span class="star-tag">R</span>结果</h2>
<ul>
<li>KIISE（Korean Institute of Information Scientists and Engineers）优秀奖（우수상），2025年7月。</li>
<li>590 个原始视频全部制作成局部篡改深度伪造：唇形合成 436 个，换脸 154 个。</li>
</ul>
</div>

</div>
