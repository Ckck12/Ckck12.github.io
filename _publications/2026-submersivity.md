---
title: "Submersivity: XR-Guided Garbage Detection with an RC Submarine for Peter Street Basin Cleanup"
pub_id: submersivity
authors: "Chris McGale, Michel A. Herrera Viyella, Chan Park, Daniel Bros, Alexander Vicol, Steve Mann"
author_list:
  - name: "Chris McGale"
    sup: "1"
  - name: "Michel A. Herrera Viyella"
    sup: "1"
  - name: "Chan Park"
    sup: "2"
    me: true
  - name: "Daniel Bros"
  - name: "Alexander Vicol"
    sup: "1"
  - name: "Steve Mann"
    sup: "1,*"
affiliations:
  - "<sup>1</sup>Department of Electrical and Computer Engineering, University of Toronto"
  - "<sup>2</sup>Department of Mechanical and Industrial Engineering, University of Toronto"
author_notes: "* Corresponding author"
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
tldr_en:
  - "Submerged garbage in Toronto's Peter Street Basin is hard to see; this system turns RC-submarine video into guidance on XR swim goggles."
  - "A YOLOv8s detector finds debris, COLMAP + ArUco place it on a basin map, and a Vuzix HUD shows direction, distance and class."
  - "My part: the detector (TACO mAP50 0.27 → 0.49, multi-class YOLOv8n → single-class YOLOv8s; 92.1% precision) and the Android HUD receiver."
tldr_ko:
  - "토론토 Peter Street Basin의 물속 쓰레기는 잘 보이지 않습니다. 이 시스템은 RC 잠수정 영상을 XR 수경의 안내 화면으로 바꿉니다."
  - "YOLOv8s가 쓰레기를 탐지하고, COLMAP + ArUco가 위치를 수조 지도에 올리며, Vuzix HUD가 방향, 거리, 클래스를 보여 줍니다."
  - "담당: 탐지 모델(다중 클래스 YOLOv8n → 단일 클래스 YOLOv8s로 TACO mAP50 0.27 → 0.49, 정밀도 92.1%)과 안드로이드 HUD 수신 앱."
excerpt: "XR-guided underwater garbage detection with an RC submarine, a YOLOv8s detector, basin localization and a swim-goggle HUD (IEEE ICMA 2026, 3rd author)."
meta_in_body: true
featured: false
collection: publications
---

{% include lang-toggle.html %}

{% include projects/submersivity.html %}
