---
title: "Salience, Not Visibility: Privileged Supervision Puts Unobservable Claims into a Driving VLA's Control Loop"
pub_id: driving-vla
authors: "Chan Park"
author_list:
  - name: "Chan Park"
    me: true
affiliations:
  - "Independent research"
venue: ""
status: "Manuscript in preparation (target: NAACL)"
year: 2026
date: 2026-10-01   # year-level only; used for ordering
order: 1
teaser: driving_camera.jpg
teaser_hover: driving_occlusion.jpg
paper_url:
code_url:
project_url:
slides_url:
tldr: "Auto-generated driving commentary often names causes that the generator's own LiDAR visibility flag marks as not visible, and the VLA treats that language as a control input (open-loop analysis; closed-loop evaluation pending)."
tldr_en:
  - "SimLingo, a CARLA driving VLA, writes a commentary and then drives conditioned on it; its training commentary is auto-generated from privileged simulator state."
  - "Across all 2,085,459 records, 5.48% of cause-bearing ones cite a cause that the generator's own visibility flag marks as not visible."
  - "Open-loop, this language steers control: swapping only the cause clause shifts predicted speed by +0.85 m/s (one scenario type); deleting unsupported sentences also deletes braking cues."
tldr_ko:
  - "CARLA 주행 VLA인 SimLingo는 주석 문장을 쓴 뒤 그 문장을 조건으로 운전하며, 학습용 주석은 특권 시뮬레이터 정보로 자동 생성됩니다."
  - "기록 2,085,459건을 전수 감사한 결과, 원인을 언급한 기록의 5.48%가 생성기 자체 가시성 플래그상 “안 보임”인 원인을 언급했습니다."
  - "개루프 실험에서 이 언어는 제어를 바꿉니다. 원인절만 바꿔도 예측 속도가 +0.85 m/s 변하고(단일 시나리오 유형), 근거 없는 문장을 지우면 제동 신호도 함께 사라집니다."
excerpt: "Independent research on SimLingo: auto-generated driving commentary cites causes its own visibility flag marks as not visible, and the VLA uses that language as a control input (open-loop analysis; manuscript in preparation)."
meta_in_body: true
featured: true
collection: publications
---

{% include lang-toggle.html %}

{% include projects/driving-vla.html %}
