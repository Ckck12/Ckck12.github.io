---
title: "MetaForensic: Partial-Manipulation Deepfake Attacks (KIISE Excellence Award)"
order: 5
redirect_from:
  - /portfolio/04-metaforensic/
excerpt: "KIISE Deepfake Generation & Detection Competition, red-team track (Jun. 2025): a partial-manipulation deepfake generation pipeline that won the Excellence Award (Jul. 2025).<br/><img src='/images/projects/metaforensic_lip.jpg' alt='Lip-synthesis pipeline figure'>"
tldr_en:
  - "The KIISE red-team track asked for deepfakes that spread a false message while staying hard to detect."
  - "Team MetaForensic changed only the few words or short segments that flip the message, across 590 silent videos."
  - "Result: Excellence Award (우수상), Korean Institute of Information Scientists and Engineers, Jul. 2025."
tldr_ko:
  - "한국정보과학회 경진대회 레드팀 트랙은 탐지되기 어려우면서 허위 메시지를 퍼뜨리는 딥페이크를 요구했습니다."
  - "MetaForensic 팀은 무음 영상 590개에서 메시지를 뒤집는 몇 개의 단어나 짧은 구간만 바꾸는 파이프라인을 만들었습니다."
  - "결과: 2025년 7월 한국정보과학회 우수상."
collection: portfolio
---

{% include lang-toggle.html %}

{% include tldr.html %}

<div class="proj-body">

<div class="lang-en">
<p><strong>Team MetaForensic — Deepfake Generation &amp; Detection Competition, Korean Institute of Information Scientists and Engineers (KIISE), red-team track, Jun. 2025. Excellence Award, Jul. 2025.</strong></p>
<h2 class="star-h"><span class="star-tag">S</span>Situation</h2>
<p>The red-team track asked for deepfakes that spread false messages while staying hard to detect. Manipulating a whole video leaves obvious artifacts that detectors pick up.</p>
<h2 class="star-h"><span class="star-tag">T</span>Task</h2>
<p>Build a generation pipeline that alters only the frames needed to change the message.</p>
<h2 class="star-h"><span class="star-tag">A</span>Action</h2>
<p>As part of Team MetaForensic, I co-built a partial-manipulation pipeline over 590 silent source videos, with two branches:</p>
<ul>
<li><strong>Lip-synthesis branch (436 videos, 73.9%):</strong> visual speech recognition (Auto-AVSR) reads what is said; an LLM flips at least 3 key or emotion words; Kokoro TTS speaks the new sentence; MuseTalk generates matching lip motion; Montreal Forced Aligner gives word timestamps, so only the frames of the altered words are swapped.</li>
<li><strong>Identity-swap branch (154 videos, 26.1%, where the VSR text was unreliable):</strong> ArcFace-embedding candidate selection, SimSwap face swapping, then splicing 1–3 short segments of 15–45 frames (about 0.5–1.5 s) into the real video.</li>
</ul>
</div>
<div class="lang-ko" lang="ko">
<p><strong>MetaForensic 팀 — 한국정보과학회(KIISE) 딥페이크 생성·탐지 경진대회 레드팀 트랙, 2025년 6월. 2025년 7월 우수상 수상.</strong></p>
<h2 class="star-h"><span class="star-tag">S</span>상황 (Situation)</h2>
<p>레드팀 트랙은 탐지되기 어려우면서 허위 메시지를 퍼뜨리는 딥페이크를 요구했습니다. 영상 전체를 조작하면 탐지기가 잡아내는 뚜렷한 흔적이 남습니다.</p>
<h2 class="star-h"><span class="star-tag">T</span>과제 (Task)</h2>
<p>메시지를 바꾸는 데 꼭 필요한 프레임만 조작하는 생성 파이프라인을 만든다.</p>
<h2 class="star-h"><span class="star-tag">A</span>행동 (Action)</h2>
<p>MetaForensic 팀의 일원으로 무음 원본 영상 590개를 대상으로 하는 부분 조작 파이프라인을 함께 만들었습니다. 두 갈래로 구성됩니다.</p>
<ul>
<li><strong>입술 합성 브랜치 (436개, 73.9%):</strong> 시각 음성 인식(Auto-AVSR)으로 발화 내용을 읽고, LLM이 핵심어·감정 단어를 3개 이상 바꾸며, Kokoro TTS로 새 문장을 음성으로 만들고, MuseTalk로 입 모양을 생성합니다. Montreal Forced Aligner의 단어 타임스탬프로 바뀐 단어 구간의 프레임만 교체합니다.</li>
<li><strong>얼굴 교체 브랜치 (154개, 26.1%, VSR 텍스트를 신뢰하기 어려운 경우):</strong> ArcFace 임베딩으로 후보를 고르고 SimSwap으로 얼굴을 바꾼 뒤, 15–45프레임(약 0.5–1.5초) 길이의 짧은 구간 1–3개를 실제 영상에 이어 붙입니다.</li>
</ul>
</div>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/projects/metaforensic_lip.jpg" alt="Lip-synthesis branch of the MetaForensic pipeline" loading="lazy">
<figcaption><span class="lang-en">Lip-synthesis branch.</span><span class="lang-ko" lang="ko">입술 합성 브랜치.</span></figcaption>
</figure>

<figure class="pfig">
<img src="{{ site.baseurl }}/images/projects/metaforensic_face.jpg" alt="Identity-swap branch of the MetaForensic pipeline" loading="lazy">
<figcaption><span class="lang-en">Identity-swap branch.</span><span class="lang-ko" lang="ko">얼굴 교체 브랜치.</span></figcaption>
</figure>

<div class="lang-en">
<h2 class="star-h"><span class="star-tag">R</span>Result</h2>
<p>Excellence Award (우수상), Korean Institute of Information Scientists and Engineers (KIISE), Jul. 2025.</p>
</div>
<div class="lang-ko" lang="ko">
<h2 class="star-h"><span class="star-tag">R</span>결과 (Result)</h2>
<p>한국정보과학회(KIISE) 우수상, 2025년 7월.</p>
</div>

</div>
