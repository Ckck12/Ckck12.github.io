---
title: "Part 6 · Separating perception from choice: a world model and a planner"
title_ko: "6편 · 인지와 선택을 분리하기: 월드 모델과 플래너"
title_zh: "第 6 篇 · 把感知与选择分开：世界模型与规划器"
date: 2026-10-07 13:00:00 +0900
permalink: /blog/drone-policy/06-world-model/
series: drone-policy
part: 6
tags: [drone, world-model, planning, cem, evaluation, pytorch]
excerpt: "Part 6 of *Building and Evaluating a Drone VLA from Scratch*. Instead of hoping one network learns to listen, split the job: a world model finds every target without reading the words, a 340-parameter selector lets the words pick one, and a planner flies there. Every pair goes the right way, pair success 6/10, and planning through the learned dynamics fails completely."
excerpt_ko: "연재 6편. 하나의 네트워크가 말을 듣게 되길 바라는 대신 일을 나눴습니다: 월드 모델은 말을 읽지 않고 모든 타깃의 위치를 찾고 340 파라미터짜리 선택기가 말로 그중 하나를 고르고 플래너가 그곳으로 납니다. 모든 쌍이 맞는 방향으로 갈라지고 쌍 성공 6/10. 학습된 동역학으로 계획하면 완전히 실패합니다."
excerpt_zh: "系列第 6 篇：与其指望一个网络学会听指令，不如把任务拆开：世界模型在不读指令的情况下找到所有目标，一个 340 参数的选择器让指令挑出其中一个，规划器飞过去。每一对都飞向了正确方向，pair 成功 6/10；而用学到的动力学做规划则完全失败。"
tldr_en:
  - "The words are only allowed to <b>choose</b>: a world model (575k parameters, no words) estimates where every colour's target is, a 340-parameter selector maps the sentence to a colour, and a sampling planner (CEM) plus a Stop rule fly there."
  - "On the same 10 validation pairs, <b>every pair splits to the right targets</b> (end-to-end models: at most 5). Success 15/20, pair success <b>6/10</b> (95% interval 0.31–0.83, one seed). It reaches the goal region in all 20 flights; the misses are perception error near the goal."
  - "Planning through the world model's own <b>learned dynamics</b> scores 0/20. The planner finds commands whose predicted effect is wrong. Assuming the drone does what it is told (an integrator) works far better. And this comparison with BC is not fair: the modular system also got 3x the states and position labels. Giving BC the same labels and states, separately or together, did not bring it close to 10 of 10."
tldr_ko:
  - "말에게는 <b>고르는 일</b>만 맡깁니다: 월드 모델(57.5만 파라미터, 말 입력 없음)이 각 색깔 타깃의 위치를 추정하고 340 파라미터 선택기가 문장을 색깔로 바꾸고 샘플링 플래너(CEM)와 Stop 규칙이 그곳으로 납니다."
  - "같은 검증 10쌍에서 <b>모든 쌍이 맞는 타깃으로 갈라졌습니다</b>(end-to-end 모델은 많아야 5쌍). 성공 15/20, 쌍 성공 <b>6/10</b>(95% 구간 0.31–0.83, 시드 1개). 20번 모두 목표 영역에 도달했고 실패는 목표 근처의 인지 오차 때문입니다."
  - "월드 모델이 학습한 <b>동역학</b>으로 계획하면 0/20입니다. 플래너가 예측 효과가 틀린 명령을 찾아냅니다. '시킨 대로 움직인다'고 가정하는 적분기가 훨씬 낫습니다. 그리고 BC와의 비교는 공정하지 않습니다: 모듈형 시스템은 3배의 상태와 위치 라벨도 받았습니다. 같은 라벨과 상태를 BC에 따로 주든 함께 주든 BC는 10쌍 모두를 맞게 가르는 수준에는 못 미쳤습니다."
tldr_zh:
  - "只让指令负责<b>选择</b>：世界模型（57.5 万参数，不读指令）估计每种颜色目标的位置，一个 340 参数的选择器把句子映射成颜色，采样规划器（CEM）加 Stop 规则飞过去。"
  - "在同样的 10 个验证对上，<b>每一对都飞向了正确的目标</b>（端到端模型最多 5 对）。成功 15/20，pair 成功 <b>6/10</b>（95% 区间 0.31–0.83，单个种子）。20 次全部到达目标区域，失败来自目标附近的感知误差。"
  - "用世界模型自己<b>学到的动力学</b>做规划得分 0/20：规划器会找到预测效果错误的指令。假设无人机按指令运动（积分器）要好得多。而且与 BC 的比较并不公平：模块化系统还得到了 3 倍的状态和位置标签。把同样的标签和状态单独或一起交给 BC，也远未达到 10 对全对。"
---

{% include tldr.html %}

<div class="lang-zh pnote" lang="zh-Hans" markdown="1">

正文为英文，图表与代码与语言无关。

</div>

<div class="lang-enzh" markdown="1">

Parts 4 and 5 kept one network responsible for everything: reading the image, reading the
words, and flying. The words kept losing to the image. This part tests a different division of
labour, stated as a design rule:

> **The sentence should only choose the target. The image and the state should do the flying.**

So the words get exactly one job and no path to the motors. A world model learns where every
target is without ever seeing a sentence. A tiny selector turns the sentence into a colour. A
planner, which is not learned, flies to the chosen target.

<div class="vgrid">
  <figure class="pvid">
    <img src="/images/blog/drone-policy/06/pair0_plan_integrator.gif" alt="World model plus planner with the integrator on validation pair 0: each sentence leads to its own pillar and both flights succeed" loading="lazy">
    <figcaption><span class="pvid__label">World model + planner (A)</span>Validation pair 0, the first pair (not selected). Each sentence reaches its own target.</figcaption>
  </figure>
  <figure class="pvid">
    <img src="/images/blog/drone-policy/06/pair0_plan_wm.gif" alt="The same system planning with learned dynamics on validation pair 0: the drone jitters near the start, one flight times out and the other collides" loading="lazy">
    <figcaption><span class="pvid__label">Same, learned dynamics (B)</span>Same pair, same perception. Planning through the learned dynamics: one timeout, one collision.</figcaption>
  </figure>
</div>

## 1. The system: perceive, select, plan

<figure class="pfig">
  <img src="/images/blog/drone-policy/06/01_system.png" alt="System diagram. 1: a world-model encoder reads the camera and state, without words, into a 64-number latent and outputs the offset from the drone to each of four colours' hover points. 2: a goal selector maps the sentence to one colour. The named slot is picked. 3: a filter smooths the goal offset. 4: a cross-entropy-method planner scores 64 command sequences of 8 steps with either an integrator (A) or the learned latent dynamics (B). 5: a Stop rule identical to the expert's" loading="lazy">
  <figcaption><b>Perceive every target, let the words pick one, then plan to it.</b> Blue parts are learned; gray parts are hand-written rules. The words enter only at step 2. Variant (A) predicts the effect of a command by integration; variant (B) rolls the world model's learned dynamics forward. Everything else is shared.</figcaption>
</figure>

1. Perceive (learned, no words). An encoder of the same kind as the BC policy's turns the
   image and state into 64 numbers, z. A small head reads out, for each of the four colours,
   the offset from the drone to that colour's hover point. The head was trained against the
   simulator's true positions; those positions are training labels only, never inputs.
2. Select (learned, 340 parameters). Word embeddings, averaged, then one linear layer to
   four colours. It is 100% correct on the train and validation sentences. In this task one
   colour word decides the target, so that is expected, not a result.
3. Filter (rule). Single-frame estimates are noisy (about 0.17 m near the goal), and acting
   on them raw makes the commands jitter. The filter predicts the offset forward by the last
   command and blends in each new measurement at 30%.
4. Plan (rule). The cross-entropy method (CEM): sample 64 sequences of 8 commands (1.6 s),
   score each by where it would leave the drone relative to the goal, keep the best 8, resample
   around them, four rounds. Fly the first command and plan again 0.2 s later.
5. Stop (rule). The expert's own rule: Stop when the estimated goal is within 0.12 m and the
   drone is slower than 0.08 m/s.

The planner is short
([full code](https://github.com/Ckck12/Drone_VLA_Simulation/blob/7b9acb9551c3fb2d7392d9beaf32c249b6396630/dronevla/planner.py#L146-L163)):

```python
def plan(self, g0, displacement):        # g0: filtered goal offset (2,), body frame
    mean, std = self.mean, np.full((8, 2), 0.3)
    for _ in range(4):
        A = cap(mean + std * rng.standard_normal((64, 8, 2)))   # 64 sequences of 8 commands
        off = g0 + displacement(A)                              # where the goal would be
        cost = (off ** 2).sum(-1).mean(1)                       # stay close to it
        cost += 0.5 * jerk(A) + 1.0 * (A[:, -1] ** 2).sum(-1)   # smooth, arrive slowly
        elite = A[np.argsort(cost)[:8]]
        mean, std = elite.mean(0), elite.std(0) + 1e-3
    return elite[0][0]                                          # fly step 1, re-plan next tick

integrator = lambda A: -np.cumsum(A, axis=1) * 0.2               # variant (A)
```

The only difference between the two variants is `displacement`. **(A)** assumes the drone moves
exactly as commanded. **(B)** starts from z, applies the world model's learned dynamics
z' = LN(z + f(z, a)) step by step, and reads the head after each step.

All planner settings were tuned with the true target positions on *training* scenes, before any
learned variant was run on validation.

## 2. What the world model learned

The world model has 574,515 parameters and was trained on CPU for 12 epochs (36 minutes). Its
training data was the 100 demonstration layouts plus exploration flights over the same
layouts: noisy expert, random walks, and overshoots past the goal. That is 30,448 rows, three
times the states the BC policies saw. The loss unrolls the dynamics 4 steps from each start
state with the recorded commands
([full code](https://github.com/Ckck12/Drone_VLA_Simulation/blob/7b9acb9551c3fb2d7392d9beaf32c249b6396630/dronevla/world_model.py#L175-L202)):

```python
z = model.encode(rgb[:, 0], state[:, 0])
loss = heads_loss(z, k=0)                                  # state + every colour's offset
for k in range(1, 5):
    z = model.step(z, command[:, k - 1])                   # z' = LN(z + f(z, a))
    target = model.encode(rgb[:, k], state[:, k]).detach() # what the encoder sees at t+k
    loss += 0.9 ** k * (((z - target) ** 2).mean() + heads_loss(z, k))
```

<figure class="pfig">
  <img src="/images/blog/drone-policy/06/02_perception_dynamics.png" alt="A: error of the estimated goal position on unseen layouts, by distance to the target; with 100 training scenes it is 0.15 to 0.26 m, roughly half of the 20-scene model, and below the 0.4 m goal radius in 68 to 92 percent of frames. B: error when predicting ahead from the same perceived position: the integrator stays near 0.30 m up to 2 s while the learned dynamics grows to 0.39 m on expert flights and 0.57 m on random walks. C: holding one command for 1 s, the learned model predicts forward and left moves well but backward 1.97 times too far and right only 0.58 times" loading="lazy">
  <figcaption>Validation layouts, medians. A: perception, for the 20-scene and 100-scene world models. B: prediction error over time, both starting from the same perceived goal position. C: the constant-command probe, 519 validation states; 1.0 means the predicted move equals the commanded one.</figcaption>
</figure>

- **A, perception is good enough to plan with.** On unseen layouts the estimated goal position
  is off by a median 0.15–0.26 m, under the 0.4 m goal radius in 68–92% of frames. Training on
  100 scenes instead of 20 roughly halved the error. The bottleneck was the number of distinct
  scenes, not the model.
- **B, the learned dynamics are worse than doing nothing clever.** Starting from the same
  perceived position, rolling the learned dynamics forward 2 s gives 0.39 m error on expert
  flights and 0.57 m on random walks. Simply integrating the commands stays near 0.30 m.
  <span class="key-line">In this simulator the low-level controller tracks commands closely, so
  an integrator is almost perfect dynamics.</span>
- **C, and the learned dynamics are biased by direction.** Hold one command for 1 s: the model
  predicts forward and left moves about right, but backward moves twice as far as commanded and
  right moves at 0.58 of the commanded distance. Most training commands point forward, toward
  the targets.

## 3. Results

<figure class="pfig">
  <img src="/images/blog/drone-policy/06/03_scoreboard.png" alt="Scoreboard. Each sentence led to its own target: expert 10, planner with true positions 10, world model plus integrator planner 10, world model with learned dynamics 3, BC Part 4 0, BC with the same position labels 0, BC tokens with chunk 4. Success out of 20: 20, 20, 15, 0, 3, 8, 8. Pair success out of 10: 10, 10, 6, 0, 0, 0, 3" loading="lazy">
  <figcaption>Validation, one training seed. * = privileged reference (true positions). "BC + the same position labels" is the Part 4 policy with an extra head trained on exactly the target-position labels the world model gets.</figcaption>
</figure>

**(A) World model + integrator planner.**
- <span class="key-line">All 10 pairs split the right way</span> (no end-to-end model splits more than 5 correctly;
  Fisher's exact test for 10/10 vs 5/10: p ≈ 0.03).
- Success is 15/20 and pair success **6/10**, 95% interval 0.31–0.83. Against the best
  end-to-end model's 3/10 (0.11–0.60) that difference is not yet beyond chance.
- It reaches the 0.4 m goal region in all 20 flights.
- The five misses all come after it has reached the region: two stops just outside it and
  three collisions. The same planner fed the *true* target position succeeds 20/20, so what
  is left is perception error close to the target.

**(B) The same system planning through the learned dynamics** scores 0/20: 13 timeouts and
5 collisions. The GIF above shows why. CEM searches for commands that look good *according to
the model*, and panel C showed the model exaggerates some directions. The planner keeps
choosing the commands whose effect the model gets most wrong, and the drone jitters. Two things
make this worse: the model was trained to unroll 4 steps and is asked for 8, and most of its
training commands point forward, while a planner tries every direction.

<figure class="pfig">
  <img src="/images/blog/drone-policy/06/04_paths.png" alt="Top views of validation pairs 0 to 2. With the integrator, the two sentences' paths split to their own targets in all three pairs; pairs 0 and 1 succeed, pair 2 stops just outside both goal circles. With learned dynamics, paths zigzag near the start and wander, with timeouts and collisions" loading="lazy">
  <figcaption>Validation pairs 0–2 (the first three, not selected). With the integrator, both sentences go to their own targets every time; in pair 2 both flights stop just outside the 0.4 m circle, which is where perception error shows. With the learned dynamics, the planner exploits the model's bias and the flights zigzag.</figcaption>
</figure>

## 4. Is this a fair comparison? No, so here is what it does and doesn't explain

The modular system differs from the BC policies in more than its structure:

| advantage | does it alone explain the result? |
|---|---|
| dense position labels (where every target is, from the simulator) | No. Part 4's policy with a head trained on exactly these labels still sends both sentences to the same target in 10 of 10 pairs. |
| 3x the states (exploration flights) | No. The chunk policy trained on the same exploration flights (Part 5) splits 5 of 10 pairs correctly and completes none. |
| both together | No. The chunk policy with the exploration flights *and* the position-label head also splits 5 of 10 pairs correctly and completes none (success 2/20). |
| an explicit colour selector and hand-written filter, planner and Stop | Not tested separately. This is the structure itself. |

<span class="key-line">Neither the labels nor the states, alone or together, gave an
end-to-end policy the modular system's 10 of 10 correctly split pairs.</span> What's left is the structure: the words can
only choose, and nothing downstream can ignore that choice. I can't yet separate how much comes
from the selector and how much from the rule-based Stop.

Two smaller differences:

- **Randomness.** The planner's random seed differs between the two flights of a pair; BC
  evaluation is deterministic.
- **Training budget.** The world model trained for 36 minutes, about 4–5 times a Part 4 BC
  run.

## What this part does not show

- One training seed and ten pairs. 6/10 has a 95% interval of 0.31–0.83.
- The selector's job is trivial here. Two pillars always differ in colour, so one word
  decides. Relational instructions ("the pillar left of the red one") would need a real
  grounding model.
- The integrator works because the simulator is kind. Its controller tracks commands
  almost perfectly. With wind, drag or a real airframe, the dynamics would have to be learned or
  identified, and section 3 shows that naively learned dynamics are a liability for a sampling
  planner.
- Validation only. It was used for screening; final numbers need a fresh test split.

## Next

Season 2 ends with a working answer to Part 4's question, for this two-pillar, one-colour-word
task: yes, the words can be made to decide, if they are only allowed to decide. Season 3 turns
to engineering. One evaluation contract will run the same policies in Python and C++, so every
later change (ONNX, INT8, a C++ runner) is measured against the same numbers.

## Appendix

<details>
<summary>A. Settings</summary>

<table>
<thead><tr><th>component</th><th>setting</th></tr></thead>
<tbody>
<tr><td>world model</td><td>4 strided convolutions + state MLP → z (64); dynamics MLP 256-256 with a residual and LayerNorm; heads for state (11) and colour offsets (4 × 2). 574,515 parameters.</td></tr>
<tr><td>world-model training</td><td>v0.2 demonstrations + 4 exploration flights per layout (30,448 rows); 4-step unroll, discount 0.9; Adam 10⁻³, batch 32, 12 epochs fixed in advance, last epoch kept; seed 0; 36 min on CPU.</td></tr>
<tr><td>goal selector</td><td>17-token vocabulary × 16-d embedding, mean, linear to 4 colours; 300 full-batch steps on the 200 training sentences; 100% train and val.</td></tr>
<tr><td>filter</td><td>predict by the last command, blend 0.3 of the new measurement.</td></tr>
<tr><td>planner (CEM)</td><td>horizon 8 (1.6 s), 64 samples, 8 elites, 4 iterations, initial std 0.3 m/s, speed cap 0.5 m/s; cost weights: smoothness 0.5, end speed 1.0.</td></tr>
<tr><td>Stop</td><td>|goal offset| &lt; 0.12 m and speed &lt; 0.08 m/s (the expert's values); then the environment's three-in-a-row and 1 s hold rules.</td></tr>
</tbody>
</table>
</details>

<details>
<summary>B. Outcomes (validation, 20 flights)</summary>

<table>
<thead><tr><th>system</th><th>success</th><th>stop elsewhere</th><th>collision</th><th>timeout</th><th>out of bounds</th><th>reached goal region</th></tr></thead>
<tbody>
<tr><td>planner, true target position</td><td>20</td><td>0</td><td>0</td><td>0</td><td>0</td><td>20</td></tr>
<tr><td>world model + integrator</td><td>15</td><td>2</td><td>3</td><td>0</td><td>0</td><td>20</td></tr>
<tr><td>world model + learned dynamics</td><td>0</td><td>1</td><td>5</td><td>13</td><td>1</td><td>8</td></tr>
</tbody>
</table>
</details>

<details>
<summary>C. Reproduce</summary>

<p>Commit <a href="https://github.com/Ckck12/Drone_VLA_Simulation/tree/7b9acb9551c3fb2d7392d9beaf32c249b6396630"><code>7b9acb9</code></a>, dataset v0.2 and exploration flights <code>explore_v0.2</code>.</p>

<pre><code>python -m dronevla.world_model train --data data/v0.2 --explore data/explore_v0.2 --out runs/wm_v1
python -m dronevla.world_model eval --run runs/wm_v1 --out reports/wm_v1_prediction.json
python scripts/wm_diagnostics.py --run runs/wm_v1 --out reports/wm_v1_diagnostics.json
python -m dronevla.planner train-selector --data data/v0.2 --out runs/goal_selector
python -m dronevla.evaluate --data data/v0.2 --split val --policy plan-oracle \
    --policy plan:runs/wm_v1:integrator --policy plan:runs/wm_v1:wm \
    --out reports/eval_planner_val.json
python scripts/figures/blog06.py
bash scripts/figures/render_blog06_videos.sh
</code></pre>
<p>Stage-by-stage notes: <code>docs/phase1_world_model.md</code>, <code>docs/season2_experiments.md</code>.</p>
</details>

### References: what I took from each

- [World Models (Ha and Schmidhuber, 2018)](https://arxiv.org/abs/1803.10122) and
  [PlaNet (Hafner et al., 2019)](https://arxiv.org/abs/1811.04551) learn a compact latent
  state and its dynamics from images. → The encoder, latent z and residual dynamics.
- [PETS (Chua et al., 2018)](https://arxiv.org/abs/1805.12114) plans with CEM through learned
  dynamics. → The planner. Its use of model ensembles to avoid exploiting model error is
  exactly what variant (B) lacks.
- [When to Trust Your Model (Janner et al., 2019)](https://arxiv.org/abs/1906.08253) shows how
  model error compounds over long rollouts. → Why the planner was given an integrator first,
  and why (B) fails at an 8-step horizon after 4-step training.
- [TD-MPC (Hansen et al., 2022)](https://arxiv.org/abs/2203.04955) plans in a latent space
  learned for control. → A direction for making (B) work, not tried here.
- [CLIPort (Shridhar et al., 2021)](https://arxiv.org/abs/2109.12098) separates *what*
  (language-conditioned semantics) from *where* (spatial precision). → The same split, in its
  simplest possible form.

## Summary (STAR)

<div class="star-summary" markdown="1">

<h3 class="star-h"><span class="star-tag">S</span>Situation</h3>

In Parts 4 and 5 one network read the image, read the words and flew, and the words kept
losing to the image. No end-to-end model split more than 5 of 10 validation pairs correctly, and the best completed 3.

<h3 class="star-h"><span class="star-tag">T</span>Task</h3>

Test a design rule in which the sentence only chooses the target and the image and state do
the flying. Check whether the modular system's extra advantages (position labels, 3x the
states) explain the result on their own.

<h3 class="star-h"><span class="star-tag">A</span>Action</h3>

I trained a 574,515-parameter world model without words on 30,448 rows (36 minutes on CPU) and
a 340-parameter colour selector, then added a filter, a CEM planner and the expert's Stop rule.
The planner predicted command effects either with an integrator (A) or with the learned
dynamics (B), and BC controls got the same position labels and exploration flights.

<h3 class="star-h"><span class="star-tag">R</span>Result</h3>

With the integrator all 10 pairs split the right way (p ≈ 0.03 against 5/10); success was
15/20 and pair success 6/10 (95% interval 0.31–0.83, one seed); every flight reached the goal
region. Planning through the learned dynamics scored 0/20. Neither the labels nor the states,
alone or together, gave an end-to-end policy the modular system's 10 of 10 correctly split pairs.

</div>

</div>

<div class="lang-ko" lang="ko" markdown="1">

4편과 5편에서는 네트워크 하나가 모든 일을 맡았습니다. 이미지를 읽고 말을 읽고 비행까지 했습니다.
그리고 말은 번번이 이미지에 밀렸습니다. 이번 편에서는 일을 다르게 나눠 봅니다. 설계 규칙으로
적으면 다음과 같습니다.

> **문장은 타깃을 고르기만 해야 합니다. 비행은 이미지와 상태가 맡아야 합니다.**

그래서 말에는 딱 한 가지 일만 주고 모터로 가는 경로는 주지 않습니다. 월드 모델은 문장을 한 번도
보지 않은 채 모든 타깃의 위치를 배웁니다. 아주 작은 선택기가 문장을 색깔로 바꿉니다. 고른 타깃까지
비행하는 일은 학습하지 않은 플래너가 맡습니다.

<div class="vgrid">
  <figure class="pvid">
    <img src="/images/blog/drone-policy/06/pair0_plan_integrator.gif" alt="검증 쌍 0에서 적분기를 쓴 월드 모델과 플래너: 두 문장이 각자의 기둥으로 가고 두 비행 모두 성공" loading="lazy">
    <figcaption><span class="pvid__label">월드 모델 + 플래너 (A)</span>검증 쌍(pair) 0, 첫 번째 쌍입니다(골라낸 것이 아님). 문장마다 자기 타깃에 도달합니다.</figcaption>
  </figure>
  <figure class="pvid">
    <img src="/images/blog/drone-policy/06/pair0_plan_wm.gif" alt="같은 시스템이 검증 쌍 0에서 학습된 동역학으로 계획하는 모습: 드론이 출발점 근처에서 떨리며 한 비행은 시간 초과, 다른 비행은 충돌" loading="lazy">
    <figcaption><span class="pvid__label">같은 시스템, 학습된 동역학 (B)</span>같은 쌍, 같은 인지입니다. 학습된 동역학으로 계획하면 한 번은 시간 초과, 한 번은 충돌로 끝납니다.</figcaption>
  </figure>
</div>

## 1. 시스템: 인지, 선택, 계획

<figure class="pfig">
  <img src="/images/blog/drone-policy/06/01_system.png" alt="시스템 다이어그램. 1: 월드 모델 인코더가 말 없이 카메라와 상태를 읽어 64개 숫자의 잠재 벡터로 만든 뒤 드론에서 네 색깔 각각의 호버 지점까지의 오프셋을 출력. 2: 목표 선택기가 문장을 색깔 하나로 바꾸며 이름이 불린 슬롯이 선택됨. 3: 필터가 목표 오프셋을 부드럽게 다듬음. 4: 교차 엔트로피 방법 플래너가 8스텝짜리 명령 시퀀스 64개를 적분기(A) 또는 학습된 잠재 동역학(B)으로 평가. 5: 전문가와 똑같은 Stop 규칙" loading="lazy">
  <figcaption><b>타깃을 모두 인지하고 말로 하나를 고른 다음 그곳까지 계획합니다.</b> 파란 부분은 학습한 것이고 회색 부분은 손으로 짠 규칙입니다. 말은 2단계에서만 들어옵니다. 변형 (A)는 명령의 효과를 적분으로 예측하고 변형 (B)는 월드 모델의 학습된 동역학을 순차적으로 적용해 미래 상태를 예측합니다. 나머지는 모두 같습니다.</figcaption>
</figure>

1. 인지 (학습, 말 없음). 행동 복제(BC) 정책과 같은 종류의 인코더가 이미지와 상태를 숫자
   64개, 곧 z로 바꿉니다. 작은 헤드가 네 색깔마다 드론에서 그 색깔의 호버 지점까지의 오프셋을
   읽어 냅니다. 헤드는 시뮬레이터가 아는 실제 위치를 정답으로 삼아 학습했습니다. 이 위치는 학습
   라벨로만 쓰고 입력으로는 한 번도 넣지 않습니다.
2. 선택 (학습, 340 파라미터). 단어 임베딩을 평균한 뒤 선형 층 하나로 네 색깔에 대응시킵니다.
   학습 문장과 검증 문장에서 100% 맞힙니다. 이 과제에서는 색깔 단어 하나가 타깃을 정하므로 당연한
   일이고 결과로 볼 수는 없습니다.
3. 필터 (규칙). 프레임 하나로 낸 추정은 잡음이 큽니다(목표 근처에서 약 0.17 m). 이 값을
   그대로 쓰면 명령이 떨립니다. 필터는 마지막 명령만큼 오프셋을 앞으로 예측한 다음 새 측정값을 30%
   비율로 섞습니다.
4. 계획 (규칙). 교차 엔트로피 방법(cross-entropy method, CEM)을 씁니다. 명령 8개(1.6 s)로
   된 시퀀스를 64개 샘플링하고 각 시퀀스가 끝났을 때 드론이 목표 기준으로 어디에 있을지로 점수를
   매깁니다. 가장 좋은 8개를 남기고 그 주변에서 다시 샘플링하는 과정을 네 번 반복합니다. 첫 번째
   명령으로 비행하고 0.2 s 뒤에 다시 계획합니다.
5. Stop (규칙). 전문가가 쓰는 규칙 그대로입니다. 추정한 목표가 0.12 m 안에 있고 드론이
   0.08 m/s보다 느리면 Stop합니다.

플래너 코드는 짧습니다
([전체 코드](https://github.com/Ckck12/Drone_VLA_Simulation/blob/7b9acb9551c3fb2d7392d9beaf32c249b6396630/dronevla/planner.py#L146-L163)):

```python
def plan(self, g0, displacement):        # g0: filtered goal offset (2,), body frame
    mean, std = self.mean, np.full((8, 2), 0.3)
    for _ in range(4):
        A = cap(mean + std * rng.standard_normal((64, 8, 2)))   # 64 sequences of 8 commands
        off = g0 + displacement(A)                              # where the goal would be
        cost = (off ** 2).sum(-1).mean(1)                       # stay close to it
        cost += 0.5 * jerk(A) + 1.0 * (A[:, -1] ** 2).sum(-1)   # smooth, arrive slowly
        elite = A[np.argsort(cost)[:8]]
        mean, std = elite.mean(0), elite.std(0) + 1e-3
    return elite[0][0]                                          # fly step 1, re-plan next tick

integrator = lambda A: -np.cumsum(A, axis=1) * 0.2               # variant (A)
```

두 변형은 `displacement` 하나만 다릅니다. **(A)**는 드론이 명령받은 그대로 움직인다고 가정합니다.
**(B)**는 z에서 출발해 월드 모델의 학습된 동역학 z' = LN(z + f(z, a))를 한 스텝씩 적용하고 스텝마다
헤드를 읽습니다.

플래너 설정은 모두 *학습* 장면에서 실제 타깃 위치를 써서 맞췄습니다. 학습된 변형을 검증에서 돌리기
전에 끝낸 작업입니다.

## 2. 월드 모델이 배운 것

월드 모델은 파라미터가 574,515개이고 CPU에서 12 에폭(36분) 동안 학습했습니다. 학습 데이터는 시연
레이아웃 100개에 같은 레이아웃 위를 나는 탐색 비행을 더한 것입니다. 탐색 비행은 잡음을 섞은 전문가,
무작위 보행(random walk), 목표를 지나쳐 가는 비행으로 구성됩니다. 모두 30,448행이고 BC 정책들이 본
상태의 세 배입니다. 손실은 각 시작 상태에서 기록된 명령으로 동역학을 4스텝 펼쳐서 계산합니다
([전체 코드](https://github.com/Ckck12/Drone_VLA_Simulation/blob/7b9acb9551c3fb2d7392d9beaf32c249b6396630/dronevla/world_model.py#L175-L202)):

```python
z = model.encode(rgb[:, 0], state[:, 0])
loss = heads_loss(z, k=0)                                  # state + every colour's offset
for k in range(1, 5):
    z = model.step(z, command[:, k - 1])                   # z' = LN(z + f(z, a))
    target = model.encode(rgb[:, k], state[:, k]).detach() # what the encoder sees at t+k
    loss += 0.9 ** k * (((z - target) ** 2).mean() + heads_loss(z, k))
```

<figure class="pfig">
  <img src="/images/blog/drone-policy/06/02_perception_dynamics.png" alt="A: 처음 보는 레이아웃에서 추정한 목표 위치의 오차를 타깃까지의 거리별로 나타냄. 학습 장면 100개로는 0.15~0.26 m로 장면 20개 모델의 약 절반이며 프레임의 68~92%에서 목표 반경 0.4 m보다 작음. B: 같은 인지 위치에서 앞을 예측할 때의 오차. 적분기는 2 s까지 0.30 m 근처에 머물지만 학습된 동역학은 전문가 비행에서 0.39 m, 무작위 보행에서 0.57 m까지 커짐. C: 명령 하나를 1 s 동안 유지하면 학습된 모델은 앞쪽과 왼쪽 이동은 잘 예측하지만 뒤쪽은 1.97배 너무 멀리, 오른쪽은 0.58배만 예측함" loading="lazy">
  <figcaption>검증 레이아웃, 중앙값. A: 장면 20개와 100개로 학습한 월드 모델의 인지 오차. B: 같은 인지 목표 위치에서 출발했을 때 시간에 따른 예측 오차. C: 일정 명령 탐침(probe), 검증 상태 519개. 1.0이면 예측 이동이 명령한 이동과 같습니다.</figcaption>
</figure>

- **A, 인지는 계획에 쓸 만큼 정확합니다.** 처음 보는 레이아웃에서 추정한 목표 위치는 중앙값으로
  0.15–0.26 m 어긋납니다. 프레임의 68–92%에서는 목표 반경 0.4 m 안에 듭니다. 학습 장면을 20개에서
  100개로 늘리자 오차가 대략 절반으로 줄었습니다. 병목은 모델이 아니라 서로 다른 장면의 수였습니다.
- **B, 학습된 동역학은 아무 요령 없는 방법보다도 못합니다.** 같은 인지 위치에서 학습된 동역학을
  순차적으로 적용해 2 s 뒤의 상태를 예측하면 오차가 전문가 비행에서 0.39 m, 무작위 보행에서 0.57 m입니다. 명령을 그냥
  적분하면 0.30 m 근처에 머뭅니다. <span class="key-line">이 시뮬레이터에서는 저수준 제어기가 명령을
  바짝 따라가므로 적분기가 거의 완벽한 동역학입니다.</span>
- **C, 게다가 학습된 동역학은 방향에 따라 치우쳐 있습니다.** 명령 하나를 1 s 유지해 보면 모델은
  앞쪽과 왼쪽 이동은 대체로 맞게 예측합니다. 반면 뒤쪽 이동은 명령한 거리의 두 배로, 오른쪽 이동은
  명령한 거리의 0.58로 예측합니다. 학습에 쓴 명령은 대부분 타깃이 있는 앞쪽을 향합니다.

## 3. 결과

<figure class="pfig">
  <img src="/images/blog/drone-policy/06/03_scoreboard.png" alt="점수표. 문장마다 자기 타깃으로 간 쌍의 수: 전문가 10, 실제 위치를 쓴 플래너 10, 월드 모델과 적분기 플래너 10, 학습된 동역학을 쓴 월드 모델 3, 4편 BC 0, 같은 위치 라벨을 더한 BC 0, 청크를 쓴 BC 토큰 4. 20회 중 성공: 20, 20, 15, 0, 3, 8, 8. 10쌍 중 쌍 성공: 10, 10, 6, 0, 0, 0, 3" loading="lazy">
  <figcaption>검증, 학습 시드 1개. * = 실제 위치를 입력받는 비교 기준. "BC + 같은 위치 라벨"은 4편 정책에 헤드를 하나 더 붙여 월드 모델이 받는 것과 똑같은 타깃 위치 라벨로 학습한 것입니다.</figcaption>
</figure>

**(A) 월드 모델 + 적분기 플래너.**
- <span class="key-line">10쌍 모두 맞는 방향으로 갈라졌습니다</span>(end-to-end 모델은 많아야
  5쌍. 10/10 대 5/10의 피셔 정확 검정(Fisher's exact test): p ≈ 0.03).
- 성공은 15/20, 쌍 성공(pair success)은 **6/10**이고 95% 구간은 0.31–0.83입니다. 가장 나은
  end-to-end 모델의 3/10(0.11–0.60)과 견주면 이 차이는 아직 우연의 범위를 벗어나지 못합니다.
- 20번의 비행 모두 0.4 m 목표 영역에 도달합니다.
- 실패 다섯 번은 모두 목표 영역에 도달한 뒤에 나왔습니다. 두 번은 영역 바로 바깥에서 멈췄고 세 번은
  충돌했습니다. 같은 플래너에 *실제* 타깃 위치를 주면 20/20 성공하므로 남은 원인은 타깃 근처의 인지
  오차입니다.

**(B) 같은 시스템을 학습된 동역학으로 계획하게 하면** 0/20입니다. 시간 초과가 13번, 충돌이 5번입니다.
이유는 위의 GIF에 보입니다. CEM은 *모델 기준으로* 좋아 보이는 명령을 찾습니다. 그런데 패널 C에서
봤듯이 모델은 몇몇 방향을 과장합니다. 플래너는 모델이 효과를 가장 크게 틀리는 명령을 계속 고르고
드론은 떨립니다. 사정을 더 나쁘게 만드는 요인도 두 가지 있습니다. 모델은 4스텝을 펼치도록 학습했는데
8스텝을 요구받습니다. 또 학습 명령은 대부분 앞쪽을 향하는 반면 플래너는 모든 방향을 시험합니다.

<figure class="pfig">
  <img src="/images/blog/drone-policy/06/04_paths.png" alt="검증 쌍 0~2를 위에서 본 경로. 적분기를 쓰면 세 쌍 모두 두 문장의 경로가 각자의 타깃으로 갈라짐. 쌍 0과 1은 성공하고 쌍 2는 두 목표 원 바로 바깥에서 멈춤. 학습된 동역학을 쓰면 경로가 출발점 근처에서 지그재그로 움직이다 헤매며 시간 초과와 충돌이 생김" loading="lazy">
  <figcaption>검증 쌍 0–2(처음 세 쌍, 골라낸 것이 아님). 적분기를 쓰면 두 문장이 매번 각자의 타깃으로 갑니다. 쌍 2에서는 두 비행 모두 0.4 m 원 바로 바깥에서 멈추는데 인지 오차가 드러나는 곳이 바로 여기입니다. 학습된 동역학을 쓰면 플래너가 모델의 편향을 파고들어 비행이 지그재그가 됩니다.</figcaption>
</figure>

## 4. 공정한 비교입니까? 아닙니다. 그래서 무엇이 설명되고 무엇이 설명되지 않는지 적습니다

모듈형 시스템은 구조 말고도 BC 정책과 다른 점이 더 있습니다.

| 이점 | 이것만으로 결과가 설명됩니까? |
|---|---|
| 촘촘한 위치 라벨(시뮬레이터가 알려 주는 모든 타깃의 위치) | 아닙니다. 4편 정책에 바로 이 라벨로 학습한 헤드를 붙여도 10쌍 중 10쌍에서 두 문장을 같은 타깃으로 보냅니다. |
| 3배의 상태(탐색 비행) | 아닙니다. 같은 탐색 비행으로 학습한 청크 정책(5편)은 10쌍 중 5쌍을 맞게 가르지만 완료한 쌍은 하나도 없습니다. |
| 둘 다 | 아닙니다. 탐색 비행*과* 위치 라벨 헤드를 모두 쓴 청크 정책도 10쌍 중 5쌍을 맞게 가르고 완료한 쌍은 없습니다(성공 2/20). |
| 명시적인 색깔 선택기, 손으로 짠 필터·플래너·Stop | 따로 시험하지 않았습니다. 이것이 구조 자체입니다. |

<span class="key-line">위치 라벨과 상태를 따로 주든 함께 주든 end-to-end 정책은 모듈형 시스템처럼
10쌍을 모두 맞게 가르지 못했습니다.</span> 그러면 구조가 남습니다. 말은 고르기만 할 수 있고 그 뒤에 있는 어떤 부분도
그 선택을 무시할 수 없습니다. 이 효과 가운데 얼마가 선택기 덕분이고 얼마가 규칙 기반 Stop 덕분인지는
아직 나누지 못했습니다.

작은 차이가 두 가지 더 있습니다.

- **무작위성.** 한 쌍의 두 비행에서 플래너의 랜덤 시드가 다릅니다. BC 평가는 결정론적입니다.
- **학습 예산.** 월드 모델은 36분 동안 학습했습니다. 4편 BC 학습 한 번의 4–5배쯤 됩니다.

## 이번 편이 보여 주지 못한 것

- 학습 시드 1개, 쌍 10개. 6/10의 95% 구간은 0.31–0.83입니다.
- 여기서 선택기가 하는 일은 아주 쉽습니다. 두 기둥은 항상 색깔이 다르므로 단어 하나로 정해집니다.
  "빨간 기둥 왼쪽에 있는 기둥" 같은 관계형 지시문이라면 제대로 된 그라운딩 모델이 필요합니다.
- 적분기가 통하는 것은 시뮬레이터가 친절하기 때문입니다. 이 시뮬레이터의 제어기는 명령을 거의
  완벽하게 따라갑니다. 바람이나 항력이 있거나 실제 기체를 쓴다면 동역학을 학습하거나 식별해야 합니다.
  3절에서 봤듯이 단순한 방식으로 학습한 동역학은 샘플링 플래너에게 오히려 짐이 됩니다.
- 검증 분할만 썼습니다. 검증 분할은 선별에 썼으므로 최종 수치를 내려면 새 테스트 분할이
  필요합니다.

## 다음 편

기둥 두 개와 색깔 단어 하나로 된 이 과제에 한해서는 시즌 2가 4편이 던진 질문에 작동하는 답을 얻고
끝납니다. 답은 "그렇다"입니다. 말에게 결정하는 일만 허락하면 말이 결정하게 만들 수 있습니다.
시즌 3은 엔지니어링으로 넘어갑니다. 같은 정책을 Python과 C++에서 돌리는 평가 계약(evaluation
contract)을 하나 두고 이후의 모든 변경(ONNX, INT8, C++ 러너)을 같은 수치에 대어 측정합니다.

## 부록

<details>
<summary>A. 설정</summary>

<table>
<thead><tr><th>구성 요소</th><th>설정</th></tr></thead>
<tbody>
<tr><td>월드 모델</td><td>스트라이드 합성곱 4개 + 상태 MLP → z (64); 잔차와 LayerNorm을 쓴 동역학 MLP 256-256; 상태(11)와 색깔 오프셋(4 × 2) 헤드. 파라미터 574,515개.</td></tr>
<tr><td>월드 모델 학습</td><td>v0.2 시연 + 레이아웃마다 탐색 비행 4개(30,448행); 4스텝 펼침, 할인율 0.9; Adam 10⁻³, 배치 32, 미리 정한 12 에폭, 마지막 에폭 사용; 시드 0; CPU에서 36분.</td></tr>
<tr><td>목표 선택기</td><td>어휘 17토큰 × 16차원 임베딩, 평균, 4색으로 가는 선형 층; 학습 문장 200개로 전체 배치 300스텝; 학습·검증 100%.</td></tr>
<tr><td>필터</td><td>마지막 명령으로 예측하고 새 측정값을 0.3 비율로 섞음.</td></tr>
<tr><td>플래너 (CEM)</td><td>호라이즌 8 (1.6 s), 샘플 64개, 엘리트 8개, 반복 4회, 초기 표준편차 0.3 m/s, 속도 상한 0.5 m/s; 비용 가중치: 부드러움 0.5, 종료 속도 1.0.</td></tr>
<tr><td>Stop</td><td>|목표 오프셋| &lt; 0.12 m 이고 속도 &lt; 0.08 m/s (전문가와 같은 값); 그다음에는 환경의 3회 연속 규칙과 1 s 유지 규칙.</td></tr>
</tbody>
</table>
</details>

<details>
<summary>B. 결과 (검증, 비행 20회)</summary>

<table>
<thead><tr><th>시스템</th><th>성공</th><th>다른 곳에서 정지</th><th>충돌</th><th>시간 초과</th><th>범위 이탈</th><th>목표 영역 도달</th></tr></thead>
<tbody>
<tr><td>플래너, 실제 타깃 위치</td><td>20</td><td>0</td><td>0</td><td>0</td><td>0</td><td>20</td></tr>
<tr><td>월드 모델 + 적분기</td><td>15</td><td>2</td><td>3</td><td>0</td><td>0</td><td>20</td></tr>
<tr><td>월드 모델 + 학습된 동역학</td><td>0</td><td>1</td><td>5</td><td>13</td><td>1</td><td>8</td></tr>
</tbody>
</table>
</details>

<details>
<summary>C. 재현</summary>

<p>커밋 <a href="https://github.com/Ckck12/Drone_VLA_Simulation/tree/7b9acb9551c3fb2d7392d9beaf32c249b6396630"><code>7b9acb9</code></a>, 데이터셋 v0.2, 탐색 비행 <code>explore_v0.2</code>.</p>

<pre><code>python -m dronevla.world_model train --data data/v0.2 --explore data/explore_v0.2 --out runs/wm_v1
python -m dronevla.world_model eval --run runs/wm_v1 --out reports/wm_v1_prediction.json
python scripts/wm_diagnostics.py --run runs/wm_v1 --out reports/wm_v1_diagnostics.json
python -m dronevla.planner train-selector --data data/v0.2 --out runs/goal_selector
python -m dronevla.evaluate --data data/v0.2 --split val --policy plan-oracle \
    --policy plan:runs/wm_v1:integrator --policy plan:runs/wm_v1:wm \
    --out reports/eval_planner_val.json
python scripts/figures/blog06.py
bash scripts/figures/render_blog06_videos.sh
</code></pre>
<p>단계별 기록: <code>docs/phase1_world_model.md</code>, <code>docs/season2_experiments.md</code>.</p>
</details>

### 참고 문헌: 각각에서 가져온 것

- [World Models (Ha and Schmidhuber, 2018)](https://arxiv.org/abs/1803.10122)와
  [PlaNet (Hafner et al., 2019)](https://arxiv.org/abs/1811.04551)은 이미지에서 압축된 잠재 상태와
  그 동역학을 학습합니다. → 인코더, 잠재 z, 잔차 동역학.
- [PETS (Chua et al., 2018)](https://arxiv.org/abs/1805.12114)는 학습된 동역학을 거쳐 CEM으로
  계획합니다. → 플래너. PETS는 모델 앙상블을 써서 플래너가 모델 오차를 파고들지 못하게 하는데 변형
  (B)에 없는 것이 바로 이것입니다.
- [When to Trust Your Model (Janner et al., 2019)](https://arxiv.org/abs/1906.08253)은 긴 롤아웃에서
  모델 오차가 어떻게 쌓이는지 보입니다. → 플래너에 적분기를 먼저 준 이유이자 4스텝으로 학습한 (B)가
  8스텝 호라이즌에서 실패하는 이유.
- [TD-MPC (Hansen et al., 2022)](https://arxiv.org/abs/2203.04955)는 제어용으로 학습한 잠재 공간에서
  계획합니다. → (B)를 살릴 수 있는 방향이지만 여기서는 시도하지 않았습니다.
- [CLIPort (Shridhar et al., 2021)](https://arxiv.org/abs/2109.12098)는 *무엇*(언어 조건 의미)과
  *어디*(공간 정밀도)를 분리합니다. → 같은 분리를 가장 단순한 형태로 적용했습니다.

## 요약 (STAR)

<div class="star-summary" markdown="1">

<h3 class="star-h"><span class="star-tag">S</span>상황</h3>

4편과 5편에서는 네트워크 하나가 이미지와 말을 읽고 비행까지 맡았는데 말이 번번이 이미지에 밀렸습니다.
end-to-end 모델은 검증 10쌍 중 많아야 5쌍을 맞게 갈랐고 가장 나은 모델도 3쌍만 완료했습니다.

<h3 class="star-h"><span class="star-tag">T</span>과제</h3>

문장은 타깃만 고르고 비행은 이미지와 상태가 맡는다는 설계 규칙을 시험합니다. 모듈형 시스템이 덤으로
받은 위치 라벨과 3배의 상태가 그것만으로 결과를 설명하는지도 확인합니다.

<h3 class="star-h"><span class="star-tag">A</span>행동</h3>

말을 읽지 않는 574,515 파라미터 월드 모델을 30,448행으로 학습하고(CPU에서 36분) 340 파라미터 색깔
선택기를 만든 뒤 필터, CEM 플래너, 전문가의 Stop 규칙을 붙였습니다. 플래너는 명령의 효과를
적분기(A)나 학습된 동역학(B)으로 예측했습니다. 대조군 BC에는 같은 위치 라벨과 탐색 비행을
주었습니다.

<h3 class="star-h"><span class="star-tag">R</span>결과</h3>

적분기를 쓰면 10쌍 모두 맞는 방향으로 갈라졌고(5/10 대비 p ≈ 0.03) 성공 15/20, 쌍 성공 6/10(95%
구간 0.31–0.83, 시드 1개)이었으며 20번 모두 목표 영역에 도달했습니다. 학습된 동역학으로 계획하면
0/20이었습니다. 위치 라벨과 상태를 따로 주든 함께 주든 end-to-end 정책은 모듈형 시스템처럼 10쌍을
모두 맞게 가르지 못했습니다.

</div>

</div>
