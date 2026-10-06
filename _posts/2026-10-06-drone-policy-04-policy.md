---
title: "Part 4 · The policy that did not listen"
title_ko: "4편 · 말을 듣지 않은 정책"
title_zh: "第 4 篇 · 不听指令的策略"
date: 2026-10-06 12:00:00 +0900
permalink: /blog/drone-policy/04-policy/
series: drone-policy
part: 4
tags: [drone, behaviour-cloning, evaluation, language-grounding, pytorch]
excerpt: "Part 4 of *Building and Evaluating a Drone VLA from Scratch*. A 481k-parameter RGB + text policy trained on 100 counterfactual pairs learns to fly toward the targets, but not which one: for almost every pair it flies to the same pillar whichever sentence it gets. Why, measured."
excerpt_ko: "연재 4편. counterfactual 100 pair로 학습한 48만 파라미터 RGB+텍스트 정책은 타깃 쪽으로 나는 법은 배우지만 어느 타깃인지는 배우지 못합니다. 거의 모든 pair에서 어떤 문장을 받든 같은 기둥으로 날아갑니다. 그 이유를 측정했습니다."
excerpt_zh: "系列第 4 篇：用 100 对反事实样本训练的 48 万参数 RGB+文本策略学会了飞向目标，却没学会飞向哪一个：几乎每一对中，无论收到哪句指令都飞向同一根柱子。并测量了原因。"
tldr_en:
  - "A small RGB + text policy (481k parameters, CPU, ~8 min per seed) trained on 100 pairs flies toward the pillars, but <b>two sentences led to two different targets in only 0, 2 and 0 of 10 validation pairs</b> across 3 training seeds. Pair success: 0/10 for every seed."
  - "Swapping only the sentence on the identical first frame changes its sideways command by <b>0.004–0.007 m/s</b>; the expert's changes by <b>0.200 m/s</b>."
  - "Why: only the first frame of an episode truly needs the words (2% of training rows). Every later frame shows where the drone is already heading, so the loss is minimised by reading the image and ignoring the text."
tldr_ko:
  - "100 pair로 학습한 작은 RGB+텍스트 정책(48만 파라미터, CPU, seed당 약 8분)은 기둥 쪽으로 날지만, 3개 seed에서 <b>두 문장이 서로 다른 타깃으로 이어진 pair가 검증 10개 중 0, 2, 0개</b>뿐이었습니다. pair 성공은 모든 seed에서 0/10."
  - "같은 첫 프레임에서 문장만 바꾸면 정책의 좌우 명령은 <b>0.004–0.007 m/s</b> 변하고, 전문가는 <b>0.200 m/s</b> 변합니다."
  - "이유: 에피소드에서 말이 정말 필요한 건 첫 프레임뿐입니다(학습 행의 2%). 그다음 프레임은 드론이 이미 향하는 방향을 보여 주므로, 이미지만 읽고 텍스트를 무시해도 loss가 최소가 됩니다."
tldr_zh:
  - "用 100 对数据训练的小型 RGB+文本策略（48 万参数，CPU，每个种子约 8 分钟）会飞向柱子，但在 3 个训练种子中，<b>两句指令飞向不同目标的验证对只有 10 对中的 0、2、0 对</b>。pair 成功率每个种子都是 0/10。"
  - "在完全相同的首帧上只替换指令，它的横向指令只变化 <b>0.004–0.007 m/s</b>，而专家变化 <b>0.200 m/s</b>。"
  - "原因：一个回合中真正需要指令的只有第一帧（占训练行的 2%）。之后每一帧都显示无人机已经朝向哪里，所以只读图像、忽略文本就能使损失最小。"
---

{% include tldr.html %}

<div class="lang-ko pnote" lang="ko" markdown="1">
본문은 영어로 작성되어 있습니다. 그림과 코드는 언어와 상관없이 같습니다.
</div>
<div class="lang-zh pnote" lang="zh-Hans" markdown="1">
正文为英文，图表与代码与语言无关。
</div>

Three parts of setup lead to this one: a simulator, a scored task, and 100 pairs of flights in
which the same image comes with two different sentences. Now the first policy. I expected it to
be bad at flying. I didn't expect it to fly toward *a* pillar and stop there while having no
idea *which* one.

<div class="vgrid">
  <figure class="pvid">
    <img src="/images/blog/drone-policy/common/expert_val_pair0.gif" alt="The scripted expert flying a validation pair to two different targets" loading="lazy">
    <figcaption><span class="pvid__label">Scripted expert</span>Validation pair 0: two sentences, two targets.</figcaption>
  </figure>
  <figure class="pvid">
    <img src="/images/blog/drone-policy/04/bc_v0.2_s0_val_pair0.gif" alt="The learned policy flying the same validation pair: both sentences lead to nearly the same path" loading="lazy">
    <figcaption><span class="pvid__label">TinyBC, seed 0</span>Same pair. Both sentences produce almost the same flight.</figcaption>
  </figure>
</div>

## 1. The model: small on purpose

<figure class="pfig">
  <img src="/images/blog/drone-policy/04/01_architecture.png" alt="Architecture: four strided convolutions turn the 96x128 image into a 6x8x64 map and a 128-number vector; words are embedded and averaged into 32 numbers; the state goes through an MLP to 32; all are concatenated and an MLP outputs four velocities and a Stop logit" loading="lazy">
</figure>

Three inputs, one output, no pretrained parts:

- **Image:** four strided convolutions shrink 96 x 128 x 3 to a 6 x 8 x 64 map, then to 128
  numbers.
- **Sentence:** each word gets a learned 32-number vector, and the vectors are averaged. With
  only 15 distinct words in the dataset, this is the simplest text encoder that can still tell
  "red" from "blue".
- **State:** the 11 numbers from Part 2's contract, normalised with training-set statistics.

They are joined into 192 numbers, and a small MLP outputs four velocities (squashed by `tanh`
and scaled to the limits) plus one Stop logit. Total: **480,901 parameters**. It runs in
**2.7 ms per decision** on the CPU (median), far inside the 200 ms budget (simplified below;
[model code](https://github.com/Ckck12/Drone_VLA_Simulation/blob/b42a22766427e9e4eba6b8e8c79754e63f69f386/dronevla/model.py#L109-L120)):

```python
def forward(self, rgb_u8, proprio, text_vec):
    """rgb_u8 (B, 96, 128, 3) uint8; proprio (B, 11) normalised; text_vec (B, 32)."""
    x = rgb_u8.permute(0, 3, 1, 2).float().div(255.0).sub(0.5)       # to (B, 3, 96, 128)
    feats = torch.cat([self.img_fc(self.cnn(x)),                      # (B, 128)
                       text_vec,                                      # (B, 32)
                       self.prop(proprio)], dim=1)                    # (B, 32)
    out = self.head(feats)                                            # (B, 5)
    return torch.tanh(out[:, :4]), out[:, 4]                          # velocities, Stop logit
```

<figure class="pfig">
  <img src="/images/blog/drone-policy/04/05_action_heads.png" alt="Three columns comparing discrete action tokens (RT-2, OpenVLA), continuous action chunks (SmolVLA) and this project's single continuous step" loading="lazy">
  <figcaption><b>Why no action tokens (yet)?</b> Large VLAs output actions in different ways. RT-2 and OpenVLA cut each action dimension into 256 bins and predict them as tokens; SmolVLA's action expert outputs a chunk of future continuous actions. This tiny policy regresses one continuous step. Season 4 maps this (5,) action onto SmolVLA's format.</figcaption>
</figure>

## 2. Training: behaviour cloning on CPU

Behaviour cloning means copying the expert: for every recorded frame, predict the action the
expert took. Two losses are added together: a Huber loss on the four velocities, and a binary
cross-entropy on Stop, weighted up because only 6.7% of rows are Stop
([full code](https://github.com/Ckck12/Drone_VLA_Simulation/blob/b42a22766427e9e4eba6b8e8c79754e63f69f386/dronevla/train.py#L289-L304)):

```python
huber = nn.SmoothL1Loss(beta=0.1)
bce = nn.BCEWithLogitsLoss(pos_weight=torch.tensor(13.9))   # from training rows only

def train_step(idx):
    rgb, prop, ids, act, stp = prep.batch(train, idx)       # act is scaled by the limits
    m, logit = model(rgb, prop, model.encode_text(ids))
    loss = huber(m, act) + bce(logit, stp)
    opt.zero_grad()
    loss.backward()
    opt.step()
```

Settings: Adam, learning rate 10⁻³, batch 16, 9,952 training rows. Each seed takes 7–8.5
minutes on the laptop CPU.

### The evaluation rules, fixed before running

An earlier round of experiments taught me these rules by breaking them, so this time they were
written down first:

1. **Decisions use validation only.** The 10 test pairs aren't touched, and no number in this
   post comes from them. (Those test seeds were looked at during the earlier, discarded round,
   so final numbers will come from a fresh test split.)
2. **Three training seeds.** With 10 validation pairs, one seed's luck can look like an effect.
3. **One stopping rule for every run:** train 20 epochs and keep the last. Picking the "best"
   epoch by validation loss favoured whichever epoch the Stop term happened to like.
4. **"Which target did it go for" = the one it came closest to.** Scoring by the final position
   misreads flights that end outside the room.

The Stop threshold is the only thing tuned, on validation rows.

## 3. Results: it flies, it doesn't listen

<figure class="pfig">
  <img src="/images/blog/drone-policy/04/03_language_use.png" alt="Three bar charts for three seeds: pairs where the two sentences led to two different targets 0, 2 and 0 out of 10; flew to the named target 10 out of 20 for every seed; success 3, 1 and 2 out of 20" loading="lazy">
  <figcaption>Closed loop on the 20 validation episodes, three training seeds. Left is the headline: in how many of the 10 pairs did the two sentences send the drone to two different targets? Middle: 10/20 is exactly what any policy that ignores the words must score (see below).</figcaption>
</figure>

The left panel is the finding. The expert splits all 10 pairs. The policy split **0, 2 and 0**
of them: for almost every layout it flew to the same pillar whichever sentence it was given.

The middle panel follows from that. Within a pair, each sentence names a different pillar. A
policy that flies to the same pillar for both is right for exactly one of the two sentences,
so any word-blind policy scores **exactly 10 of 20**, as Part 2 predicted. Seeds 0 and 2 are
that case. Seed 1's two split pairs land on 10 too: it split one pair the right way round and
one the wrong way round. And no seed got a single pair fully right (pair success 0/10).

The top view shows it directly:

<figure class="pfig">
  <img src="/images/blog/drone-policy/04/02_closed_loop_topdown.png" alt="Six top-view panels: for three validation pairs the expert's two paths split toward the two targets, while the learned policy's two paths lie on top of each other" loading="lazy">
  <figcaption>Each line's colour is the target its sentence named; solid and dashed are the two sentences. The expert's two lines split. The policy's lie on top of each other.</figcaption>
</figure>

Its most common failures are stopping away from both hover points and stopping at the wrong
pillar (appendix A). It does fly toward the pillars and it does stop. What it lacks is the one
decision the sentence exists for.

## 4. Why: the words matter for one frame

My hypothesis was that the image makes the words unnecessary, and I tested it directly. From
every recorded training state, I asked each trained policy for its action twice: once with the
episode's own sentence, and once with its pair partner's. Then I compared how much the sideways
command changed with how much the expert's changes
([measurement script](https://github.com/Ckck12/Drone_VLA_Simulation/blob/b42a22766427e9e4eba6b8e8c79754e63f69f386/scripts/instruction_sensitivity.py)).

<figure class="pfig">
  <img src="/images/blog/drone-policy/04/04_swap_gap.png" alt="Left: on a log scale, swapping the sentence changes the expert's lateral command by about 0.2 m/s at every step but the policy's by about 0.003 to 0.007 m/s. Right: the policy's training error is about 0.10 m/s at the first step and about 0.01 m/s afterwards" loading="lazy">
  <figcaption>A: swap only the sentence and measure the change in the sideways command (log scale). B: the policy's error against the expert on the training set, by step within the episode, with each bucket's share of the training rows.</figcaption>
</figure>

**A.** On the identical first frame, swapping the sentence moves the expert's sideways command
by 0.200 m/s. It moves the policy's by **0.004–0.007 m/s**, 30 to 50 times less, for all three
seeds. At later steps the gap is just as small. The policy has nearly disconnected the words.
(The expert's later-step bars are computed actions: from each logged state, what the expert would
do for the *other* sentence. Those states never occur with the other sentence in the data,
which is why its gap grows later in the episode.)

**B.** On the training set, the policy matches the expert to about 0.01 m/s on every step except
the first, where its error is **0.10 m/s**. That's half of the 0.2 m/s gap between the two
sentences' labels, consistent with predicting the *average* of the two.

Put together, the mechanism is simple:

- On the **first frame**, both sentences share one image, and only the words say left or right.
  That frame is **2% of the training rows**.
- On **every later frame**, the drone has already started turning. The image itself shows where
  it is heading, so the image predicts the label. That's 98% of the rows.
- Behaviour cloning minimises the average loss over all rows. The cheapest solution is to read
  the image and average over the 2% it can't explain. In closed loop, the first move is then
  made without the words, and every frame after it confirms whichever way the drone happened to
  lean.

That is why the counterfactual pairs exist. Without them, this policy's success rate could have
looked like a language effect; with them, the word-blindness is visible.

## What this part does not show

- 10 validation pairs is a small sample; a policy that listened only a little could hide in it.
- One small architecture and one way of encoding words. A bigger text pathway could behave
  differently; that is a question for the next season, not a result here.
- Simulation only, two pillars, two sentence templates.

## Next

Season 2 asks whether the shortcut can be removed. One idea is to give the words a reason to
matter on every frame. Another is to split the problem in two: first perceive where every target
is, then choose the one the sentence names.

## Appendix

<details>
<summary>A. Outcomes per seed (validation, 20 episodes each)</summary>

<table>
<thead><tr><th>seed</th><th>success</th><th>stop not settled</th><th>wrong target</th><th>stop elsewhere</th><th>collision</th><th>out of bounds</th></tr></thead>
<tbody>
<tr><td>0</td><td>3</td><td>2</td><td>5</td><td>8</td><td>2</td><td>0</td></tr>
<tr><td>1</td><td>1</td><td>1</td><td>3</td><td>3</td><td>4</td><td>8</td></tr>
<tr><td>2</td><td>2</td><td>4</td><td>5</td><td>5</td><td>4</td><td>0</td></tr>
</tbody>
</table>

<p>With 20 training layouts instead of 100 (the first version of the dataset, same rules, same
seeds), the policies left the room in 31 of 60 episodes instead of 8, and the two sentences
still led to different targets in only 1, 2 and 1 of 10 pairs. More scenes improved the flying,
not the listening.</p>
</details>

<details>
<summary>B. Reproduce</summary>

<p>Commit <a href="https://github.com/Ckck12/Drone_VLA_Simulation/tree/b42a22766427e9e4eba6b8e8c79754e63f69f386"><code>b42a227</code></a>, dataset v0.2 from Part 3.</p>

<pre><code>for s in 0 1 2; do
  python -m dronevla.train --data data/v0.2 --epochs 20 --select last --seed $s --out runs/bc_v0.2_s$s
done
python -m dronevla.evaluate --data data/v0.2 --split val \
    --policy runs/bc_v0.2_s0 --policy runs/bc_v0.2_s1 --policy runs/bc_v0.2_s2 \
    --out reports/eval_v0.2_val.json
python scripts/language_use.py reports/eval_v0.2_val.json
python scripts/instruction_sensitivity.py --data data/v0.2 --split train \
    --runs runs/bc_v0.2_s0 runs/bc_v0.2_s1 runs/bc_v0.2_s2 \
    --out reports/instruction_sensitivity_v0.2_train.json
bash scripts/figures/render_blog04_videos.sh
python scripts/figures/blog02_04.py
</code></pre>
</details>

### References: what I took from each

- [RT-2 (Google DeepMind)](https://deepmind.google/blog/rt-2-new-model-translates-vision-and-language-into-action/)
  represents actions as text tokens inside a vision-language model. → That's one end of the
  action-output spectrum; this policy is the opposite end.
- [SmolVLA (Hugging Face)](https://huggingface.co/blog/smolvla) pairs a small VLM with a
  flow-matching action expert that outputs action chunks. → The target for Season 4, and why
  the (5,) contract will need a mapping step.
- [A Recipe for Training Neural Networks (Karpathy)](https://karpathy.github.io/2019/04/25/recipe/)
  says to become one with the data and verify the evaluation before trusting a model. → Pair
  success and the swap test are how this project checks the policy instead of trusting the
  loss.
