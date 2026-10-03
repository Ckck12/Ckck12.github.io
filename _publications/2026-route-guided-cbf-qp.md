---
title: "Route-Guided CBF-QP Repair for Safe Vision-Language-Action Models"
authors: "Chan Park, Sunwoo Hong, Kirak Kim, Yoonjeong Park, Alexander W. Olson, Ali Pesaranghader"
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
tldr: "A post-hoc repair module that adds a route around unsafe regions to the CBF-QP objective as soft guidance while keeping the safety constraint hard, raising collision avoidance from 64.7% to 69.1% on SafeLIBERO (average over 4 suites, vs. AEGIS)."
featured: true
collection: publications
---

Work done as an AI Research Intern at LG Electronics Toronto AI Lab.

Route-Guided CBF-QP Repair is a post-hoc repair module for VLA policies. It uses the VLA's short-horizon action chunk to detect when the nominal path is blocked by an ellipsoidal unsafe region, generates entry–exit–rejoin route candidates, scores them, and adds the chosen route to the CBF-QP objective as a soft guidance term while the CBF safety constraint stays hard.

On SafeLIBERO (average over 4 suites, vs. the AEGIS CBF-QP safety-filter baseline): collision avoidance 64.7% → 69.1%, task success 62.8% → 65.0%, SafeSuccess 47.2% → 49.7%. Largest gain on the Object suite (collision avoidance 56.3% → 76.3%). An ablation isolates route scoring and rejoin guidance.

Hover the thumbnail on the publications page to compare AEGIS (collision) with route guidance on the same rollout.

[Project page](https://ckck12.github.io/Route-Guied-CBF-QP/) · [Code](https://github.com/Ckck12/Route-Guied-CBF-QP) · [Workshop site](https://robot-fm-safety.github.io/)
