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
excerpt_ko: "연재 6편. 하나의 네트워크가 말을 듣게 되길 바라는 대신 일을 나눴습니다: 월드 모델은 말을 읽지 않고 모든 타깃의 위치를 찾고, 340 파라미터짜리 선택기가 말로 그중 하나를 고르고, 플래너가 그곳으로 납니다. 모든 pair가 맞는 방향으로 갈라지고 pair 성공 6/10. 학습된 dynamics로 계획하면 완전히 실패합니다."
excerpt_zh: "系列第 6 篇：与其指望一个网络学会听指令，不如把任务拆开：世界模型在不读指令的情况下找到所有目标，一个 340 参数的选择器让指令挑出其中一个，规划器飞过去。每一对都飞向了正确方向，pair 成功 6/10；而用学到的动力学做规划则完全失败。"
tldr_en:
  - "The words are only allowed to <b>choose</b>: a world model (575k parameters, no words) estimates where every colour's target is, a 340-parameter selector maps the sentence to a colour, and a sampling planner (CEM) plus a Stop rule fly there."
  - "On the same 10 validation pairs, <b>every pair splits to the right targets</b> (best end-to-end model: 4). Success 15/20, pair success <b>6/10</b> (95% interval 0.31–0.83, one seed). It reaches the goal region in all 20 flights; the misses are perception error near the goal."
  - "Planning through the world model's own <b>learned dynamics</b> scores 0/20. The planner finds commands whose predicted effect is wrong. Assuming the drone does what it is told (an integrator) works far better. And this comparison with BC is not fair: the modular system also got 3x the states and position labels. The post shows which of those alone don't explain it."
tldr_ko:
  - "말에게는 <b>고르는 일</b>만 맡깁니다: 월드 모델(57.5만 파라미터, 말 입력 없음)이 각 색깔 타깃의 위치를 추정하고, 340 파라미터 선택기가 문장을 색깔로 바꾸고, 샘플링 플래너(CEM)와 Stop 규칙이 그곳으로 납니다."
  - "같은 검증 10 pair에서 <b>모든 pair가 맞는 타깃으로 갈라졌습니다</b>(가장 나은 end-to-end 모델: 4개). 성공 15/20, pair 성공 <b>6/10</b>(95% 구간 0.31–0.83, seed 1개). 20번 모두 목표 영역에 도달했고, 실패는 목표 근처의 인지 오차 때문입니다."
  - "월드 모델이 학습한 <b>dynamics</b>로 계획하면 0/20입니다. 플래너가 예측 효과가 틀린 명령을 찾아냅니다. '시킨 대로 움직인다'고 가정하는 적분기가 훨씬 낫습니다. 그리고 BC와의 비교는 공정하지 않습니다: 모듈형 시스템은 3배의 상태와 위치 레이블도 받았습니다. 그중 어느 것만으로는 설명되지 않는지를 보입니다."
tldr_zh:
  - "只让指令负责<b>选择</b>：世界模型（57.5 万参数，不读指令）估计每种颜色目标的位置，一个 340 参数的选择器把句子映射成颜色，采样规划器（CEM）加 Stop 规则飞过去。"
  - "在同样的 10 个验证对上，<b>每一对都飞向了正确的目标</b>（最好的端到端模型：4 对）。成功 15/20，pair 成功 <b>6/10</b>（95% 区间 0.31–0.83，单个种子）。20 次全部到达目标区域，失败来自目标附近的感知误差。"
  - "用世界模型自己<b>学到的动力学</b>做规划得分 0/20：规划器会找到预测效果错误的指令。假设无人机按指令运动（积分器）要好得多。而且与 BC 的比较并不公平：模块化系统还得到了 3 倍的状态和位置标签。文中说明了其中哪些单独无法解释这一结果。"
---

{% include tldr.html %}

<div class="lang-ko pnote" lang="ko" markdown="1">
본문은 영어로 작성되어 있습니다. 그림과 코드는 언어와 상관없이 같습니다.
</div>
<div class="lang-zh pnote" lang="zh-Hans" markdown="1">
正文为英文，图表与代码与语言无关。
</div>

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

1. **Perceive (learned, no words).** An encoder of the same kind as the BC policy's turns the
   image and state into 64 numbers, z. A small head reads out, for each of the four colours,
   the offset from the drone to that colour's hover point. The head was trained against the
   simulator's true positions; those positions are training labels only, never inputs.
2. **Select (learned, 340 parameters).** Word embeddings, averaged, then one linear layer to
   four colours. It is 100% correct on the train and validation sentences. In this task one
   colour word decides the target, so that is expected, not a result.
3. **Filter (rule).** Single-frame estimates are noisy (about 0.17 m near the goal), and acting
   on them raw makes the commands jitter. The filter predicts the offset forward by the last
   command and blends in each new measurement at 30%.
4. **Plan (rule).** The cross-entropy method (CEM): sample 64 sequences of 8 commands (1.6 s),
   score each by where it would leave the drone relative to the goal, keep the best 8, resample
   around them, four rounds. Fly the first command and plan again 0.2 s later.
5. **Stop (rule).** The expert's own rule: Stop when the estimated goal is within 0.12 m and the
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
    return best_sequence[0]                                     # fly step 1, re-plan next tick

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
  flights and 0.57 m on random walks. Simply integrating the commands stays near 0.30 m. In
  this simulator the low-level controller tracks commands closely, so an integrator is almost
  perfect dynamics.
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
- **All 10 pairs split the right way** (the best end-to-end model: 4; Fisher's exact test
  for 10/10 vs 4/10: p ≈ 0.01).
- Success is 15/20 and pair success **6/10**, 95% interval 0.31–0.83. Against the best
  end-to-end model's 3/10 (0.11–0.60) that difference is not yet beyond chance.
- It reaches the 0.4 m goal region in all 20 flights.
- The five misses all come after it has reached the region: two stops just outside it and
  three collisions. The same planner fed the *true* target position succeeds 20/20, so what
  is left is perception error close to the target.

**(B) The same system planning through the learned dynamics** scores **0/20**: 13 timeouts and
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
| **dense position labels** (where every target is, from the simulator) | No. Part 4's policy with a head trained on exactly these labels still sends both sentences to the same target in 10 of 10 pairs. |
| **3x the states** (exploration flights) | No. The chunk policy trained on the same exploration flights (Part 5) splits 5 of 10 pairs and completes none. |
| **both together** | No. The chunk policy with the exploration flights *and* the position-label head also splits 5 of 10 pairs and completes none (success 2/20). |
| **an explicit colour selector** and **hand-written filter, planner and Stop** | Not tested separately. This is the structure itself. |

Neither the labels nor the states, alone or together, turned an end-to-end policy into one that
listens. What's left
is the structure: the words can only choose, and nothing downstream can ignore that choice.
I can't yet separate how much comes from the selector and how much from the rule-based Stop.

Two smaller differences:

- **Randomness.** The planner's random seed differs between the two flights of a pair; BC
  evaluation is deterministic.
- **Training budget.** The world model trained for 36 minutes, about 4–5 times a Part 4 BC
  run.

## What this part does not show

- **One training seed and ten pairs.** 6/10 has a 95% interval of 0.31–0.83.
- **The selector's job is trivial here.** Two pillars always differ in colour, so one word
  decides. Relational instructions ("the pillar left of the red one") would need a real
  grounding model.
- **The integrator works because the simulator is kind.** Its controller tracks commands
  almost perfectly. With wind, drag or a real airframe, the dynamics would have to be learned or
  identified, and section 3 shows that naively learned dynamics are a liability for a sampling
  planner.
- **Validation only.** It was used for screening; final numbers need a fresh test split.

## Next

Season 2 ends with a working answer to Part 4's question, for this two-pillar, one-colour-word
task: yes, the words can be made to decide, if they are only allowed to decide. Season 3 turns to engineering. One evaluation
contract will run the same policies in Python and C++, so every later change (ONNX, INT8, a
C++ runner) is measured against the same numbers.

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
