---
title: "Driving VLA: Unobservable Claims in Language-Conditioned Driving"
excerpt: "Independent research (Sep. 2026 – present): auditing whether a driving VLA's auto-generated commentary names causes the camera cannot see, and measuring how that language steers control.<br/><img src='/images/projects/driving_vla.jpg' alt='CARLA frame with a hidden hazard behind a hedge'>"
collection: portfolio
---

![CARLA front-camera frame with the hidden-hazard region boxed](/images/projects/driving_vla.jpg)

**Independent research, Sep. 2026 – Present.** Model: SimLingo, a CARLA driving VLA that generates a natural-language commentary and then predicts waypoints/speed conditioned on it. Its training commentary is auto-generated from simulator-privileged information. Status: manuscript in preparation (target: NAACL). All numbers below are open-loop.

- **Situation:** SimLingo's commentary is supervised by simulator-privileged information, so the model can be trained to state causes that are not visible in the camera image.
- **Task:** Quantify how often the commentary makes camera-unobservable claims and whether that language actually influences the predicted control.
- **Action:** Audited all 2,085,459 auto-generated commentary records (37 files); ran open-loop intervention experiments that swap only the cause clause, re-inject identical text, delete unsupported sentences, or rewrite them with hedges, with placebo edits as controls.
- **Result (open-loop):** 5.48% of cause-bearing records name a cause the camera cannot see (up to 38.1% in signalized-junction right-turn scenarios); swapping the cause clause changes predicted speed by +0.85 m/s (95% route-bootstrap CI [0.69, 1.02]; 300 frames / 119 routes) while identical-text re-injection changes it by 0.000; deleting unsupported sentences causes a 40.7% [30.8, 51.4] caution loss vs. 2.5% for hedged rewriting (204 hidden-hazard braking frames).
- **Infrastructure:** Built closed-loop evaluation on CARLA 0.9.15 + Bench2Drive (220 routes) on cloud GPU VMs (diagnosed Vulkan failures in containers and moved to KVM VMs for GPU rendering), parallel route runners, and a simulator-ground-truth hallucination scorer (CHAIR). Closed-loop results are pending; a hazard-belief module that moves decision-relevant hazard information out of the commentary is being designed and evaluated.

Related: [Route-Guided CBF-QP Repair](/publications/2026-route-guided-cbf-qp/) (safety layer for robot VLA policies).
