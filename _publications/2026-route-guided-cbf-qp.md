---
title: "Route-Guided CBF-QP Repair for Safe Vision-Language-Action Models"
pub_id: cbfqp
authors: "Chan Park, Sunwoo Hong, Kirak Kim, Yoonjeong Park, Alexander W. Olson, Ali Pesaranghader"
author_list:
  - name: "Chan Park"
    sup: "1,3,✉"
    me: true
  - name: "Sunwoo Hong"
    sup: "2,3"
  - name: "Kirak Kim"
    sup: "2,3"
  - name: "Yoonjeong Park"
    sup: "2,3"
  - name: "Alexander W. Olson"
    sup: "3"
  - name: "Ali Pesaranghader"
    sup: "4,†"
affiliations:
  - "<sup>1</sup>Sungkyunkwan University"
  - "<sup>2</sup>KAIST"
  - "<sup>3</sup>University of Toronto"
  - "<sup>4</sup>LG Electronics, Toronto AI Lab"
author_notes: "✉ Corresponding author · † Industry advisor"
venue: "RSS 2026 Workshop on Trustworthy Embodied Foundation Models"
status:
year: 2026
date: 2026-07-01   # year-level only; used for ordering
order: 2
teaser: cbfqp_aegis.jpg
teaser_hover: cbfqp_route.jpg
paper_url:
code_url: https://github.com/Ckck12/Route-Guied-CBF-QP
project_url: https://ckck12.github.io/Route-Guied-CBF-QP/
slides_url: /files/Route_Guided_CBF_QP_slides.pdf
extra_links:
  - label: "Workshop"
    url: https://robot-fm-safety.github.io/
tldr: "A post-hoc repair module that adds a route around unsafe regions to the CBF-QP objective as soft guidance while keeping the safety constraint hard, raising collision avoidance from 64.7% to 69.1% on SafeLIBERO (average over 4 suites, vs. AEGIS)."
tldr_en:
  - "A CBF-QP safety filter (AEGIS) keeps each VLA action safe, but can push the robot arm off its task path with no way back."
  - "Route-Guided CBF-QP Repair detects a blocked path and adds an entry–exit–rejoin route as soft guidance; the safety constraint stays hard."
  - "SafeLIBERO, 4-suite average vs. AEGIS: collision avoidance 64.7% → 69.1%, SafeSuccess 47.2% → 49.7% (Object suite: 56.3% → 76.3%)."
tldr_ko:
  - "CBF-QP 안전 필터(AEGIS)는 VLA의 매 동작을 안전하게 만들지만, 로봇 팔을 작업 경로 밖으로 밀어낸 뒤 돌아올 길을 주지 못합니다."
  - "Route-Guided CBF-QP Repair는 막힌 경로를 감지해 진입–이탈–재합류 경로를 소프트 가이던스로 더하고, 안전 제약은 하드로 유지합니다."
  - "SafeLIBERO 4개 스위트 평균, AEGIS 대비: 충돌 회피율 64.7% → 69.1%, SafeSuccess 47.2% → 49.7% (Object 스위트 56.3% → 76.3%)."
excerpt: "Route-Guided CBF-QP Repair: a post-hoc safety repair module that gives VLA robot policies a recoverable route around obstacles (RSS 2026 Workshop, first author)."
meta_in_body: true
featured: true
collection: publications
---

{% include lang-toggle.html %}

{% include projects/cbf-qp.html %}
