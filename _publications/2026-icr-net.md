---
title: "ICR-Net: Robust Deepfake Detection under Temporal Corruption"
authors: "Chan Park, Hyeongjun Choi, Muhammad Shahid Muneer, Binh Minh Le, Simon S. Woo"
venue: "Pacific-Asia Conference on Knowledge Discovery and Data Mining (PAKDD) 2026"
status:
year: 2026
date: 2026-04-01   # year-level only; used for ordering
order: 5
teaser: icrnet_corruptions.jpg
teaser_hover: icrnet_method.jpg
paper_url: https://doi.org/10.1007/978-981-92-1465-5_24
code_url: https://github.com/Ckck12/ICR-Net
project_url:
tldr: "Predicts per-frame reliability and selectively corrects corrupted features, keeping video-level accuracy at or above 92.9% on FF++ under every one of 8 live-streaming corruptions (97.9% clean)."
featured: true
collection: publications
---

DASH Lab, Sungkyunkwan University.

We built DF-TCB, a temporal-corruption benchmark on FaceForensics++ and DFDC with 8 corruption types (frame drop/black frame, motion blur, packet loss, bit error, H.264 CRF/ABR, H.265 CRF/ABR) that simulate live-streaming failures. ICR-Net predicts per-frame reliability with a GRU-based frame-integrity module, selectively corrects corrupted features with a 1D-CNN residual correction branch, and aligns clean and corrupted representations with a contrastive objective. On FF++ it keeps video-level accuracy ≥ 92.9% under every one of the 8 temporal corruptions (97.9% clean).

A Korean patent application (Method and Apparatus for Deepfake Detection under Temporal Corruption) was filed in 2026.

[Paper](https://doi.org/10.1007/978-981-92-1465-5_24) · [Code](https://github.com/Ckck12/ICR-Net)
