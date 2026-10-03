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
* **LG Electronics, Toronto AI Lab** — AI Research Intern, Robot Learning / VLA Safety, Jan. 2026 – Aug. 2026 ([project page](https://ckck12.github.io/Route-Guied-CBF-QP/))
  * Improved collision avoidance from 64.7% to 69.1% (average over 4 SafeLIBERO suites, vs. AEGIS) with Route-Guided CBF-QP Repair: soft route guidance and hard safety constraints; ablated scoring and rejoin guidance.
  * Built MuJoCo / RoboCasa simulations, expanded VR demonstrations with MimicGen, and LoRA fine-tuned OpenVLA, π0.5, DreamVLA, and DD-VLA with multi-GPU Slurm training.
* **DASH Lab, Sungkyunkwan University** — Graduate Researcher, Multimodal Learning / Deepfake Detection, Mar. 2024 – Dec. 2025 ([project page](https://github.com/Ckck12/ICR-Net))
  * Maintained ≥92.9% video accuracy across all 8 DF-TCB temporal corruptions on FF++ (97.9% clean) with ICR-Net's frame-reliability prediction and selective correction; built DF-TCB on FaceForensics++ and DFDC.
  * Led working-level execution of IITP deepfake R&D; deployed Dockerized detection REST APIs to the Korean National Police Agency and Supreme Prosecutors' Office.
* **Voice AI Lab, Inha University** — Undergraduate Research Assistant, Aug. 2022 – Aug. 2023
  * Built synchronized EMG, EEG, audio, and video acquisition for silent-speech research; standardized filtering and acquisition (EMG: 1,000 Hz; audio: 16 kHz / 16-bit).

Projects
======
* **Driving VLA: Unobservable Claims in Language-Conditioned Driving** — Independent research (SimLingo / CARLA), Sep. 2026 – Present
  * Identified camera-unobservable causes in 5.48% of cause-bearing records (up to 38.1% for signalized-junction right turns) by auditing all 2,085,459 auto-generated commentary records.
  * Measured +0.85 m/s predicted-speed change by swapping cause clauses (open-loop; 95% route-bootstrap CI [0.69, 1.02]; 300 frames / 119 routes); identical-text reinjection changed speed by 0.000.
  * Built CARLA / Bench2Drive closed-loop evaluation infrastructure with CHAIR scoring; results pending.
* **LLM Agent Research Pipeline with Cross-Model Review** — Personal project, Sep. 2026
  * Built a Python cross-model-family reviewer harness on the open-source ARIS skill pack for Claude Code: env-file API keys, multi-model fallback with back-off, provenance headers, and Codex CLI as second reviewer.
* **Submersivity: XR-Guided Underwater Debris Detection** — Mann Lab, University of Toronto, Jan. 2026 – Jul. 2026 ([project page](https://ckck12.github.io/Submersivity-XR-Guided-Garbage-Detection-with-an-RC-Submarine/))
  * Raised TACO validation mAP50 from 0.27 to 0.49 via single-class training; reached 92.1% precision after fine-tuning on 12,177 terrestrial and underwater images.
* **MetaForensic** — KIISE Deepfake Competition, red-team track, Jun. 2025
  * Built partial-manipulation deepfakes from 590 videos; earned the Excellence Award (Jul. 2025).
* **Undergraduate projects** — see the [Projects](/portfolio/) page.

Publications
======
1. **Park, C.** "Salience, Not Visibility: Privileged Supervision Puts Unobservable Claims into a Driving VLA's Control Loop." Manuscript in preparation (target: NAACL).
2. **Park, C.**, Hong, S., Kim, K., Park, Y., Olson, A.W., and Pesaranghader, A. "Route-Guided CBF-QP Repair for Safe Vision-Language-Action Models." Workshop on Trustworthy Embodied Foundation Models, Robotics: Science and Systems (RSS) 2026. [[Project Page](https://ckck12.github.io/Route-Guied-CBF-QP/)] [[Code](https://github.com/Ckck12/Route-Guied-CBF-QP)]
3. McGale, C., Herrera Viyella, M.A., **Park, C.**, Bros, D., Vicol, A., and Mann, S. "Submersivity: XR-Guided Garbage Detection with an RC Submarine for Peter Street Basin Cleanup." IEEE International Conference on Mechatronics and Automation (ICMA) 2026. [[Project Page](https://ckck12.github.io/Submersivity-XR-Guided-Garbage-Detection-with-an-RC-Submarine/)]
4. Vicol, A., Mann, S., McGale, C., Bejar-Alvarez, G., Herrera Viyella, M., Zanjanpour, D., **Park, C.**, Chen, X., Yang, X., and Yang, B. "State-of-Flight: Wearable EEG and Motion Context During Flying or Floating." IEEE International Symposium on Computer-Based Medical Systems (CBMS) 2026.
5. **Park, C.**, Choi, H., Muneer, M.S., Le, B.M., and Woo, S.S. "ICR-Net: Robust Deepfake Detection under Temporal Corruption." Pacific-Asia Conference on Knowledge Discovery and Data Mining (PAKDD) 2026. [[Paper](https://doi.org/10.1007/978-981-92-1465-5_24)] [[Code](https://github.com/Ckck12/ICR-Net)]
6. **Park, C.**, Muneer, M.S., and Woo, S.S. "Beyond Masking: Landmark-based Representation Learning and Knowledge-Distillation for Audio-Visual Deepfake Detection." ACM International Conference on Information and Knowledge Management (CIKM) 2025, Short Paper. [[Paper](https://doi.org/10.1145/3746252.3760853)] [[Code](https://github.com/Ckck12/Beyond-Masking)]
7. **Park, C.**\*, Moon, B.\*, Jeon, M., Jung, J., and Woo, S.S. "X3A: Efficient Multimodal Deepfake Detection with Score-Level Fusion." ACM/SIGAPP Symposium on Applied Computing (SAC) 2025. [[Paper](https://doi.org/10.1145/3672608.3707934)]
8. "Weighted Contrastive Learning for Robust Audio-Visual Deepfake Detection." Under review.

\* equal contribution

Patents & Software
======
* Method and Apparatus for Deepfake Detection under Temporal Corruption — Korean patent application filed, 2026.
* Efficient Multimodal Deepfake Detection with Score-Level Fusion — Korean software copyright registration, 2025.

Honors & Awards
======
* Korean Government Scholarship for visiting research at the University of Toronto — IITP "AI Excellence Global Innovative Leader Education Program" (MSIT), 2026.
* Top 10%, [1M-Deepfakes Detection Challenge](https://deepfakes1m.github.io/2025/about), ACM Multimedia 2025.
* Excellence Award, Deepfake Generation & Detection Competition (Team MetaForensic), Korean Institute of Information Scientists and Engineers (KIISE), Jul. 2025.
* SKKU AI Research Fellowship, Sungkyunkwan University, 2024–2026.
* Brain Korea 21 (BK21) Master's Research Fellowship, Sungkyunkwan University, 2024–2026.
* Undergraduate Research Fellowship (×2), Inha University.
* City-funded Future Talent Scholarship, Siheung City.

Skills
======
* **Programming:** Python, C++, Java (Android), SQL
* **ML / CV:** PyTorch, LoRA fine-tuning, multi-GPU training (Slurm), OpenCV, YOLOv8, Hugging Face
* **Simulation / Robotics:** CARLA 0.9.15, Bench2Drive, MuJoCo, RoboCasa, MimicGen, VR teleoperation
* **Systems:** Docker, REST APIs (Flask), Linux, Git, cloud GPU VMs
* **LLM / Agents:** Claude Code, Codex CLI, Gemini API, MCP
* **Languages:** Korean (native); English (OPIc Advanced Low, Sep. 2026)
