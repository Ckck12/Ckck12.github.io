---
title: "Salience, Not Visibility: Privileged Commentary Supervision in End-to-End Driving VLAs"
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
tldr: "Auto-generated driving commentary cites causes its own LiDAR visibility flag marks as not visible, and the VLA uses that language as a control input (open-loop analysis)."
tldr_list_ko: "자동 생성 주행 주석이 자체 LiDAR 가시성 플래그상 “안 보임”인 원인을 언급하고, VLA는 그 언어를 제어 입력으로 씁니다(개루프 분석)."
tldr_list_zh: "自动生成的驾驶解说会提到被自身 LiDAR 可见性标志标为“不可见”的原因，而 VLA 把这些语言当作控制输入（开环分析）。"
tldr_en:
  - "SimLingo, a CARLA driving VLA, writes a commentary, then drives conditioned on it. Its training commentary is auto-generated from privileged simulator state."
  - "Across all 2,085,459 records, 5.48% of cause-bearing ones cite a cause the generator's own LiDAR visibility flag marks as not visible."
  - "Open-loop: swapping only the cause clause shifts predicted speed by +0.85 m/s (one scenario type). Deleting unsupported sentences also deletes braking cues."
tldr_ko:
  - "CARLA 주행 VLA인 SimLingo는 주석을 먼저 쓰고 그 주석을 조건으로 주행합니다. 학습 주석은 특권 시뮬레이터 상태로 자동 생성됩니다."
  - "기록 2,085,459건 전수 감사: 원인 언급 기록의 5.48%가 생성기 자체 LiDAR 가시성 플래그상 “안 보임”인 원인을 언급합니다."
  - "개루프: 원인절만 바꿔도 예측 속도가 +0.85 m/s 변합니다(단일 시나리오 유형). 근거 없는 문장을 지우면 제동 신호도 사라집니다."
tldr_zh:
  - "SimLingo 是 CARLA 驾驶 VLA，先生成解说，再以解说为条件驾驶；训练解说由仿真器特权状态自动生成。"
  - "全量审查 2,085,459 条记录：含原因的记录中有 5.48% 所述原因被生成器自身 LiDAR 可见性标志标为“不可见”。"
  - "开环实验：仅替换原因从句，预测速度变化 +0.85 m/s（单一场景类型）；删除无依据句子也会删掉制动线索。"
excerpt: "Independent research on SimLingo: auto-generated commentary cites causes its own visibility flag marks as not visible, and the VLA uses that language for control (open-loop analysis; manuscript in preparation)."
meta_in_body: true
featured: true
collection: publications
details_url: /portfolio/01-driving-vla/
redirect_to: /portfolio/01-driving-vla/
---

{% include projects/driving-vla.html %}
