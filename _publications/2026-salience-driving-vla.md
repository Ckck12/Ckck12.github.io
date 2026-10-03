---
title: "Salience, Not Visibility: Privileged Supervision Puts Unobservable Claims into a Driving VLA's Control Loop"
authors: "Chan Park"
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
tldr: "Auto-generated driving commentary often names causes that the generator's own LiDAR visibility flag marks as not visible, and the VLA treats that language as a control input (open-loop analysis; closed-loop evaluation pending)."
featured: true
collection: publications
---

Independent research on SimLingo, a CARLA driving VLA that generates a natural-language commentary and then predicts waypoints and speed conditioned on it. Its training commentary is auto-generated from simulator-privileged information.

**Status:** Manuscript in preparation (target: NAACL). All numbers below are open-loop.

- Audited all 2,085,459 auto-generated commentary records (37 files): 5.48% of cause-bearing records cite a cause that the generator's LiDAR visibility flag marks as not visible; up to 38.1% in signalized-junction right-turn scenarios.
- Language is a control input: swapping only the cause clause of the commentary changes predicted speed by +0.85 m/s (95% route-bootstrap CI [0.69, 1.02]; 300 frames / 119 routes), while re-injecting identical text changes it by 0.000.
- Deleting unsupported sentences also deletes braking cues: caution loss of 40.7% [30.8, 51.4] for deletion vs. 2.5% for hedged rewriting (204 hidden-hazard braking frames); the gap persists under placebo edits.
- Closed-loop evaluation infrastructure: CARLA 0.9.15 + Bench2Drive (220 routes) on cloud GPU VMs, parallel route runners, and a simulator-ground-truth hallucination scorer (CHAIR). Closed-loop results are pending.
