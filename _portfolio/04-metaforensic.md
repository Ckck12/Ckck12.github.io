---
title: "MetaForensic: Partial-Manipulation Deepfake Attacks (KIISE Excellence Award)"
excerpt: "KIISE Deepfake Generation & Detection Competition, red-team track (Jun. 2025): a partial-manipulation deepfake generation pipeline that won the Excellence Award (Jul. 2025).<br/><img src='/images/projects/metaforensic_lip.jpg' alt='Lip-synthesis pipeline figure'>"
collection: portfolio
---

![Lip-synthesis branch](/images/projects/metaforensic_lip.jpg)

**Team MetaForensic — Deepfake Generation & Detection Competition, Korean Institute of Information Scientists and Engineers (KIISE), red-team track, Jun. 2025. Excellence Award, Jul. 2025.**

- **Situation:** The red-team track asked for deepfakes that spread false messages while staying hard to detect; whole-video manipulation leaves obvious artifacts.
- **Task:** Build a generation pipeline that alters only the frames needed to change the message.
- **Action:** Built a partial-manipulation pipeline over 590 silent source videos. Lip-synthesis branch (436 videos, 73.9%): visual speech recognition (Auto-AVSR), an LLM flips at least 3 key/emotion words, Kokoro TTS, MuseTalk lip generation, Montreal Forced Aligner word timestamps, then swap only the altered-word frames. Identity-swap branch (154 videos, 26.1%, where VSR text was unreliable): ArcFace-embedding candidate selection, SimSwap, then splice 1–3 short segments of 15–45 frames (about 0.5–1.5 s) into the real video.
- **Result:** Excellence Award (우수상), KIISE, Jul. 2025.

![Identity-swap branch](/images/projects/metaforensic_face.jpg)
