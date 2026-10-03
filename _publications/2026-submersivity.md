---
title: "Submersivity: XR-Guided Garbage Detection with an RC Submarine for Peter Street Basin Cleanup"
authors: "Chris McGale, Michel A. Herrera Viyella, Chan Park, Daniel Bros, Alexander Vicol, Steve Mann"
venue: "IEEE International Conference on Mechatronics and Automation (ICMA) 2026"
status:
year: 2026
date: 2026-06-01   # year-level only; used for ordering
order: 3
teaser: submersivity.jpg
teaser_hover: submersivity_hover.jpg
paper_url:
code_url: https://github.com/Ckck12/Submersivity-XR-Guided-Garbage-Detection-with-an-RC-Submarine
project_url: https://ckck12.github.io/Submersivity-XR-Guided-Garbage-Detection-with-an-RC-Submarine/
tldr: "An RC submarine streams frames to a YOLOv8s debris detector, localized with COLMAP + ArUco, and guides a swimmer through an XR goggle HUD."
featured: false
collection: publications
---

Mann Lab, University of Toronto (3rd author).

End-to-end system: an RC submarine (ESP-DIVE, XIAO ESP32-S3 camera) streams frames to a YOLOv8s detector, a basin localization stage (COLMAP + ArUco) places detections in the Peter Street Basin (approximately 60 m × 30 m; 796 / 823 frames registered), and Vuzix Smart Swim XR goggles show distance, direction, and class on a HUD.

My contributions: the detector (moving from a multi-class YOLOv8n to a single-class YOLOv8s raised mAP50 on the TACO validation split from 0.27 to 0.49; the final model, fine-tuned on a combined 12,177-image terrestrial + underwater dataset, reaches 92.1% precision) and the native Android XR HUD receiver (byte-stream packet reassembly, 500 ms stale-data detection, auto-reconnect, link-quality indicator).

[Project page](https://ckck12.github.io/Submersivity-XR-Guided-Garbage-Detection-with-an-RC-Submarine/) · [Code](https://github.com/Ckck12/Submersivity-XR-Guided-Garbage-Detection-with-an-RC-Submarine)
