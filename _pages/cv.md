---
layout: archive
title: "CV"
permalink: /cv/
author_profile: true
redirect_from:
  - /resume
---

{% include base_path %}

<a href="{{ base_path }}/files/Chan_Park_CV.pdf" class="btn btn--primary">Download CV (PDF)</a>

Education
======
* **Sungkyunkwan University**, Suwon, Korea — M.S. in Artificial Intelligence, Mar. 2024 – Aug. 2026
  * [DASH Lab](https://dash-lab.github.io/); Advisor: [Prof. Simon S. Woo](https://scholar.google.com/citations?user=mHnj60cAAAAJ)
* **University of Toronto**, Toronto, Canada — Visiting graduate student via CARTE, in partnership with LG Electronics Toronto AI Lab, Jan. 2026 – Jul. 2026
  * Dept. of Mechanical and Industrial Engineering; Advisor: [Prof. Steve Mann](https://scholar.google.com/citations?user=7bmQ4FgAAAAJ)
* **Inha University**, Incheon, Korea — B.S. in Industrial Engineering, Mar. 2017 – Aug. 2023

Experience
======
* **LG Electronics, Toronto AI Lab** — AI Research Intern, Robot Learning / VLA Safety (first-author paper, RSS 2026 Workshop), Jan. 2026 – Aug. 2026 ([project page](https://ckck12.github.io/Route-Guied-CBF-QP/))
  * Raised SafeLIBERO collision avoidance 64.7% → 69.1% and SafeSuccess 47.2% → 49.7% over the AEGIS CBF-QP filter (4-suite average) with blocked-path detection and entry–exit–rejoin soft guidance.
  * Built MuJoCo / RoboCasa simulations, expanded VR demonstrations with MimicGen, and LoRA fine-tuned OpenVLA, π0.5, DreamVLA, and DD-VLA with multi-GPU Slurm training.
* **DASH Lab, Sungkyunkwan University** — Graduate Researcher, Multimodal Learning / Deepfake Detection, Mar. 2024 – Dec. 2025 ([GitHub](https://github.com/Ckck12/ICR-Net))
  * Maintained ≥92.9% video accuracy across all 8 DF-TCB temporal corruptions on FF++ (97.9% clean) with ICR-Net's frame-reliability prediction and selective correction; built DF-TCB on FaceForensics++ and DFDC.
  * Led working-level execution of IITP deepfake R&D; deployed Dockerized detection REST APIs to the Korean National Police Agency and Supreme Prosecutors' Office.
* **Voice AI Lab, Inha University** — Undergraduate Research Assistant, Aug. 2022 – Aug. 2023
  * Built synchronized EMG / EEG / audio / video acquisition for silent-speech research (EMG 1,000 Hz; audio 16 kHz).

Projects
======
* **Driving VLA: Unobservable Claims in Language-Conditioned Driving** — Independent research (SimLingo / CARLA), Sep. 2026 – Present
  * Identified camera-unobservable causes in 5.48% of cause-bearing records (up to 38.1% for signalized-junction right turns) by auditing all 2,085,459 auto-generated commentary records.
  * Showed commentary acts as a control input (open-loop): swapping only the cause clause shifted predicted speed by +0.85 m/s (95% CI [0.69, 1.02]); deleting unsupported sentences lost caution in 40.7% of hidden-hazard braking frames vs. 2.5% for hedging.
  * Built CARLA / Bench2Drive closed-loop evaluation infrastructure with CHAIR scoring; results pending.
* **LLM Agent Research Pipeline with Cross-Model Review** — Personal project, Sep. 2026
  * Built a Python cross-model-family reviewer harness on the open-source [ARIS](https://github.com/wanshuiyin/Auto-claude-code-research-in-sleep) skill pack for Claude Code: multi-model fallback with back-off, provenance headers, and Codex CLI as second reviewer.
  * Flash-tier Gemini scored my two proposals 8.2 / 7.63 vs. code-grounded Codex 5.35 / 5.65 (all: revise); provenance showed the 8.2 came from a fallback model; kept objections open; caught a reviewer error (≥2048 vs. 896).
* **Submersivity: XR-Guided Underwater Debris Detection** — Mann Lab, University of Toronto, Jan. 2026 – Jul. 2026 ([project page](https://ckck12.github.io/Submersivity-XR-Guided-Garbage-Detection-with-an-RC-Submarine/))
  * Raised TACO validation mAP50 from 0.27 to 0.49 by moving from multi-class YOLOv8n to single-class YOLOv8s; reached 92.1% precision (combined validation set) after fine-tuning on 12,177 terrestrial and underwater images.
  * Integrated submarine video, YOLOv8s, COLMAP + ArUco, and Vuzix Smart Swim guidance; built the Android XR receiver with packet reassembly, 500 ms stale-data detection, and reconnection.
* **MetaForensic** — KIISE Deepfake Competition, red-team track (team project), Jun. 2025
  * Co-built a lip-synthesis pipeline for 436 of 590 silent videos: visual speech recognition (Auto-AVSR), LLM word flips, Kokoro TTS, and MuseTalk lip sync, swapping only the altered-word frames located by Montreal Forced Aligner.
  * Sent the 154 videos with unreliable transcripts to an identity-swap branch (ArcFace frame selection + SimSwap, 1–3 spliced segments of 15–45 frames); earned the Excellence Award (Jul. 2025).
* **Undergraduate Projects** — details on the [Projects](/portfolio/) page
  * Built the 3D Figure Generation Platform with PIFuHD as PM/back-end developer ([demo](https://aahg.netlify.app)), Apr. 2023.
  * Found 59 of 168 rest areas suitable for hydrogen stations (supervised learning); fine-tuned MobileNet for a Korean food classifier app, 2022.
  * Finished the Autonomous RC-Car Competition with the lightest completing car, Fall 2017.

Publications
======
**Peer-reviewed** (\* equal contribution)

1. **Chan Park**, Sunwoo Hong, Kirak Kim, Yoonjeong Park, Alexander W. Olson, and Ali Pesaranghader. "Route-Guided CBF-QP Repair for Safe Vision-Language-Action Models." Workshop on Trustworthy Embodied Foundation Models, Robotics: Science and Systems (RSS) 2026. [[Project Page](https://ckck12.github.io/Route-Guied-CBF-QP/)] [[Code](https://github.com/Ckck12/Route-Guied-CBF-QP)]
2. Chris McGale, Michel A. Herrera Viyella, **Chan Park**, Daniel Bros, Alexander Vicol, and Steve Mann. "Submersivity: XR-Guided Garbage Detection with an RC Submarine for Peter Street Basin Cleanup." IEEE International Conference on Mechatronics and Automation (ICMA) 2026. [[Project Page](https://ckck12.github.io/Submersivity-XR-Guided-Garbage-Detection-with-an-RC-Submarine/)]
3. Alexander Vicol, Steve Mann, Chris McGale, Gianella Bejar-Alvarez, Michel Herrera Viyella, Darya Zanjanpour, **Chan Park**, Xiaoming Chen, Xueqi Yang, and Bingxuan Yang. "State-of-Flight: Wearable EEG and Motion Context During Flying or Floating." IEEE International Symposium on Computer-Based Medical Systems (CBMS) 2026.
4. **Chan Park**, Hyeongjun Choi, Muhammad Shahid Muneer, Binh Minh Le, and Simon S. Woo. "ICR-Net: Robust Deepfake Detection under Temporal Corruption." Pacific-Asia Conference on Knowledge Discovery and Data Mining (PAKDD) 2026. [[Paper](https://doi.org/10.1007/978-981-92-1465-5_24)] [[Code](https://github.com/Ckck12/ICR-Net)]
5. **Chan Park**, Muhammad Shahid Muneer, and Simon S. Woo. "Beyond Masking: Landmark-based Representation Learning and Knowledge-Distillation for Audio-Visual Deepfake Detection." ACM International Conference on Information and Knowledge Management (CIKM) 2025, Short Paper. [[Paper](https://doi.org/10.1145/3746252.3760853)] [[Code](https://github.com/Ckck12/Beyond-Masking)]
6. **Chan Park**\*, Bohyun Moon\*, Minsun Jeon, Jee-weon Jung, and Simon S. Woo. "X3A: Efficient Multimodal Deepfake Detection with Score-Level Fusion." ACM/SIGAPP Symposium on Applied Computing (SAC) 2025. [[Paper](https://doi.org/10.1145/3672608.3707934)]

**Manuscripts (not yet peer-reviewed)**

7. **Chan Park**. "Salience, Not Visibility: Privileged Supervision Puts Unobservable Claims into a Driving VLA's Control Loop." In preparation (target: NAACL).
8. "Weighted Contrastive Learning for Robust Audio-Visual Deepfake Detection." Under review. [[Code](https://anonymous.4open.science/r/hashformer-4CE6/)]
9. "Where Are the Artifacts? Residual-Guided Representation Learning for Deepfake Detection and Localization." Under review.

Patents & Software
======
* Method and Apparatus for Deepfake Detection under Temporal Corruption — Korean patent application filed, 2026.
* Efficient Multimodal Deepfake Detection with Score-Level Fusion — Korean software copyright registration, 2025.

Honors & Awards
======
* Korean Government Scholarship — IITP "AI Excellence Global Innovative Leader Education Program" (MSIT), 2026.
* Top 10%, [1M-Deepfakes Detection Challenge](https://deepfakes1m.github.io/2025/about), ACM Multimedia 2025.
* Excellence Award, KIISE Deepfake Generation & Detection Competition (Team MetaForensic), Jul. 2025.
* SKKU AI Research Fellowship; BK21 Master's Research Fellowship, Sungkyunkwan University, 2024–2026.
* Undergraduate Research Fellowship (×2), Inha University; City-funded Future Talent Scholarship, Siheung City.

Skills
======
* **Programming / Systems:** Python, C++, Java (Android), SQL; Docker, REST APIs (Flask), Linux, Git, cloud GPU VMs.
* **ML / LLM:** PyTorch, LoRA fine-tuning, multi-GPU training (Slurm), OpenCV, YOLOv8, Hugging Face; Claude Code, Codex CLI, Gemini API, MCP.
* **Simulation / Robotics:** CARLA 0.9.15, Bench2Drive, MuJoCo, RoboCasa, MimicGen, VR teleoperation.
* **Languages:** Korean (native); English (OPIc Advanced Low, Sep. 2026).
