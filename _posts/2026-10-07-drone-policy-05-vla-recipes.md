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
excerpt_ko: "연재 5편. 같은 작은 정책, 같은 데이터에서 행동을 출력하는 방식만 바꿨습니다. OpenVLA식 액션 토큰은 비행을 고치고, 8스텝 액션 청크는 지금까지 가장 나은 end-to-end 모델(pair 성공 3/10)이 됩니다. 상태를 더 주면 말에는 반응하지만 멈추지를 못합니다."
excerpt_zh: "系列第 5 篇：同样的小策略、同样的数据，只改变输出动作的方式。OpenVLA 式动作 token 修好了飞行，8 步动作块成为目前最好的端到端模型（pair 成功 3/10）；再给它更多状态，它会对指令作出反应，却停不下来。"
tldr_en:
  - "Only the action head and its loss changed; no pretrained model. <b>256-bin action tokens</b> (as in OpenVLA) raised success from 3/20 to 8/20 and cut the median final distance from 1.38 m to 0.22 m."
  - "Why: on the frame where only the words decide, regression answers <b>between</b> the two sentences' answers. A token head puts its probability mostly on <b>one</b> of them, although not always the right one (right in 135 of 200 training frames)."
  - "An <b>8-step action chunk</b>, executed one step at a time, is the best end-to-end model so far: 4 of 10 validation pairs split to the right targets, pair success <b>3/10</b> (95% interval 0.11–0.60, one training seed). Adding exploration states makes it react to the words like the expert on training frames, but in flight 8 of 20 runs overshoot and leave the room."
tldr_ko:
  - "바뀐 것은 행동 출력 헤드와 그 loss뿐이고, 사전학습 모델은 없습니다. <b>256-bin 액션 토큰</b>(OpenVLA 방식)으로 성공이 3/20에서 8/20으로 오르고, 최종 거리 중앙값이 1.38 m에서 0.22 m로 줄었습니다."
  - "이유: 말만이 방향을 정하는 프레임에서 회귀는 두 문장의 정답 <b>사이</b>를 답합니다. 토큰 헤드는 확률을 대부분 둘 중 <b>하나</b>에 겁니다. 다만 늘 맞는 쪽은 아닙니다(학습 프레임 200개 중 135개에서 맞는 쪽)."
  - "한 스텝씩 실행하는 <b>8스텝 액션 청크</b>가 지금까지 가장 나은 end-to-end 모델입니다: 검증 10 pair 중 4개가 맞는 타깃으로 갈라졌고, pair 성공 <b>3/10</b>(95% 구간 0.11–0.60, 학습 seed 1개). 탐색 상태를 더하면 학습 프레임에서는 전문가만큼 말에 반응하지만, 실제 비행에서는 20번 중 8번이 목표를 지나쳐 방 밖으로 나갑니다."
tldr_zh:
  - "只改变了动作输出头和它的损失，没有预训练模型。<b>256 档动作 token</b>（OpenVLA 的做法）使成功率从 3/20 升至 8/20，最终距离中位数从 1.38 m 降到 0.22 m。"
  - "原因：在只有指令能决定方向的那一帧，回归给出两句指令答案<b>之间</b>的值；token 头则把概率主要放在其中<b>一个</b>上，但不总是对的那个（200 个训练帧中 135 个是对的）。"
  - "逐步执行的 <b>8 步动作块</b>是目前最好的端到端模型：10 个验证对中 4 个分别飞向正确目标，pair 成功 <b>3/10</b>（95% 区间 0.11–0.60，单个训练种子）。加入探索状态后，它在训练帧上对指令的反应达到专家水平，但实际飞行中 20 次有 8 次冲过目标飞出房间。"
---

{% include tldr.html %}

<div class="lang-ko pnote" lang="ko" markdown="1">
본문은 영어로 작성되어 있습니다. 그림과 코드는 언어와 상관없이 같습니다.
</div>
<div class="lang-zh pnote" lang="zh-Hans" markdown="1">
正文为英文，图表与代码与语言无关。
</div>

Part 4 ended with a diagnosis. The words matter on one frame per episode, the first, which is
2% of the training rows. On that frame the policy answered with the *average* of the two
sentences' answers. On every other frame the image already showed where to go.

Large vision-language-action models (VLAs) don't output actions the way that policy did. RT-2
and OpenVLA turn each action dimension into one of 256 **tokens** and train with cross-entropy.
OpenVLA-OFT and ACT predict a **chunk** of several future actions at once. This post borrows
those two output formats and nothing else. The image encoder, the sentence encoder, the data and
the parameter budget stay as they were. There is still no pretrained model; that arrives in
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
- **Validation only**: 10 pairs, 20 flights. The test split is untouched.
- **Same training recipe as Part 4**: 20 epochs, keep the last, dataset v0.2 (100 training
  pairs).
- **Same size**: every model has 80–100% of Part 4's 480,901 parameters.

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

A token head can't place its answer between two bins it was never shown. It has to put its
probability *somewhere*, and it mostly puts it on one expert answer. It picks the right one in
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

- **Tokens alone don't make the drone choose.** With 256 bins, 7 of 10 pairs still go to the
  same pillar for both sentences. The token head commits, but on most first frames it commits
  to whatever the image suggests.
- **Tokens + an 8-step chunk is the best end-to-end model**: 4 pairs split the right way, and
  pair success is **3/10**. One plausible reason: predicting 1.6 s ahead forces the
  representation to encode where the flight is going, not just the next step. Its 95% interval
  is 0.11–0.60, which overlaps Part 4's 0/10 (0–0.28). With one seed and ten pairs this is a
  lead, not a proven gain.
- **Flying all 8 predicted steps before asking again** flies about as well (9/20) but listens
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
direction in 180 of 200 frames. In closed loop it splits 5 of 10 pairs the right way, tying the
best end-to-end result. But **8 of 20 flights overshoot the target and leave the room**, success falls to 6, and
pair success is 0.

I didn't separate the causes. Three candidates:

- **Stop is diluted.** The exploration rows contain only 6 Stop examples, so Stop becomes a
  rarer event in the training mix.
- **The bins got coarser.** The bin range comes from the training speeds, and exploration
  widens the vy range from about ±0.17 to ±0.42 m/s. Each bin is 2.4 times wider, which is
  coarse near the goal.
- **Recovery labels near the goal.** Overshoot flights teach large corrections exactly where
  the expert usually slows down.

The experiment changes what the policy reacts to without changing how it stops. That split,
choosing versus flying, is what Part 6 builds in on purpose.

## What this part does not show

- **One training seed and ten pairs.** Wilson 95% for 3/10 is 0.11–0.60. Single-pair
  differences, and the bin-count comparison in the appendix, are within noise.
- **Validation was used both to screen models and to set the Stop threshold**, so these are not
  generalisation estimates. The original test seeds were seen in an earlier round; final
  numbers need a fresh test split.
- **The swap test runs on training frames.** Near-zero token errors there may partly be
  memorisation.
- **The language is easy.** The two pillars always differ in colour, so one word decides.
- **It is not a VLA.** Only the output format is borrowed; there is no pretrained vision or
  language model.

## Next

The best end-to-end model splits 4 of 10 pairs. Part 6 tries the opposite design: a model that
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
<li><b>Counterfactual relabelling</b> adds, for every training state, the expert's action toward the <i>other</i> target with the other sentence. The words start to matter, but correct and swapped pairs are about equal, and flying collapses. Part of that is a confound: the added rows contain no Stop positives, while the Stop loss weight stayed at its original value.</li>
<li><b>Aux head</b>: an extra output trained to predict where every colour's target is, from the simulator's true positions (the same labels Part 6's world model gets). It flies better (8/20) and listens no more.</li>
<li><b>FiLM and cross-attention</b> let the sentence modulate the image features (FiLM scales and shifts the convolution channels; cross-attention uses the sentence as a query over 6 × 8 image patches). The two FiLM rows predate the 80–100% parameter rule (FiLM 102%, aux 103%).</li>
<li><b>Bin count</b>: 64, 256 and 1024 bins are within one seed's noise of each other.</li>
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
