---
title: "Part 5 · Borrowing from VLAs: action tokens and action chunks"
title_ko: "5편 · VLA에서 빌려 온 것: 액션 토큰과 액션 청크"
title_zh: "第 5 篇 · 向 VLA 借鉴：动作 token 与动作块"
date: 2026-10-07 12:00:00 +0900
permalink: /blog/drone-policy/05-vla-recipes/
series: drone-policy
part: 5
tags: [drone, behaviour-cloning, action-tokens, action-chunking, evaluation, pytorch]
excerpt: "Part 5 of *Building and Evaluating a Drone VLA from Scratch*. Same small policy, same data; only the way it outputs an action changes. OpenVLA-style action tokens fix the flying, an 8-step action chunk makes it the best end-to-end model so far (pair success 3/10), and giving it more states makes it react to the words but stop stopping."
excerpt_ko: "연재 5편. 같은 작은 정책, 같은 데이터에서 행동을 출력하는 방식만 바꿨습니다. OpenVLA식 액션 토큰은 비행을 고치고 8스텝 액션 청크는 지금까지 가장 나은 end-to-end 모델(쌍 성공 3/10)이 됩니다. 상태를 더 주면 말에는 반응하지만 멈추지를 못합니다."
excerpt_zh: "系列第 5 篇：同样的小策略、同样的数据，只改变输出动作的方式。OpenVLA 式动作 token 修好了飞行，8 步动作块成为目前最好的端到端模型（pair 成功 3/10）；再给它更多状态，它会对指令作出反应，却停不下来。"
tldr_en:
  - "Only the action head and its loss changed; no pretrained model. <b>256-bin action tokens</b> (as in OpenVLA) raised success from 3/20 to 8/20 and cut the median final distance from 1.38 m to 0.22 m."
  - "Why: on the frame where only the words decide, regression answers <b>between</b> the two sentences' answers. A token head puts its probability mostly on <b>one</b> of them, although not always the right one (right in 135 of 200 training frames)."
  - "An <b>8-step action chunk</b>, executed one step at a time, is the best end-to-end model so far: 4 of 10 validation pairs split to the right targets, pair success <b>3/10</b> (95% interval 0.11–0.60, one training seed). Adding exploration states makes it react to the words like the expert on training frames, but in flight 8 of 20 runs overshoot and leave the room."
tldr_ko:
  - "바뀐 것은 행동 출력 헤드와 그 손실뿐이고 사전학습 모델은 없습니다. <b>256개 구간 액션 토큰</b>(OpenVLA 방식)으로 성공이 3/20에서 8/20으로 오르고 최종 거리 중앙값이 1.38 m에서 0.22 m로 줄었습니다."
  - "이유: 말만이 방향을 정하는 프레임에서 회귀는 두 문장의 정답 <b>사이</b>를 답합니다. 토큰 헤드는 확률을 대부분 둘 중 <b>하나</b>에 겁니다. 다만 늘 맞는 쪽은 아닙니다(학습 프레임 200개 중 135개에서 맞는 쪽)."
  - "한 스텝씩 실행하는 <b>8스텝 액션 청크</b>가 지금까지 가장 나은 end-to-end 모델입니다: 검증 10쌍 중 4쌍이 맞는 타깃으로 갈라졌고 쌍 성공 <b>3/10</b>(95% 구간 0.11–0.60, 학습 시드 1개). 탐색 상태를 더하면 학습 프레임에서는 전문가만큼 말에 반응하지만 실제 비행에서는 20번 중 8번이 목표를 지나쳐 방 밖으로 나갑니다."
tldr_zh:
  - "只改变了动作输出头和它的损失，没有预训练模型。<b>256 档动作 token</b>（OpenVLA 的做法）使成功率从 3/20 升至 8/20，最终距离中位数从 1.38 m 降到 0.22 m。"
  - "原因：在只有指令能决定方向的那一帧，回归给出两句指令答案<b>之间</b>的值；token 头则把概率主要放在其中<b>一个</b>上，但不总是对的那个（200 个训练帧中 135 个是对的）。"
  - "逐步执行的 <b>8 步动作块</b>是目前最好的端到端模型：10 个验证对中 4 个分别飞向正确目标，pair 成功 <b>3/10</b>（95% 区间 0.11–0.60，单个训练种子）。加入探索状态后，它在训练帧上对指令的反应达到专家水平，但实际飞行中 20 次有 8 次冲过目标飞出房间。"
---

{% include tldr.html %}

<div class="lang-zh pnote" lang="zh-Hans" markdown="1">

正文为英文，图表与代码与语言无关。

</div>

<div class="lang-enzh" markdown="1">

Part 4 ended with a diagnosis. The words matter on one frame per episode, the first, which is
2% of the training rows. On that frame the policy answered with the *average* of the two
sentences' answers. On every other frame the image already showed where to go.

Large vision-language-action models (VLAs) don't output actions the way that policy did. RT-2
and OpenVLA turn each action dimension into one of 256 **tokens** and train with cross-entropy.
OpenVLA-OFT and ACT predict a **chunk** of several future actions at once. This post borrows
those two output formats and nothing else. The encoders keep their design, the data and the
parameter budget stay as they were, and the image layer shrinks from 128 to 104 numbers to keep
the parameter counts comparable. There is still no pretrained model; that arrives in
Season 4.

<div class="vgrid">
  <figure class="pvid">
    <img src="/images/blog/drone-policy/05/pair7_bc_v0.2_s0.gif" alt="Part 4 policy on validation pair 7: both sentences end hovering between the two pillars" loading="lazy">
    <figcaption><span class="pvid__label">Part 4 regression</span>Validation pair 7. Both flights stop away from both targets.</figcaption>
  </figure>
  <figure class="pvid">
    <img src="/images/blog/drone-policy/05/pair7_bc_tokchunk8_v0.2_s0.gif" alt="Token chunk policy on validation pair 7: each sentence leads to its own pillar and both succeed" loading="lazy">
    <figcaption><span class="pvid__label">Tokens + 8-step chunk</span>Same pair. Each sentence reaches its own target. (Pair 7 was chosen because this model succeeds on both sentences there; it does so on 3 of 10 pairs.)</figcaption>
  </figure>
</div>

### How these experiments were run

This post is a screen, not a final result, so the rules are lighter than Part 4's and are
stated up front:

- **One training seed per model** (seed 0). Part 4 used three.
- Validation only: 10 pairs, 20 flights. The test split is untouched.
- Same training recipe as Part 4: 20 epochs, keep the last, dataset v0.2 (100 training
  pairs).
- Same size: every model has 80–100% of Part 4's 480,901 parameters.

Single-pair differences are noise at this sample size, so the post gives intervals where it
matters.

## 1. What changed: only the output

<figure class="pfig">
  <img src="/images/blog/drone-policy/05/01_method.png" alt="Diagram: camera, sentence and state go through the same encoder as Part 4 into a 168-number feature; three heads follow: (a) regression with tanh and Huber loss, (b) 256-bin tokens for vx and vy with cross-entropy and argmax decoding, (c) an 8-step chunk of tokens and Stop, decoded by a shared decoder with a learned step embedding" loading="lazy">
  <figcaption><b>Same backbone, three ways to output an action.</b> (a) Part 4 regresses the speeds. (b) Action tokens: vx and vy each become 256 bins between the 1st and 99th percentile of the training speeds; the head is trained with cross-entropy and flies the centre of the most likely bin. (c) Chunk: one trunk plus a learned embedding per future step predicts bins and Stop for the next 8 steps (1.6 s). Only step 1 is flown, and the model is asked again 0.2 s later. The image layer shrinks from 128 to 104 numbers in (b) and (c) to keep the parameter counts comparable.</figcaption>
</figure>

**Tokens.** For each axis, the training speeds are split into 256 equal bins between their
1st and 99th percentiles, as OpenVLA does. The head outputs 256 scores per axis instead of one
number. Training asks it to score the expert's bin highest (cross-entropy); flying uses the
centre of its top bin
([model code](https://github.com/Ckck12/Drone_VLA_Simulation/blob/7b9acb9551c3fb2d7392d9beaf32c249b6396630/dronevla/model.py#L145-L178)):

```python
def set_action_bins(self, actions):            # actions: (N, 4) training speeds / limits
    for i, d in enumerate((0, 1)):             # vx, vy; vz and yaw rate are always 0 here
        lo, hi = torch.quantile(actions[:, d], torch.tensor([0.01, 0.99]))
        self.bin_edges[i] = torch.linspace(lo, hi, 257)

def tokenize(self, actions):                   # training target: which bin
    return torch.stack([torch.bucketize(actions[:, d], self.bin_edges[i, 1:-1])
                        for i, d in enumerate((0, 1))], dim=1)

def detokenize(self, logits):                  # flying: the centre of the most likely bin
    centres = (self.bin_edges[:, :-1] + self.bin_edges[:, 1:]) / 2
    return centres.gather(1, logits.argmax(-1).T).T
```

**Chunks.** The head predicts the next 8 steps at once. A shared trunk produces one feature, a
learned embedding for each step k is added, and one decoder turns each into bins plus a Stop
logit. The loss covers every future step that exists in the episode
([training code](https://github.com/Ckck12/Drone_VLA_Simulation/blob/7b9acb9551c3fb2d7392d9beaf32c249b6396630/dronevla/train.py#L445-L456)):

```python
logits_h, stops_h = model.heads(feats)[2]          # (B, 8, 2, 256), (B, 8)
target = model.tokenize(chunk_action[idx])         # expert's next 8 actions, as bins
ce = cross_entropy(logits_h.flatten(0, 2), target.flatten(), reduction="none")
motion = (ce.view(B, 8, 2).mean(-1) * mask).sum() / mask.sum()   # mask: steps past the end
stop = (bce(stops_h, chunk_stop[idx]) * mask).sum() / mask.sum()
loss = motion + stop
```

The model is still asked every 0.2 s, and only the first step is flown. So the chunk changes
what the model is trained to predict, not how it flies. Section 3 also tests flying all 8
steps.

## 2. Why tokens fly better: averaging versus choosing

<figure class="pfig">
  <img src="/images/blog/drone-policy/05/02_midpoint_vs_pick.png" alt="Four training pairs. Top: the expert's two sideways speeds differ, while the regression model outputs nearly the same in-between value for both sentences. Bottom: the token model's probability over vy bins, sentence A upward and sentence B downward, concentrates on one expert answer; in pair 0 sentence B's mass sits mostly on the other sentence's answer" loading="lazy">
  <figcaption>The first frame of training pairs 0–3, in order (not selected). Top: the expert's two answers (filled) and the regression output for each sentence (open). Bottom: the token head's probability over the vy bins; sentence A points up, sentence B down; triangles mark the expert's answers.</figcaption>
</figure>

On the first frame, the image and the state are identical for both sentences of a pair, and
their labels differ. Regression with a Huber loss is pulled toward the middle. Over all 200
training first frames, the Part 4 model is closer to its own sentence's answer in exactly 100
and to the other sentence's answer in the other 100. It sits on average 0.03 m/s from the
midpoint.

A token head can't place its answer between two bins it was never shown. <span class="key-line">It has to put its
probability *somewhere*, and it mostly puts it on one expert answer.</span> It picks the right one in
135 of 200 training first frames. Its median error is 0.0005 m/s, but its mean is 0.066 m/s,
because the wrong picks are a whole answer away. Training pair 0, sentence B in the figure is
such a wrong pick.

In flight, a regression policy that starts in the middle tends to hover between the pillars.
A token policy commits to a side. That alone moves the numbers:

| validation, 20 flights | Part 4 regression | 256-bin tokens |
|---|---:|---:|
| success | 3 | **8** |
| median distance to the named target at the end | 1.38 m | **0.22 m** |
| stopped away from both targets | 8 | 3 |
| first frames where swapping the sentence changes vy by > 0.02 m/s (of 200 training frames) | 0 | 74 |

This is the multimodality problem that Diffusion Policy and Behavior Transformers were built
for: when two answers are both right, a model that averages them is wrong for both. Here the
two answers come from two sentences rather than from two demonstrators, but the loss can't tell
the difference on that frame.

## 3. Results: the chunk is the best end-to-end model so far

<figure class="pfig">
  <img src="/images/blog/drone-policy/05/04_scoreboard.png" alt="Scoreboard of seven models. Left: in how many of 10 validation pairs each sentence led to its own target: expert 10, world model planner 10, Part 4 regression 0, tokens 3, tokens with chunk 4 plus 1 swapped, chunk run open-loop 1, chunk with exploration 5 plus 2 swapped. Middle: success out of 20. Right: pair success out of 10 with 95% Wilson intervals: chunk 3 out of 10, others 0 or 1" loading="lazy">
  <figcaption>Validation, one training seed. Left: which target each sentence's flight came closest to. Right: pair success, both sentences of a layout succeeding, with 95% Wilson intervals. * = reference. The world model + planner is Part 6.</figcaption>
</figure>

- Tokens alone don't make the drone choose. With 256 bins, 7 of 10 pairs still go to the
  same pillar for both sentences. The token head commits, but on most first frames it commits
  to whatever the image suggests.
- <span class="key-line">Tokens + an 8-step chunk is the best end-to-end model: 4 pairs split the right way, and
  pair success is **3/10**.</span> One plausible reason: predicting 1.6 s ahead forces the
  representation to encode where the flight is going, not just the next step. Its 95% interval
  is 0.11–0.60, which overlaps Part 4's 0/10 (0–0.28). With one seed and ten pairs this is a
  lead, not a proven gain.
- Flying all 8 predicted steps before asking again flies about as well (9/20) but listens
  less (1 correct pair). My guess: re-asking every step lets each new image correct the
  course, and flying 8 steps blind gives that up.

The first three validation pairs, unselected, show how far from solved this is:

<figure class="pfig">
  <img src="/images/blog/drone-policy/05/05_paths.png" alt="Top views of validation pairs 0 to 2 for three models. The Part 4 policy's two paths overlap and stop short; the token policy splits pair 0 and 1 correctly; the chunk policy sends both sentences toward one pillar in pairs 0 and 2" loading="lazy">
  <figcaption>Validation pairs 0–2 (the first three, not selected). Colour = the target the sentence named; solid and dashed = the two sentences. On these three pairs the chunk model looks <i>worse</i> than tokens alone; across all ten it splits more pairs correctly (4 vs 3) and completes more (3 vs 1). Ten pairs is a small sample, and individual pairs disagree with the total.</figcaption>
</figure>

## 4. More states: it reacts, then it can't stop

Part 4's diagnosis was that the words decide only on frames the policy rarely sees. Part 6's
world model was trained on three times as many states: expert flights plus *exploration*
flights (noisy expert, random walks, overshoots past the goal). So I gave the chunk policy the
same 400 exploration flights (19,896 states), each state labelled with what the expert would do from there toward
a target picked at random for that episode, with the matching sentence. For exploration rows
only the current step is a label; their future chunk steps are masked, because a random walk
isn't the expert's plan.

<figure class="pfig">
  <img src="/images/blog/drone-policy/05/06_reaction_vs_stop.png" alt="Left: on the first frames of 200 training episodes, swapping the sentence changes vy by about 0 for Part 4, by a median near 0 for tokens, 0.12 m/s for the chunk, and 0.196 m/s with exploration states, close to the expert's 0.200. Right: closed-loop outcomes: with exploration states, 6 successes, 8 out of bounds, 4 collisions, 2 timeouts" loading="lazy">
  <figcaption>Left: the open-loop swap test from Part 4 on the 200 training first frames. Each dot is one frame; the bar is the median; the dashed line is the expert. Right: what happened in the 20 validation flights.</figcaption>
</figure>

On training frames, the swap test now looks like the expert. Changing only the sentence moves
the first command by a median 0.196 m/s (the expert: 0.200), and it moves in the right
direction in 180 of 200 frames. In closed loop it splits 5 of 10 pairs the right way, the most
of any end-to-end model (cross-attention + counterfactual relabelling also reaches 5). But **8 of 20 flights overshoot the target and leave the room**, success falls to 6, and
pair success is 0.

I didn't separate the causes. Three candidates:

- Stop is diluted. The exploration rows contain only 6 Stop examples, so Stop becomes a
  rarer event in the training mix.
- The bins got coarser. The bin range comes from the training speeds, and exploration
  widens the vy range from about ±0.17 to ±0.42 m/s. Each bin is 2.4 times wider, which is
  coarse near the goal.
- Recovery labels near the goal. Overshoot flights teach large corrections exactly where
  the expert usually slows down.

<span class="key-line">With exploration states the first-frame reaction matched the expert's on training
frames (median 0.196 vs 0.200 m/s), but pair success fell from 3/10 to 0/10.</span> The experiment
changes what the policy reacts to without changing how it stops. That split,
choosing versus flying, is what Part 6 builds in on purpose.

## What this part does not show

- **One training seed and ten pairs.** Wilson 95% for 3/10 is 0.11–0.60. Single-pair
  differences, and the bin-count comparison in the appendix, are within noise.
- Validation was used both to screen models and to set the Stop threshold, so these are not
  generalisation estimates. The original test seeds were seen in an earlier round; final
  numbers need a fresh test split.
- The swap test runs on training frames. Near-zero token errors there may partly be
  memorisation.
- The language is easy. The two pillars always differ in colour, so one word decides.
- It is not a VLA. Only the output format is borrowed; there is no pretrained vision or
  language model.

## Next

No end-to-end model splits more than 5 of 10 pairs the right way, and the best one completes 3. Part 6 tries the opposite design: a model that
perceives where *every* target is without reading the words, a tiny selector that lets the words
pick one, and a planner that flies there.

## Appendix

<details>
<summary>A. Everything else I tried (validation, one seed)</summary>

<table>
<thead><tr><th>model</th><th>params</th><th>correct / swapped / same-target pairs</th><th>success /20</th><th>pair success /10</th></tr></thead>
<tbody>
<tr><td>Part 4 regression</td><td>480,901</td><td>0 / 0 / 10</td><td>3</td><td>0</td></tr>
<tr><td>+ counterfactual relabelling</td><td>480,901</td><td>2 / 3 / 5</td><td>0</td><td>0</td></tr>
<tr><td>+ counterfactual + FiLM</td><td>492,517</td><td>4 / 3 / 3</td><td>0</td><td>0</td></tr>
<tr><td>+ target-position aux head</td><td>493,773</td><td>0 / 0 / 10</td><td>8</td><td>0</td></tr>
<tr><td>+ cross-attention</td><td>420,997</td><td>0 / 0 / 10</td><td>0</td><td>0</td></tr>
<tr><td>+ cross-attention + counterfactual</td><td>420,997</td><td>5 / 3 / 2</td><td>0</td><td>0</td></tr>
<tr><td>256-bin tokens</td><td>469,609</td><td>3 / 0 / 7</td><td>8</td><td>1</td></tr>
<tr><td>64-bin tokens</td><td>471,289</td><td>1 / 1 / 8</td><td>7</td><td>1</td></tr>
<tr><td>1024-bin tokens</td><td>475,609</td><td>2 / 0 / 8</td><td>4</td><td>0</td></tr>
<tr><td>256-bin + paired goal loss</td><td>480,555</td><td>2 / 0 / 8</td><td>7</td><td>0</td></tr>
<tr><td>256-bin + cross-attention</td><td>477,289</td><td>1 / 0 / 9</td><td>9</td><td>0</td></tr>
<tr><td><b>256-bin + chunk 8, fly step 1</b></td><td>470,633</td><td>4 / 1 / 5</td><td>8</td><td><b>3</b></td></tr>
<tr><td>256-bin + chunk 8, fly all 8</td><td>470,633</td><td>1 / 0 / 9</td><td>9</td><td>1</td></tr>
<tr><td>chunk 8 + exploration states</td><td>470,633</td><td>5 / 2 / 3</td><td>6</td><td>0</td></tr>
<tr><td>chunk 8 + exploration + aux head</td><td>468,909</td><td>5 / 2 / 3</td><td>2</td><td>0</td></tr>
</tbody>
</table>

<p>Notes:</p>
<ul>
<li>Counterfactual relabelling adds, for every training state, the expert's action toward the <i>other</i> target with the other sentence. The words start to matter, but correct and swapped pairs are about equal, and flying collapses. Part of that is a confound: the added rows contain no Stop positives, while the Stop loss weight stayed at its original value.</li>
<li>Aux head: an extra output trained to predict where every colour's target is, from the simulator's true positions (the same labels Part 6's world model gets). It flies better (8/20) and listens no more.</li>
<li>FiLM and cross-attention let the sentence modulate the image features (FiLM scales and shifts the convolution channels; cross-attention uses the sentence as a query over 6 × 8 image patches). The FiLM row and the aux-head row predate the 80–100% parameter rule (FiLM 102%, aux 103%).</li>
<li>Bin count: 64, 256 and 1024 bins are within one seed's noise of each other.</li>
</ul>

<figure class="pfig">
  <img src="/images/blog/drone-policy/05/03_attention.png" alt="Attention maps on the first frames of validation pairs 0 and 1: the regression model attends to floor patches, nearly the same for both sentences; the token model attends along the top of the image near the pillars, also nearly the same for both sentences" loading="lazy">
  <figcaption>Where the cross-attention models look, on the shared first frame of validation pairs 0 and 1, for each sentence (head-averaged weights, normalised per map). Both maps hardly change with the sentence. Attention weights are not a causal explanation (Jain &amp; Wallace, 2019); the swap test in section 4 is the measurement that counts.</figcaption>
</figure>
</details>

<details>
<summary>B. Reproduce</summary>

<p>Commit <a href="https://github.com/Ckck12/Drone_VLA_Simulation/tree/7b9acb9551c3fb2d7392d9beaf32c249b6396630"><code>7b9acb9</code></a>, dataset v0.2 from Part 3, exploration flights from <code>python -m dronevla.explore</code>.</p>

<pre><code>python -m dronevla.train --data data/v0.2 --epochs 20 --select last --seed 0 \
    --action-bins 256 --img-dim 104 --out runs/bc_tok256_v0.2_s0
python -m dronevla.train --data data/v0.2 --epochs 20 --select last --seed 0 \
    --action-bins 256 --img-dim 104 --chunk 8 --out runs/bc_tokchunk8_v0.2_s0
python -m dronevla.train --data data/v0.2 --epochs 20 --select last --seed 0 \
    --action-bins 256 --img-dim 104 --chunk 8 --explore data/explore_v0.2/train \
    --out runs/bc_tokchunk8_explore_v0.2_s0
bash scripts/run_tokens_s0.sh && bash scripts/run_tokens2_s0.sh   # evaluate (and train if missing)
bash scripts/run_explore_s0.sh
python scripts/season2_summary.py                 # every number in this post (needs all run_*_s0.sh)
python scripts/figures/blog05.py                  # figures
bash scripts/figures/render_blog05_videos.sh      # GIFs
</code></pre>
<p>All screening runs: <code>scripts/run_*_s0.sh</code>. Full write-up: <code>docs/season2_experiments.md</code>.</p>
</details>

### References: what I took from each

- [OpenVLA (Kim et al., 2024)](https://arxiv.org/abs/2406.09246) and
  [RT-2 (Brohan et al., 2023)](https://arxiv.org/abs/2307.15818) discretise each action
  dimension into 256 bins and predict them as tokens. → The token head and its 1st–99th
  percentile bins.
- [OpenVLA-OFT (Kim, Finn and Liang, 2025)](https://arxiv.org/abs/2502.19645) and
  [ACT (Zhao et al., 2023)](https://arxiv.org/abs/2304.13705) predict chunks of future actions.
  → The 8-step chunk. Here only the first step is flown.
- [Diffusion Policy (Chi et al., 2023)](https://arxiv.org/abs/2303.04137) and
  [Behavior Transformers (Shafiullah et al., 2022)](https://arxiv.org/abs/2206.11251) show that
  averaging a multimodal action distribution fails. → The explanation for section 2.
- [Stop Regressing (Farebrother et al., 2024)](https://arxiv.org/abs/2403.03950) finds
  classification losses train value networks better than regression. → Why switching the loss
  alone was worth testing.
- [Causal Confusion in Imitation Learning (de Haan et al., 2019)](https://arxiv.org/abs/1905.11979)
  describes policies that latch onto the wrong cue. → The image shortcut from Part 4, which
  tokens don't remove.

## Summary (STAR)

<div class="star-summary" markdown="1">

<h3 class="star-h"><span class="star-tag">S</span>Situation</h3>

In Part 4 the regression policy answered with the average of the two sentences' answers on the
first frame, the only frame where the words decide (2% of the training rows). On validation it
split 0 of 10 pairs and succeeded in 3 of 20 flights.

<h3 class="star-h"><span class="star-tag">T</span>Task</h3>

Find out whether borrowing only the output formats of VLAs, action tokens and action chunks,
fixes this with the same encoder design, the same dataset v0.2, 80–100% of Part 4's 480,901
parameters and no pretrained model. The image projection shrank from 128 to 104 outputs to keep
the parameter counts comparable. This was a screen: one training seed, validation only.

<h3 class="star-h"><span class="star-tag">A</span>Action</h3>

I replaced the regression head with 256 bins per axis trained with cross-entropy, then added an
8-step (1.6 s) chunk head that is flown one step at a time. I also tried flying all 8 steps and
adding 400 exploration flights (19,896 states), and measured each model with the sentence swap
test and 20 closed-loop flights, with Wilson intervals.

<h3 class="star-h"><span class="star-tag">R</span>Result</h3>

Tokens raised success from 3/20 to 8/20 and cut the median final distance from 1.38 m to
0.22 m. Tokens plus the chunk split 4 of 10 pairs the right way, with pair success 3/10 (95%
interval 0.11–0.60, overlapping Part 4's 0–0.28): a lead, not a proven gain. Exploration
states made the swap reaction match the expert (0.196 vs 0.200 m/s), but 8 of 20 flights
overshot and left the room, and pair success fell to 0.

</div>

</div>

<div class="lang-ko" lang="ko" markdown="1">

4편은 진단으로 끝났습니다. 말이 의미를 갖는 프레임은 에피소드마다 하나, 첫 프레임뿐이고
학습 행으로 치면 2%입니다. 그 프레임에서 정책은 두 문장 정답의 *평균*을 답했습니다. 나머지
프레임에서는 어디로 갈지 이미지가 이미 알려 주고 있었습니다.

대형 비전-언어-행동 모델(vision-language-action model, VLA)은 그 정책처럼 행동을 출력하지
않습니다. RT-2와 OpenVLA는 행동의 각 차원을 256개 **토큰** 중 하나로 바꾸고 cross-entropy로
학습합니다. OpenVLA-OFT와 ACT는 앞으로 할 행동 여러 개를 한 덩어리, 곧 **청크**로 한 번에
예측합니다. 이번 글은 이 두 출력 형식만 빌려 옵니다. 인코더의 기본 구조와 데이터, 파라미터
예산은 그대로 두고 파라미터 수를 비슷하게 맞추려고 이미지 층의 출력만 숫자 128개에서 104개로
줄입니다. 사전학습 모델은 이번에도 쓰지 않습니다. 그건 시즌 4의 몫입니다.

<div class="vgrid">
  <figure class="pvid">
    <img src="/images/blog/drone-policy/05/pair7_bc_v0.2_s0.gif" alt="4편 정책의 검증 쌍 7 비행: 두 문장 모두 두 기둥 사이에 떠 있는 채로 끝납니다" loading="lazy">
    <figcaption><span class="pvid__label">4편 회귀</span>검증 쌍(pair) 7. 두 비행 모두 어느 목표와도 떨어진 곳에서 멈춥니다.</figcaption>
  </figure>
  <figure class="pvid">
    <img src="/images/blog/drone-policy/05/pair7_bc_tokchunk8_v0.2_s0.gif" alt="토큰 청크 정책의 검증 쌍 7 비행: 문장마다 자기 기둥으로 가서 둘 다 성공합니다" loading="lazy">
    <figcaption><span class="pvid__label">토큰 + 8스텝 청크</span>같은 쌍입니다. 문장마다 자기 목표에 도착합니다. (쌍 7은 이 모델이 두 문장 모두 성공하는 쌍이라서 골랐습니다. 그런 쌍은 10개 중 3개입니다.)</figcaption>
  </figure>
</div>

### 실험 방식

이번 글은 최종 결과가 아니라 후보를 걸러 보는 스크리닝입니다. 그래서 규칙을 4편보다 가볍게
잡았고 그 내용을 먼저 밝혀 둡니다.

- **모델마다 학습 시드 하나**(시드 0). 4편은 세 개를 썼습니다.
- 검증 데이터만 씁니다. 10쌍, 비행 20회입니다. 테스트 분할은 건드리지 않았습니다.
- 학습 방식은 4편과 같습니다. 20 epoch, 마지막 모델 사용, 데이터셋 v0.2(학습 쌍 100개).
- 크기도 맞췄습니다. 모든 모델의 파라미터 수는 4편 480,901개의 80–100%입니다.

이 정도 표본에서는 쌍 하나 차이가 잡음에 묻힙니다. 그래서 중요한 곳에는 구간을 같이 적었습니다.

## 1. 바뀐 것은 출력뿐

<figure class="pfig">
  <img src="/images/blog/drone-policy/05/01_method.png" alt="도식: 카메라, 문장, 상태가 4편과 같은 인코더를 거쳐 숫자 168개짜리 특징이 되고 그 뒤에 헤드 세 가지가 붙습니다. (a) tanh와 Huber 손실을 쓰는 회귀, (b) vx와 vy를 256개 구간 토큰으로 바꿔 cross-entropy로 학습하고 argmax로 디코딩, (c) 토큰과 Stop을 8스텝 청크로 예측하고 학습된 스텝 임베딩과 공유 디코더로 디코딩" loading="lazy">
  <figcaption><b>같은 백본, 행동을 내는 세 가지 방식.</b> (a) 4편은 속도를 회귀합니다. (b) 액션 토큰: vx와 vy를 각각 학습 속도의 1번째와 99번째 백분위수 사이에서 256개 구간(bin)으로 나눕니다. 헤드는 cross-entropy로 학습합니다. 비행할 때는 확률이 가장 높은 구간의 중심값으로 납니다. (c) 청크: 공유 네트워크(trunk)에 미래 스텝마다 학습된 임베딩을 더해 다음 8스텝(1.6 s)의 구간과 Stop을 예측합니다. 실제로 나는 것은 1번째 스텝뿐이고 0.2 s 뒤에 모델에 다시 묻습니다. 파라미터 수를 비슷하게 맞추려고 (b)와 (c)에서는 이미지 층의 출력을 숫자 128개에서 104개로 줄였습니다.</figcaption>
</figure>

**토큰.** 축마다 학습 속도의 1번째와 99번째 백분위수 사이를 같은 폭의 구간 256개로 나눕니다.
OpenVLA와 같은 방식입니다. 헤드는 숫자 하나 대신 축마다 점수 256개를 냅니다. 학습에서는
전문가 행동이 들어 있는 구간에 가장 높은 점수를 주도록 가르칩니다(cross-entropy). 비행할 때는
점수가 가장 높은 구간의 중심값을 씁니다
([모델 코드](https://github.com/Ckck12/Drone_VLA_Simulation/blob/7b9acb9551c3fb2d7392d9beaf32c249b6396630/dronevla/model.py#L145-L178)).

```python
def set_action_bins(self, actions):            # actions: (N, 4) training speeds / limits
    for i, d in enumerate((0, 1)):             # vx, vy; vz and yaw rate are always 0 here
        lo, hi = torch.quantile(actions[:, d], torch.tensor([0.01, 0.99]))
        self.bin_edges[i] = torch.linspace(lo, hi, 257)

def tokenize(self, actions):                   # training target: which bin
    return torch.stack([torch.bucketize(actions[:, d], self.bin_edges[i, 1:-1])
                        for i, d in enumerate((0, 1))], dim=1)

def detokenize(self, logits):                  # flying: the centre of the most likely bin
    centres = (self.bin_edges[:, :-1] + self.bin_edges[:, 1:]) / 2
    return centres.gather(1, logits.argmax(-1).T).T
```

**청크.** 헤드가 다음 8스텝을 한 번에 예측합니다. 공유 네트워크가 특징을 추출하고 스텝 k마다
학습된 임베딩을 더합니다. 그러면 디코더 하나가 각각을 구간과 Stop 로짓으로 바꿉니다. 손실에는 에피소드
안에 실제로 남아 있는 미래 스텝이 모두 들어갑니다
([학습 코드](https://github.com/Ckck12/Drone_VLA_Simulation/blob/7b9acb9551c3fb2d7392d9beaf32c249b6396630/dronevla/train.py#L445-L456)).

```python
logits_h, stops_h = model.heads(feats)[2]          # (B, 8, 2, 256), (B, 8)
target = model.tokenize(chunk_action[idx])         # expert's next 8 actions, as bins
ce = cross_entropy(logits_h.flatten(0, 2), target.flatten(), reduction="none")
motion = (ce.view(B, 8, 2).mean(-1) * mask).sum() / mask.sum()   # mask: steps past the end
stop = (bce(stops_h, chunk_stop[idx]) * mask).sum() / mask.sum()
loss = motion + stop
```

모델에는 여전히 0.2 s마다 묻고 실제로 나는 것은 첫 스텝뿐입니다. 그러니 청크는 모델이
무엇을 예측하도록 학습하는지만 바꾸고 나는 방식은 그대로 둡니다. 8스텝을 모두 나는 경우는
3절에서 따로 시험합니다.

## 2. 토큰이 더 잘 나는 이유, 평균과 선택의 차이

<figure class="pfig">
  <img src="/images/blog/drone-policy/05/02_midpoint_vs_pick.png" alt="학습 쌍 네 개. 위: 전문가의 두 옆방향 속도는 서로 다른데 회귀 모델은 두 문장 모두에 거의 같은 중간값을 냅니다. 아래: 토큰 모델이 vy 구간에 거는 확률(문장 A는 위쪽, 문장 B는 아래쪽)은 전문가 답 하나에 몰립니다. 쌍 0의 문장 B는 확률 대부분이 다른 문장의 답에 가 있습니다" loading="lazy">
  <figcaption>학습 쌍 0–3의 첫 프레임을 순서대로 실었습니다(골라낸 것이 아닙니다). 위: 전문가의 두 답(채운 기호)과 문장별 회귀 출력(빈 기호). 아래: 토큰 헤드가 vy 구간에 거는 확률입니다. 문장 A는 위쪽, 문장 B는 아래쪽으로 그렸고 삼각형이 전문가의 답입니다.</figcaption>
</figure>

첫 프레임에서 한 쌍의 두 문장은 이미지도 상태도 똑같고 라벨만 다릅니다. Huber 손실을 쓰는
회귀는 그 가운데로 끌려갑니다. 학습 첫 프레임 200개를 모두 보면 4편 모델이 자기 문장의 답에
더 가까운 경우가 정확히 100개, 다른 문장의 답에 더 가까운 경우가 나머지 100개입니다. 중간점에서
떨어진 거리는 평균 0.03 m/s에 불과합니다.

토큰 헤드는 두 구간 사이, 학습에서 본 적 없는 자리에 답을 놓을 수 없습니다.
<span class="key-line">확률을 *어딘가에는* 걸어야 하는데 대부분 전문가 답 하나에 겁니다.</span>
맞는 쪽을 고른 경우는 학습 첫 프레임 200개 중 135개입니다. 오차 중앙값은 0.0005 m/s인데 평균은
0.066 m/s나 됩니다. 잘못 고르면 답 하나만큼 통째로 빗나가기 때문입니다. 그림에서 학습 쌍 0의
문장 B가 그렇게 잘못 고른 경우입니다.

비행해 보면 가운데서 출발한 회귀 정책은 두 기둥 사이에서 맴돌기 쉽습니다. 토큰 정책은 한쪽을
정하고 갑니다. 이것만으로 숫자가 달라집니다.

| 검증, 비행 20회 | 4편 회귀 | 256개 구간을 쓰는 토큰 모델 |
|---|---:|---:|
| 성공 | 3 | **8** |
| 끝났을 때 지시한 목표까지 거리 중앙값 | 1.38 m | **0.22 m** |
| 두 목표 모두와 떨어진 곳에서 멈춤 | 8 | 3 |
| 문장을 바꾸면 vy가 0.02 m/s보다 크게 바뀌는 첫 프레임 (학습 프레임 200개 중) | 0 | 74 |

Diffusion Policy와 Behavior Transformers가 풀려고 한 다중 모드(multimodality) 문제가 바로
이것입니다. 정답이 둘 다 맞을 때 둘을 평균 내는 모델은 양쪽 모두에서 틀립니다. 여기서는 두
답이 두 문장에서 나옵니다. 시연자가 둘인 경우와 출처는 다르지만 그 프레임의 손실로는 둘을
구별할 수 없습니다.

## 3. 결과: 지금까지 가장 나은 end-to-end 모델은 청크

<figure class="pfig">
  <img src="/images/blog/drone-policy/05/04_scoreboard.png" alt="모델 일곱 개의 점수판. 왼쪽: 검증 10쌍 중 문장마다 자기 목표로 간 쌍의 수. 전문가 10, 월드 모델 플래너 10, 4편 회귀 0, 토큰 3, 토큰+청크 4(뒤바뀐 쌍 1), 청크 개루프 실행 1, 청크+탐색 5(뒤바뀐 쌍 2). 가운데: 20회 중 성공. 오른쪽: 10쌍 중 쌍 성공과 95% Wilson 구간. 청크 3/10, 나머지는 0 또는 1" loading="lazy">
  <figcaption>검증, 학습 시드 하나. 왼쪽: 문장마다 비행이 어느 목표에 가장 가까이 갔는지. 오른쪽: 쌍 성공(pair success), 곧 한 레이아웃의 두 문장이 모두 성공한 비율과 95% Wilson 구간. * = 참고 기준. 월드 모델 + 플래너는 6편에서 다룹니다.</figcaption>
</figure>

- 토큰만으로는 드론이 고르게 되지 않습니다. 256개 구간을 써도 10쌍 중 7쌍은 두 문장 모두 같은
  기둥으로 갑니다. 토큰 헤드가 한쪽을 정하기는 하지만 대부분의 첫 프레임에서 정하는 쪽은
  이미지가 가리키는 쪽입니다.
- <span class="key-line">토큰 + 8스텝 청크가 가장 나은 end-to-end 모델입니다. 4쌍이 맞는 방향으로
  갈라지고 쌍 성공은 **3/10**입니다.</span> 이유로 짐작되는 것이 하나 있습니다. 1.6 s 앞을
  예측하게 하면 표현(representation)이 다음 스텝뿐 아니라 비행이 어디로 향하는지까지 담아야
  합니다. 95% 구간은 0.11–0.60으로 4편의 0/10(0–0.28)과 겹칩니다. 시드 하나, 쌍 10개로는 단서
  정도이고 개선이 입증되지는 않았습니다.
- 예측한 8스텝을 다 날고 나서 다시 묻는 방식은 비슷하게 날지만(9/20) 말을 덜 듣습니다(맞게
  갈라진 쌍 1개). 제 짐작은 이렇습니다. 매 스텝 다시 물으면 새 이미지가 들어올 때마다 경로를
  바로잡을 수 있습니다. 8스텝을 눈 감고 날면 그 기회가 사라집니다.

골라내지 않은 첫 검증 쌍 세 개를 보면 아직 갈 길이 얼마나 먼지 보입니다.

<figure class="pfig">
  <img src="/images/blog/drone-policy/05/05_paths.png" alt="세 모델의 검증 쌍 0–2 비행을 위에서 본 그림. 4편 정책은 두 경로가 겹치고 목표 전에 멈춥니다. 토큰 정책은 쌍 0과 1을 맞게 가릅니다. 청크 정책은 쌍 0과 2에서 두 문장을 모두 한 기둥 쪽으로 보냅니다" loading="lazy">
  <figcaption>검증 쌍 0–2(골라내지 않은 첫 세 쌍). 색 = 문장이 지시한 목표, 실선과 점선 = 두 문장. 이 세 쌍만 보면 청크 모델이 토큰만 쓴 모델보다 <i>나빠</i> 보입니다. 열 쌍 전체로는 맞게 가른 쌍도 더 많고(4 대 3) 두 문장 모두 성공한 쌍도 더 많습니다(3 대 1). 열 쌍은 작은 표본이라 쌍 하나하나의 결과가 전체와 어긋나기도 합니다.</figcaption>
</figure>

## 4. 상태를 늘리면 말에는 반응하지만 멈추지 못합니다

4편은 말이 방향을 정하는 프레임을 정책이 거의 보지 못한다고 진단했습니다. 6편의 월드 모델은
세 배 많은 상태로 학습했습니다. 전문가 비행에 *탐색* 비행(잡음을 넣은 전문가, 랜덤 워크,
목표를 지나치는 비행)을 더한 데이터입니다. 그래서 청크 정책에도 같은 탐색 비행 400개(상태
19,896개)를 줬습니다. 에피소드마다 목표를 무작위로 하나 고릅니다. 각 상태에는 전문가가 그
자리에서 그 목표로 가려고 할 행동을 라벨로 달고 그 목표에 맞는 문장을 짝지었습니다. 탐색
행에서는 현재 스텝만 라벨로 씁니다. 랜덤 워크는 전문가의 계획이 아니므로 미래 청크 스텝은
마스킹했습니다.

<figure class="pfig">
  <img src="/images/blog/drone-policy/05/06_reaction_vs_stop.png" alt="왼쪽: 학습 에피소드 200개의 첫 프레임에서 문장을 바꿨을 때 vy가 바뀌는 양. 4편은 거의 0, 토큰은 중앙값이 0 근처, 청크는 0.12 m/s, 탐색 상태를 더하면 0.196 m/s로 전문가의 0.200에 가깝습니다. 오른쪽: 폐루프 결과. 탐색 상태를 더한 모델은 성공 6, 범위 이탈 8, 충돌 4, 시간 초과 2" loading="lazy">
  <figcaption>왼쪽: 4편의 개루프 문장 교체 테스트(swap test)를 학습 첫 프레임 200개에 돌린 결과입니다. 점 하나가 프레임 하나, 막대가 중앙값, 점선이 전문가입니다. 오른쪽: 검증 비행 20회에서 일어난 일.</figcaption>
</figure>

학습 프레임에서는 이제 문장 교체 테스트 결과가 전문가와 비슷합니다. 문장만 바꿔도
첫 명령이 중앙값 0.196 m/s만큼 움직입니다(전문가는 0.200). 방향도 200개 프레임 중 180개에서
맞습니다. 폐루프에서는 10쌍 중 5쌍을 맞게 가릅니다. end-to-end 모델 중 가장 많은 수입니다(cross-attention + counterfactual 재라벨링도 5쌍). 그런데
**비행 20회 중 8회가 목표를 지나쳐 방 밖으로 나가고** 성공은 6회로 떨어지며 쌍 성공은 0입니다.

원인은 따로 떼어 보지 않았습니다. 후보는 셋입니다.

- Stop이 희석됩니다. 탐색 행에는 Stop 예시가 6개뿐이라서 학습 데이터 전체에서 Stop이 더 드문
  사건이 됩니다.
- 구간이 거칠어졌습니다. 구간 범위는 학습 속도에서 정하는데 탐색 데이터가 vy 범위를 약 ±0.17에서
  ±0.42 m/s로 넓힙니다. 구간 하나가 2.4배 넓어지니 목표 근처에서는 거칩니다.
- 목표 근처의 복구 라벨. 목표를 지나친 비행은 전문가가 보통 속도를 줄이는 바로 그 자리에서 크게
  방향을 고치라고 가르칩니다.

<span class="key-line">탐색 상태를 더하자 학습 프레임에서 첫 반응은 전문가 수준이 됐지만(중앙값 0.196 대
0.200 m/s) 쌍 성공은 3/10에서 0/10으로 떨어졌습니다.</span> 이 실험으로 정책이 무엇에 반응하는지는
바뀌었지만 어떻게 멈추는지는 그대로였습니다. 고르는 일과 나는 일을 이렇게 나누는 구조를 6편은 일부러 설계에 넣습니다.

## 이번 편이 보여 주지 못하는 것

- **학습 시드 하나, 쌍 10개.** 3/10의 Wilson 95% 구간은 0.11–0.60입니다. 쌍 하나 차이도, 부록의
  구간 수 비교도 잡음 범위 안에 있습니다.
- 검증 데이터를 모델 선별과 Stop 임계값 설정에 모두 썼습니다. 그래서 이 숫자를 일반화 추정치로
  볼 수는 없습니다. 원래 테스트 시드는 이전 라운드에서 이미 본 적이 있어 최종 숫자를 내려면 새
  테스트 분할이 필요합니다.
- 문장 교체 테스트는 학습 프레임에서 돌립니다. 거기서 토큰 오차가 0에 가까운 데는 암기도 일부
  섞였을 수 있습니다.
- 언어가 쉽습니다. 두 기둥은 늘 색이 달라서 단어 하나로 결정됩니다.
- VLA는 아닙니다. 출력 형식만 빌려 왔고 사전학습된 비전 모델이나 언어 모델은 없습니다.

## 다음 편

end-to-end 모델은 10쌍 중 많아야 5쌍을 맞게 가르고 가장 나은 모델도 3쌍만 완료합니다. 6편은 반대쪽 설계를 시험합니다. 말을
읽지 않고 *모든* 목표의 위치를 인지하는 모델, 말로 그중 하나를 고르는 작은 목표 선택기, 그리고
그곳까지 날아가는 플래너입니다.

## 부록

<details>
<summary>A. 그 밖에 시도한 것 전부 (검증, 시드 하나)</summary>

<table>
<thead><tr><th>모델</th><th>파라미터</th><th>맞음 / 뒤바뀜 / 같은 목표 쌍</th><th>성공 /20</th><th>쌍 성공 /10</th></tr></thead>
<tbody>
<tr><td>4편 회귀</td><td>480,901</td><td>0 / 0 / 10</td><td>3</td><td>0</td></tr>
<tr><td>+ counterfactual 재라벨링</td><td>480,901</td><td>2 / 3 / 5</td><td>0</td><td>0</td></tr>
<tr><td>+ counterfactual + FiLM</td><td>492,517</td><td>4 / 3 / 3</td><td>0</td><td>0</td></tr>
<tr><td>+ 목표 위치 보조 헤드</td><td>493,773</td><td>0 / 0 / 10</td><td>8</td><td>0</td></tr>
<tr><td>+ cross-attention</td><td>420,997</td><td>0 / 0 / 10</td><td>0</td><td>0</td></tr>
<tr><td>+ cross-attention + counterfactual</td><td>420,997</td><td>5 / 3 / 2</td><td>0</td><td>0</td></tr>
<tr><td>256개 구간을 쓰는 토큰 모델</td><td>469,609</td><td>3 / 0 / 7</td><td>8</td><td>1</td></tr>
<tr><td>64개 구간을 쓰는 토큰 모델</td><td>471,289</td><td>1 / 1 / 8</td><td>7</td><td>1</td></tr>
<tr><td>1024개 구간을 쓰는 토큰 모델</td><td>475,609</td><td>2 / 0 / 8</td><td>4</td><td>0</td></tr>
<tr><td>256개 구간 + 쌍 목표 손실</td><td>480,555</td><td>2 / 0 / 8</td><td>7</td><td>0</td></tr>
<tr><td>256개 구간 + cross-attention</td><td>477,289</td><td>1 / 0 / 9</td><td>9</td><td>0</td></tr>
<tr><td><b>256개 구간 + 청크 8, 1스텝만 비행</b></td><td>470,633</td><td>4 / 1 / 5</td><td>8</td><td><b>3</b></td></tr>
<tr><td>256개 구간 + 청크 8, 8스텝 모두 비행</td><td>470,633</td><td>1 / 0 / 9</td><td>9</td><td>1</td></tr>
<tr><td>청크 8 + 탐색 상태</td><td>470,633</td><td>5 / 2 / 3</td><td>6</td><td>0</td></tr>
<tr><td>청크 8 + 탐색 + 보조 헤드</td><td>468,909</td><td>5 / 2 / 3</td><td>2</td><td>0</td></tr>
</tbody>
</table>

<p>메모:</p>
<ul>
<li>Counterfactual 재라벨링은 모든 학습 상태에 <i>다른</i> 목표를 향한 전문가 행동을 다른 문장과 함께 추가합니다. 말이 영향을 주기 시작하지만 맞게 가른 쌍과 뒤바뀐 쌍이 거의 같은 수이고 비행은 무너집니다. 여기에는 교란 요인도 일부 섞여 있습니다. 추가한 행에는 Stop 양성 예시가 없는데 Stop 손실 가중치는 원래 값 그대로였습니다.</li>
<li>보조 헤드: 색마다 목표가 어디 있는지 예측하는 추가 출력입니다. 라벨은 시뮬레이터의 실제 위치이고 6편의 월드 모델이 받는 라벨과 같습니다. 비행은 나아지지만(8/20) 말을 더 듣지는 않습니다.</li>
<li>FiLM과 cross-attention은 문장으로 이미지 특징을 조절합니다. FiLM은 합성곱 채널에 스케일과 이동을 겁니다. cross-attention은 문장을 query로 삼아 6 × 8 이미지 패치를 봅니다. FiLM 행과 보조 헤드 행은 80–100% 파라미터 규칙을 정하기 전에 만든 모델입니다(FiLM 102%, 보조 헤드 103%).</li>
<li>구간 수: 64, 256, 1024 구간은 서로 시드 하나의 잡음 범위 안에 있습니다.</li>
</ul>

<figure class="pfig">
  <img src="/images/blog/drone-policy/05/03_attention.png" alt="검증 쌍 0과 1의 첫 프레임 attention 맵. 회귀 모델은 바닥 패치를 보고 두 문장에서 거의 같습니다. 토큰 모델은 이미지 위쪽 기둥 근처를 따라 보는데 이것도 두 문장에서 거의 같습니다" loading="lazy">
  <figcaption>cross-attention 모델들이 검증 쌍 0과 1의 공통 첫 프레임에서 문장별로 어디를 보는지 나타냈습니다(헤드 평균 가중치, 맵마다 정규화). 두 맵 모두 문장에 따라 거의 바뀌지 않습니다. attention 가중치는 인과적 설명이 되지 못합니다(Jain &amp; Wallace, 2019). 믿을 만한 측정은 4절의 문장 교체 테스트입니다.</figcaption>
</figure>
</details>

<details>
<summary>B. 재현하기</summary>

<p>커밋 <a href="https://github.com/Ckck12/Drone_VLA_Simulation/tree/7b9acb9551c3fb2d7392d9beaf32c249b6396630"><code>7b9acb9</code></a>, 3편의 데이터셋 v0.2, <code>python -m dronevla.explore</code>로 만든 탐색 비행을 씁니다.</p>

<pre><code>python -m dronevla.train --data data/v0.2 --epochs 20 --select last --seed 0 \
    --action-bins 256 --img-dim 104 --out runs/bc_tok256_v0.2_s0
python -m dronevla.train --data data/v0.2 --epochs 20 --select last --seed 0 \
    --action-bins 256 --img-dim 104 --chunk 8 --out runs/bc_tokchunk8_v0.2_s0
python -m dronevla.train --data data/v0.2 --epochs 20 --select last --seed 0 \
    --action-bins 256 --img-dim 104 --chunk 8 --explore data/explore_v0.2/train \
    --out runs/bc_tokchunk8_explore_v0.2_s0
bash scripts/run_tokens_s0.sh && bash scripts/run_tokens2_s0.sh   # evaluate (and train if missing)
bash scripts/run_explore_s0.sh
python scripts/season2_summary.py                 # every number in this post (needs all run_*_s0.sh)
python scripts/figures/blog05.py                  # figures
bash scripts/figures/render_blog05_videos.sh      # GIFs
</code></pre>
<p>스크리닝 실행 전체: <code>scripts/run_*_s0.sh</code>. 전체 기록: <code>docs/season2_experiments.md</code>.</p>
</details>

### 참고 문헌과 각각에서 가져온 것

- [OpenVLA (Kim et al., 2024)](https://arxiv.org/abs/2406.09246)와
  [RT-2 (Brohan et al., 2023)](https://arxiv.org/abs/2307.15818)는 행동의 각 차원을 256개 구간으로
  이산화해 토큰으로 예측합니다. → 토큰 헤드와 1번째–99번째 백분위수 구간.
- [OpenVLA-OFT (Kim, Finn and Liang, 2025)](https://arxiv.org/abs/2502.19645)와
  [ACT (Zhao et al., 2023)](https://arxiv.org/abs/2304.13705)는 미래 행동을 청크로 예측합니다.
  → 8스텝 청크. 여기서는 첫 스텝만 납니다.
- [Diffusion Policy (Chi et al., 2023)](https://arxiv.org/abs/2303.04137)와
  [Behavior Transformers (Shafiullah et al., 2022)](https://arxiv.org/abs/2206.11251)는 다중 모드
  행동 분포를 평균 내면 실패한다는 것을 보였습니다. → 2절의 설명.
- [Stop Regressing (Farebrother et al., 2024)](https://arxiv.org/abs/2403.03950)는 가치 네트워크를
  학습할 때 분류 손실이 회귀보다 낫다는 결과를 냈습니다. → 손실만 바꿔 보는 실험이 해 볼 만했던
  근거.
- [Causal Confusion in Imitation Learning (de Haan et al., 2019)](https://arxiv.org/abs/1905.11979)은
  엉뚱한 단서에 매달리는 정책을 다룹니다. → 4편의 이미지 지름길(shortcut). 토큰을 써도 이 지름길은
  사라지지 않습니다.

## 요약 (STAR)

<div class="star-summary" markdown="1">

<h3 class="star-h"><span class="star-tag">S</span>상황</h3>

4편의 회귀 정책은 말이 방향을 정하는 유일한 프레임(첫 프레임, 학습 행의 2%)에서 두 문장 정답의
평균을 답했습니다. 검증에서 맞게 가른 쌍은 10개 중 0개였고 성공은 비행 20회 중 3회였습니다.

<h3 class="star-h"><span class="star-tag">T</span>과제</h3>

VLA의 출력 형식, 곧 액션 토큰과 액션 청크만 빌려 와서 이 문제가 풀리는지 확인하는 일이었습니다.
인코더의 기본 구조와 데이터셋 v0.2는 유지하되 파라미터 수를 맞추려고 이미지 출력 차원을 128에서
104로 줄였고 파라미터는 4편 480,901개의 80–100%로 맞췄으며 사전학습 모델은 쓰지 않았습니다. 학습 시드 하나로 검증 데이터만 쓴 스크리닝입니다.

<h3 class="star-h"><span class="star-tag">A</span>행동</h3>

회귀 헤드를 축마다 256개 구간을 cross-entropy로 학습하는 토큰 헤드로 바꿨습니다. 여기에
8스텝(1.6 s) 청크 헤드를 더하고 한 스텝씩 실행했습니다. 8스텝을 모두 나는 방식과 탐색 비행
400개(상태 19,896개)를 더하는 방식도 시험했습니다. 모델마다 문장 교체 테스트와 폐루프 비행
20회로 측정했고 Wilson 구간을 붙였습니다.

<h3 class="star-h"><span class="star-tag">R</span>결과</h3>

토큰만으로 성공이 3/20에서 8/20으로 오르고 최종 거리 중앙값이 1.38 m에서 0.22 m로 줄었습니다.
토큰 + 청크는 10쌍 중 4쌍을 맞게 갈라 쌍 성공 3/10(95% 구간 0.11–0.60)을 기록했지만 4편의
0–0.28과 구간이 겹쳐 아직 단서 수준입니다. 탐색 상태를 더하면 교체 반응은 전문가 수준(0.196 대
0.200 m/s)이 되지만 비행 20회 중 8회가 목표를 지나쳐 방 밖으로 나갔고 쌍 성공은 0이 됐습니다.

</div>

</div>
