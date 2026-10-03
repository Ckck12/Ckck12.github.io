---
title: "Submersivity: XR-Guided Underwater Debris Detection"
excerpt: "Mann Lab, University of Toronto (Jan. – Jul. 2026): an RC submarine, a YOLOv8s debris detector, basin localization, and an XR goggle HUD for cleanup swimmers. IEEE ICMA 2026.<br/><img src='/images/projects/submersivity.jpg' alt='Peter Street Basin cleanup site'>"
collection: portfolio
---

![Peter Street Basin](/images/projects/submersivity.jpg)

**Mann Lab, University of Toronto, Jan. 2026 – Jul. 2026.** Paper at IEEE ICMA 2026 (3rd author). [Project page](https://ckck12.github.io/Submersivity-XR-Guided-Garbage-Detection-with-an-RC-Submarine/) · [Code](https://github.com/Ckck12/Submersivity-XR-Guided-Garbage-Detection-with-an-RC-Submarine)

- **Situation:** Cleanup swimmers at the Peter Street Basin in Toronto (approximately 60 m × 30 m) cannot see submerged debris from the surface.
- **Task:** Detect debris from an RC submarine's camera and guide a swimmer to it through XR goggles.
- **Action:** End-to-end system: the RC submarine (ESP-DIVE, XIAO ESP32-S3 camera) streams frames to a YOLOv8s detector; a basin localization stage (COLMAP + ArUco, 796 / 823 frames registered) places detections; Vuzix Smart Swim XR goggles show distance, direction, and class on a HUD. I trained the detector and built the native Android XR HUD receiver (byte-stream packet reassembly, 500 ms stale-data detection, auto-reconnect, link-quality indicator).
- **Result:** Moving from a multi-class YOLOv8n to a single-class YOLOv8s raised mAP50 on the TACO validation split from 0.27 to 0.49; the final model, fine-tuned on a combined 12,177-image terrestrial + underwater dataset, reaches 92.1% precision.
