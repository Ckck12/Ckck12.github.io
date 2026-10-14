---
title: "Interpretable Autonomous Driving VLA through Belief Distillation (IBD)"
pub_id: driving-vla
authors: "Chan Park"
author_list:
  - name: "Chan Park"
    me: true
venue: ""
status: "Ongoing project"
year: 2026
date: 2026-10-01   # year-level only; used for ordering
order: 1
teaser: driving_camera.jpg
teaser_hover: driving_occlusion.jpg
paper_url:
code_url:
project_url:
slides_url:
tldr: "Ongoing project: keep a driving VLA's commentary honest and move the safety knowledge in privileged simulator labels into a camera-only belief token with a readable risk map."
tldr_list_ko: "진행 중인 프로젝트: 주행 VLA의 해설은 정직하게 두고, 특권 시뮬레이터 라벨의 안전 지식은 사람이 읽을 수 있는 위험 지도를 가진 카메라 기반 신념 토큰으로 옮깁니다."
tldr_list_zh: "进行中的项目：让驾驶 VLA 的解说保持如实，并把特权仿真标签中的安全知识转移到仅依赖相机、带可读风险地图的信念 token 中。"
tldr_en:
  - "A language-conditioned driving VLA writes a commentary and drives conditioned on it, so the commentary is both the explanation people read and an input to control."
  - "Its training labels come from privileged simulator state and can name causes the camera cannot see: safety knowledge, but unverifiable as a sentence."
  - "IBD rewrites the unseen cause into an honest caution phrase and distills the privileged teacher's caution into a camera-only belief token with a readable risk map."
tldr_ko:
  - "언어 기반 주행 VLA는 해설을 먼저 쓰고 그 해설을 조건으로 주행합니다. 해설은 사람이 읽는 설명이면서 제어 입력입니다."
  - "학습 라벨은 특권 시뮬레이터 상태로 만들어져 카메라가 볼 수 없는 원인을 말할 수 있습니다. 안전 지식이지만, 문장으로는 검증할 수 없는 주장입니다."
  - "IBD는 보이지 않는 원인을 정직한 주의 문구로 바꾸고, 특권 교사의 감속을 위험 지도로 읽을 수 있는 카메라 기반 신념 토큰으로 증류합니다."
tldr_zh:
  - "语言条件驾驶 VLA 先写解说、再以解说为条件驾驶，因此解说既是人阅读的解释，也是控制输入。"
  - "其训练标签来自仿真器特权状态，可能提到相机看不到的原因：这是安全知识，但写成句子就无法验证。"
  - "IBD 把看不见的原因改写为如实的提醒用语，并把特权教师的减速蒸馏到仅用相机、带可读风险地图的信念 token 中。"
excerpt: "Ongoing project: honest commentary for a driving VLA, with the safety knowledge in privileged simulator labels moved into a camera-only belief token and risk map."
meta_in_body: true
featured: true
collection: publications
details_url: /portfolio/01-driving-vla/
redirect_to: /portfolio/01-driving-vla/
---

{% include projects/driving-vla.html %}
