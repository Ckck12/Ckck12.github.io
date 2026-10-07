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
excerpt_ko: "연재 4편. counterfactual 100쌍으로 학습한 48만 파라미터 RGB+텍스트 정책은 타깃 쪽으로 나는 법은 배우지만 어느 타깃인지는 배우지 못합니다. 거의 모든 쌍에서 어떤 문장을 받든 같은 기둥으로 날아갑니다. 그 이유를 측정했습니다."
excerpt_zh: "系列第 4 篇：用 100 对反事实样本训练的 48 万参数 RGB+文本策略学会了飞向目标，却没学会飞向哪一个：几乎每一对中，无论收到哪句指令都飞向同一根柱子。并测量了原因。"
tldr_en:
  - "A small RGB + text policy (481k parameters, CPU, ~8 min per seed) trained on 100 pairs flies toward the pillars, but <b>two sentences led to two different targets in only 0, 2 and 0 of 10 validation pairs</b> across 3 training seeds. Pair success: 0/10 for every seed."
  - "Swapping only the sentence on the identical first frame changes its sideways command by <b>0.004–0.007 m/s</b>; the expert's changes by <b>0.200 m/s</b>."
  - "Why: only the first frame of an episode truly needs the words (2% of training rows). Every later frame shows where the drone is already heading, so the loss is minimised by reading the image and ignoring the text."
tldr_ko:
  - "100쌍으로 학습한 작은 RGB+텍스트 정책(48만 파라미터, CPU, 시드당 약 8분)은 기둥 쪽으로 날지만 3개 시드에서 <b>두 문장이 서로 다른 타깃으로 이어진 쌍이 검증 10개 중 0, 2, 0개</b>뿐이었습니다. 쌍 성공은 모든 시드에서 0/10."
  - "같은 첫 프레임에서 문장만 바꾸면 정책의 좌우 명령은 <b>0.004–0.007 m/s</b> 변하고 전문가는 <b>0.200 m/s</b> 변합니다."
  - "이유: 에피소드에서 말이 정말 필요한 건 첫 프레임뿐입니다(학습 행의 2%). 그다음 프레임은 드론이 이미 향하는 방향을 보여 주므로 이미지만 읽고 텍스트를 무시해도 손실이 최소가 됩니다."
tldr_zh:
  - "用 100 对数据训练的小型 RGB+文本策略（48 万参数，CPU，每个种子约 8 分钟）会飞向柱子，但在 3 个训练种子中，<b>两句指令飞向不同目标的验证对只有 10 对中的 0、2、0 对</b>。pair 成功率每个种子都是 0/10。"
  - "在完全相同的首帧上只替换指令，它的横向指令只变化 <b>0.004–0.007 m/s</b>，而专家变化 <b>0.200 m/s</b>。"
  - "原因：一个回合中真正需要指令的只有第一帧（占训练行的 2%）。之后每一帧都显示无人机已经朝向哪里，所以只读图像、忽略文本就能使损失最小。"
---

{% include tldr.html %}

<div class="lang-zh pnote" lang="zh-Hans" markdown="1">

正文为英文，图表与代码与语言无关。

</div>

<div class="lang-enzh" markdown="1">

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

- Image: four strided convolutions shrink 96 x 128 x 3 to a 6 x 8 x 64 map, then to 128
  numbers.
- Sentence: each word gets a learned 32-number vector, and the vectors are averaged. With
  only 15 distinct words in the dataset, this is the simplest text encoder that can still tell
  "red" from "blue".
- State: the 11 numbers from Part 2's contract, normalised with training-set statistics.

They are joined into 192 numbers, and a small MLP outputs four velocities (squashed by `tanh`
and scaled to the limits) plus one Stop logit. Total: **480,901 parameters**. It runs in
2.7 ms per decision on the CPU (median), far inside the 200 ms budget (simplified below;
[model code](https://github.com/Ckck12/Drone_VLA_Simulation/blob/49a7f1ad0e1f5c205f1689792d17086c566c2e10/dronevla/model.py#L109-L120)):

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
([full code](https://github.com/Ckck12/Drone_VLA_Simulation/blob/49a7f1ad0e1f5c205f1689792d17086c566c2e10/dronevla/train.py#L289-L304)):

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

1. Decisions use validation only. The 10 test pairs aren't touched, and no number in this
   post comes from them. (Those test seeds were looked at during the earlier, discarded round,
   so final numbers will come from a fresh test split.)
2. Three training seeds. With 10 validation pairs, one seed's luck can look like an effect.
3. One stopping rule for every run: train 20 epochs and keep the last. Picking the "best"
   epoch by validation loss favoured whichever epoch the Stop term happened to like.
4. "Which target did it go for" = the one it came closest to. Scoring by the final position
   misreads flights that end outside the room.

The Stop threshold is the only thing tuned, on validation rows.

## 3. Results: it flies, it doesn't listen

<figure class="pfig">
  <img src="/images/blog/drone-policy/04/03_language_use.png" alt="Three bar charts for three seeds: pairs where the two sentences led to two different targets 0, 2 and 0 out of 10; flew to the named target 10 out of 20 for every seed; success 3, 1 and 2 out of 20" loading="lazy">
  <figcaption>Closed loop on the 20 validation episodes, three training seeds. Left is the headline: in how many of the 10 pairs did the two sentences send the drone to two different targets? Middle: 10/20 is exactly what any policy that ignores the words must score (see below).</figcaption>
</figure>

The expert splits all 10 pairs. <span class="key-line">The policy split **0, 2 and 0**
of them: for almost every layout it flew to the same pillar whichever sentence it was given.</span>

The middle panel follows from that. Within a pair, each sentence names a different pillar. A
policy that flies to the same pillar for both is right for exactly one of the two sentences,
so any word-blind policy scores **exactly 10 of 20**, as Part 2 predicted. Seeds 0 and 2 are
that case. Seed 1's two split pairs land on 10 too: it split one pair the right way round and
one the wrong way round. And no seed completed both sentences of any pair: pair success was
0/10 for all three seeds.

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
([measurement script](https://github.com/Ckck12/Drone_VLA_Simulation/blob/49a7f1ad0e1f5c205f1689792d17086c566c2e10/scripts/instruction_sensitivity.py)).

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

The mechanism is simple:

- On the first frame, both sentences share one image, and only the words say left or right.
  That frame is **2% of the training rows**.
- On every later frame, the drone has already started moving to one side (its heading never
  changes; only its position does). The image itself shows which way it is going, so the image
  predicts the label. That's 98% of the rows.
- Behaviour cloning minimises the average loss over all rows. <span class="key-line">The cheapest solution is to read
  the image and average over the 2% it can't explain.</span> In closed loop, the first move is then
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

<p>Commit <a href="https://github.com/Ckck12/Drone_VLA_Simulation/tree/49a7f1ad0e1f5c205f1689792d17086c566c2e10"><code>49a7f1a</code></a>, dataset v0.2 from Part 3.</p>

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

## Summary (STAR)

<div class="star-summary" markdown="1">

<h3 class="star-h"><span class="star-tag">S</span>Situation</h3>

Three earlier parts built a simulator, a scored task and 100 counterfactual pairs of flights, in
which the same image comes with two sentences that name different pillars.

<h3 class="star-h"><span class="star-tag">T</span>Task</h3>

Train a first RGB + text policy by behaviour cloning, and check on 10 validation pairs and three
training seeds whether the sentence decides which pillar it flies to.

<h3 class="star-h"><span class="star-tag">A</span>Action</h3>

I trained a 480,901-parameter policy with no pretrained parts on the laptop CPU (20 epochs, last
epoch kept, 7–8.5 minutes per seed), under evaluation rules written down before the runs. I scored
it in closed loop, then swapped in the pair partner's sentence on every training state and compared
the change in its sideways command with the expert's.

<h3 class="star-h"><span class="star-tag">R</span>Result</h3>

The policy flies toward the pillars, but the two sentences sent it to different targets in only
0, 2 and 0 of 10 pairs, it flew to the named target in exactly 10 of 20 episodes for every seed,
and pair success was 0/10 for all three seeds. On the first frame, swapping the sentence moved its
sideways command by 0.004–0.007 m/s against the expert's 0.200 m/s: only that frame (2% of the
training rows) needs the words, so behaviour cloning learned to read the image instead.

</div>

</div>

<div class="lang-ko" lang="ko" markdown="1">

앞의 세 편에서 준비한 것은 시뮬레이터, 점수를 매길 수 있는 과제, 그리고 같은 이미지에 서로 다른
두 문장이 붙은 비행 쌍(pair) 100개입니다. 이번 편에서는 드디어 첫 정책을 학습합니다. 비행이 서툴
거라고는 예상했습니다. 그런데 기둥 *하나*를 향해 날아가 거기서 멈추면서도 그게 *어느* 기둥인지는
전혀 모를 줄은 몰랐습니다.

<div class="vgrid">
  <figure class="pvid">
    <img src="/images/blog/drone-policy/common/expert_val_pair0.gif" alt="스크립트 전문가가 검증 쌍 하나를 서로 다른 두 타깃으로 비행하는 모습" loading="lazy">
    <figcaption><span class="pvid__label">스크립트 전문가</span>검증 쌍 0: 두 문장, 두 타깃.</figcaption>
  </figure>
  <figure class="pvid">
    <img src="/images/blog/drone-policy/04/bc_v0.2_s0_val_pair0.gif" alt="학습된 정책이 같은 검증 쌍을 비행하는 모습. 두 문장 모두 거의 같은 경로로 이어집니다" loading="lazy">
    <figcaption><span class="pvid__label">TinyBC, 시드 0</span>같은 쌍입니다. 두 문장이 거의 같은 비행을 만듭니다.</figcaption>
  </figure>
</div>

## 1. 모델: 일부러 작게

<figure class="pfig">
  <img src="/images/blog/drone-policy/04/01_architecture.png" alt="구조도. 스트라이드 합성곱 네 개가 96x128 이미지를 6x8x64 맵으로 바꾼 뒤 숫자 128개짜리 벡터로 만듭니다. 단어는 임베딩한 다음 평균을 내 숫자 32개로 만듭니다. 상태는 MLP를 거쳐 32개가 됩니다. 셋을 이어 붙이면 MLP가 속도 네 개와 Stop 로짓을 출력합니다" loading="lazy">
</figure>

입력은 세 가지, 출력은 하나이고 사전 학습된 부분은 없습니다.

- 이미지: 스트라이드 합성곱 네 개가 96 x 128 x 3을 6 x 8 x 64 맵으로 줄이고 이를 다시 숫자
  128개로 만듭니다.
- 문장: 단어마다 학습되는 숫자 32개짜리 벡터를 두고 그 벡터들의 평균을 냅니다. 데이터셋에 있는
  서로 다른 단어가 15개뿐이니 "red"와 "blue"를 구별할 수 있는 텍스트 인코더로는 이게 가장
  단순합니다.
- 상태: 2편에서 정한 계약의 숫자 11개입니다. 학습 세트 통계로 정규화합니다.

셋을 이어 붙이면 숫자 192개가 됩니다. 작은 MLP가 여기서 속도 네 개(`tanh`로 누른 뒤 한계값에
맞춰 스케일)와 Stop 로짓 하나를 출력합니다. 전체 파라미터는 **480,901개**입니다. CPU에서 결정
한 번에 2.7 ms(중앙값)가 걸리니 200 ms 예산보다 한참 여유가 있습니다. 아래는 단순화한 코드입니다
([모델 코드](https://github.com/Ckck12/Drone_VLA_Simulation/blob/49a7f1ad0e1f5c205f1689792d17086c566c2e10/dronevla/model.py#L109-L120)).

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
  <img src="/images/blog/drone-policy/04/05_action_heads.png" alt="이산 액션 토큰(RT-2, OpenVLA), 연속 액션 청크(SmolVLA), 이 프로젝트의 단일 연속 스텝을 나란히 비교한 세 열" loading="lazy">
  <figcaption><b>왜 (아직) 액션 토큰을 쓰지 않을까요?</b> 큰 VLA는 액션을 내놓는 방식이 저마다 다릅니다. RT-2와 OpenVLA는 액션의 각 차원을 256개 구간(bin)으로 잘라 토큰으로 예측합니다. SmolVLA의 action expert는 미래의 연속 액션 여러 개를 묶은 액션 청크를 출력합니다. 이 작은 정책은 연속 스텝 하나를 회귀합니다. 시즌 4에서 이 (5,) 액션을 SmolVLA의 형식에 맞춰 옮깁니다.</figcaption>
</figure>

## 2. 학습: CPU에서 하는 행동 복제

행동 복제(behaviour cloning, BC)는 전문가를 그대로 따라 하는 방법입니다. 기록된 프레임마다
전문가가 그때 낸 액션을 예측합니다. 손실은 두 가지를 더합니다. 속도 네 개에는 Huber 손실을
쓰고 Stop에는 이진 교차 엔트로피를 쓰는데 Stop인 행이 6.7%밖에 안 되어서 가중치를 높였습니다
([전체 코드](https://github.com/Ckck12/Drone_VLA_Simulation/blob/49a7f1ad0e1f5c205f1689792d17086c566c2e10/dronevla/train.py#L289-L304)).

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

설정은 Adam, 학습률 10⁻³, 배치 16, 학습 행 9,952개입니다. 노트북 CPU로 시드 하나에 7–8.5분이
걸립니다.

### 실행 전에 고정한 평가 규칙

이전 실험 라운드에서 이 규칙들을 어겨 가며 배웠기 때문에 이번에는 먼저 적어 두고 시작했습니다.

1. 결정은 검증 분할로만 내립니다. 테스트 쌍 10개는 건드리지 않았고 이 글의 숫자 중 테스트에서
   나온 것은 없습니다. (그 테스트 시드들은 폐기한 이전 라운드에서 이미 들여다봤기 때문에 최종
   숫자는 새 테스트 분할에서 낼 계획입니다.)
2. 학습 시드는 세 개입니다. 검증 쌍이 10개뿐이면 시드 하나의 운이 효과처럼 보일 수 있습니다.
3. 모든 실행에 같은 중단 규칙을 씁니다. 20 에폭을 학습하고 마지막 에폭을 남깁니다. 검증 손실로
   "최고" 에폭을 고르면 Stop 항이 우연히 좋게 나온 에폭이 뽑혔습니다.
4. "어느 타깃으로 갔는가"는 가장 가까이 다가간 타깃으로 판정합니다. 마지막 위치로 채점하면 방
   밖에서 끝난 비행을 잘못 읽게 됩니다.

검증 행에서 튜닝한 값은 Stop 임계값 하나입니다.

## 3. 결과: 날기는 하지만 말은 듣지 않는 정책

<figure class="pfig">
  <img src="/images/blog/drone-policy/04/03_language_use.png" alt="시드 세 개의 막대그래프 세 개. 두 문장이 서로 다른 타깃으로 이어진 쌍은 10개 중 0, 2, 0개입니다. 지시된 타깃으로 비행한 경우는 모든 시드에서 20개 중 10개입니다. 성공은 20개 중 3, 1, 2개입니다" loading="lazy">
  <figcaption>검증 에피소드 20개를 학습 시드 세 개로 돌린 폐루프 결과입니다. 왼쪽이 핵심입니다. 쌍 10개 중 몇 개에서 두 문장이 드론을 서로 다른 타깃으로 보냈는지 셉니다. 가운데의 10/20은 말을 무시하는 정책이라면 어떤 정책이든 정확히 받게 되는 점수입니다(아래 참고).</figcaption>
</figure>

전문가는 쌍 10개를 모두 서로 다른 타깃으로 갈라 냅니다.
<span class="key-line">정책은 그중 **0, 2, 0**개만 갈랐고 거의 모든 레이아웃에서 어떤 문장을 받든 같은 기둥으로 날아갔습니다.</span>

가운데 패널은 여기서 곧바로 따라 나옵니다. 한 쌍 안에서 두 문장은 서로 다른 기둥을 가리킵니다.
두 문장 모두에 같은 기둥으로 가는 정책은 둘 중 정확히 한 문장에서만 맞습니다. 그래서 말을
무시하는 정책은 2편에서 예측한 대로 어떤 것이든 **정확히 20개 중 10개**를 받습니다. 시드 0과 2가
여기에 해당합니다. 시드 1에서는 두 쌍의 경로가 갈라졌지만 점수는 역시 20개 중 10개였습니다. 한 쌍은 맞는 방향으로,
다른 한 쌍은 반대 방향으로 갈랐기 때문입니다. 한 쌍의 두 문장을 모두 완수한 시드는 하나도
없었습니다. 쌍 성공(pair success)은 세 시드 모두 0/10이었습니다.

위에서 내려다본 궤적을 보면 바로 드러납니다.

<figure class="pfig">
  <img src="/images/blog/drone-policy/04/02_closed_loop_topdown.png" alt="위에서 본 패널 여섯 개. 검증 쌍 세 개에서 전문가의 두 경로는 두 타깃 쪽으로 갈라지지만 학습된 정책의 두 경로는 서로 겹칩니다" loading="lazy">
  <figcaption>선 색은 그 문장이 가리킨 타깃이고 실선과 점선이 두 문장입니다. 전문가의 두 선은 갈라집니다. 정책의 두 선은 서로 포개집니다.</figcaption>
</figure>

가장 흔한 실패는 두 호버 지점 어디와도 떨어진 곳에서 멈추는 것과 엉뚱한 기둥에서 멈추는
것입니다(부록 A). 정책은 기둥 쪽으로 날아가기도 하고 멈추기도 합니다. 그런데 문장이 맡은 단
하나의 결정은 해내지 못합니다.

## 4. 이유: 말이 필요한 건 한 프레임뿐

저는 이미지 때문에 말이 필요 없어진다고 가정하고 이를 직접 확인했습니다. 기록된 학습 상태마다
학습된 정책 각각에 액션을 두 번 물었습니다. 한 번은 그 에피소드의 원래 문장으로, 한 번은 쌍을
이루는 상대 에피소드의 문장으로 물었습니다. 그다음 좌우 명령이 얼마나 바뀌는지 전문가의
변화량과 비교했습니다([측정 스크립트](https://github.com/Ckck12/Drone_VLA_Simulation/blob/49a7f1ad0e1f5c205f1689792d17086c566c2e10/scripts/instruction_sensitivity.py)).

<figure class="pfig">
  <img src="/images/blog/drone-policy/04/04_swap_gap.png" alt="왼쪽: 로그 스케일에서 문장을 바꾸면 전문가의 좌우 명령은 모든 스텝에서 약 0.2 m/s 바뀌지만 정책의 명령은 약 0.003에서 0.007 m/s만 바뀝니다. 오른쪽: 정책의 학습 오차는 첫 스텝에서 약 0.10 m/s이고 이후에는 약 0.01 m/s입니다" loading="lazy">
  <figcaption>A: 문장만 바꾸고 좌우 명령이 얼마나 변하는지 잽니다(로그 스케일). B: 학습 세트에서 정책과 전문가 사이의 오차를 에피소드 안의 스텝별로 나눴고 각 묶음이 학습 행에서 차지하는 비율도 함께 적었습니다.</figcaption>
</figure>

**A.** 똑같은 첫 프레임에서 문장을 바꾸면 전문가의 좌우 명령은 0.200 m/s 움직입니다. 정책의
명령은 세 시드 모두 **0.004–0.007 m/s**만 움직여 30~50배 작습니다. 이후 스텝에서도 차이는
그만큼 작습니다. 정책은 말과의 연결을 거의 끊어 버렸습니다. (이후 스텝의 전문가 막대는 계산으로
구한 액션입니다. 기록된 각 상태에서 전문가가 *다른* 문장을 받았다면 냈을 액션을 구했습니다.
데이터에는 그 상태들이 다른 문장과 함께 나온 적이 없습니다. 그래서 에피소드 후반으로 갈수록 전문가
쪽 차이가 커집니다.)

**B.** 학습 세트에서 첫 스텝을 제외한 모든 스텝의 정책과 전문가 간 오차는 약 0.01 m/s입니다.
첫 스텝에서만 오차가 **0.10 m/s**입니다. 두 문장의 라벨이 0.2 m/s 차이 나니 그 절반이고 정책이
두 라벨의 *평균*을 예측한다고 보면 딱 맞는 값입니다.

메커니즘은 단순합니다.

- 첫 프레임에서는 두 문장이 같은 이미지를 공유하므로 왼쪽인지 오른쪽인지는 말만 알려 줍니다. 이
  프레임은 **학습 행의 2%**입니다.
- 그 뒤의 모든 프레임에서 드론은 이미 한쪽으로 움직이기 시작한 상태입니다(기수 방향은 그대로이고
  위치만 바뀝니다). 어느 쪽으로 가고 있는지 이미지에 그대로 보이니 이미지만으로 라벨을 예측할 수
  있습니다. 이런 행이 98%입니다.
- BC는 모든 행의 평균 손실을 최소화합니다. <span class="key-line">평균 손실을 가장 쉽게 줄이는 방법은 이미지로 행동을 예측하고 이미지로 구별할 수 없는 2%에서는 두 라벨의 평균을 내는 것입니다.</span>
  그러면 폐루프에서 첫 움직임은 말 없이 정해지고 그 뒤의 모든 프레임은 드론이 우연히 기운
  방향을 그대로 굳힙니다.

counterfactual 쌍을 만든 것도 이 때문입니다. 쌍이 없었다면 이 정책의 성공률은 언어 효과처럼
보였을 수도 있습니다. 쌍이 있으니 정책이 말을 무시한다는 사실이 그대로 드러납니다.

## 이번 편이 보여 주지 않는 것

- 검증 쌍 10개는 작은 표본입니다. 말을 아주 조금만 듣는 정책이라면 이 안에 묻힐 수 있습니다.
- 작은 구조 하나와 단어 인코딩 방식 하나만 봤습니다. 텍스트 경로가 더 크면 다르게 동작할 수도
  있습니다. 그건 다음 시즌에 다룰 질문이고 이번 편의 결과로 말할 수 있는 부분은 아닙니다.
- 시뮬레이션에서만, 기둥 두 개와 문장 템플릿 두 개로 실험했습니다.

## 다음 편

시즌 2에서는 이 지름길(shortcut)을 없앨 수 있는지 살펴봅니다. 한 가지 방법은 모든 프레임에서
말이 쓸모 있도록 만드는 것입니다. 다른 하나는 문제를 둘로 나누는 방법입니다. 먼저 모든 타깃이
어디 있는지 인지하고 그다음 문장이 가리키는 타깃을 고릅니다.

## 부록

<details>
<summary>A. 시드별 결과 (검증, 시드마다 에피소드 20개)</summary>

<table>
<thead><tr><th>시드</th><th>성공</th><th>안정 전 Stop</th><th>엉뚱한 타깃</th><th>다른 곳에서 Stop</th><th>충돌</th><th>범위 이탈</th></tr></thead>
<tbody>
<tr><td>0</td><td>3</td><td>2</td><td>5</td><td>8</td><td>2</td><td>0</td></tr>
<tr><td>1</td><td>1</td><td>1</td><td>3</td><td>3</td><td>4</td><td>8</td></tr>
<tr><td>2</td><td>2</td><td>4</td><td>5</td><td>5</td><td>4</td><td>0</td></tr>
</tbody>
</table>

<p>학습 레이아웃을 100개 대신 20개로 쓰면(데이터셋의 첫 버전, 같은 규칙, 같은 시드) 정책은 에피소드
60개 중 31개에서 방을 벗어났습니다. 100개일 때는 8개였습니다. 두 문장이 서로 다른 타깃으로 이어진
쌍도 여전히 10개 중 1, 2, 1개뿐이었습니다. 장면을 늘리자 비행은 나아졌지만 말을 듣는 능력은
그대로였습니다.</p>
</details>

<details>
<summary>B. 재현 방법</summary>

<p>커밋 <a href="https://github.com/Ckck12/Drone_VLA_Simulation/tree/49a7f1ad0e1f5c205f1689792d17086c566c2e10"><code>49a7f1a</code></a>, 3편의 데이터셋 v0.2.</p>

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

### 참고 자료: 각 자료에서 가져온 것

- [RT-2 (Google DeepMind)](https://deepmind.google/blog/rt-2-new-model-translates-vision-and-language-into-action/)는
  비전-언어 모델 안에서 액션을 텍스트 토큰으로 표현합니다. → 액션 출력 방식의 한쪽 끝이고 이
  정책은 그 정반대 끝에 있습니다.
- [SmolVLA (Hugging Face)](https://huggingface.co/blog/smolvla)는 작은 VLM에 액션 청크를 출력하는
  flow-matching action expert를 붙입니다. → 시즌 4의 목표입니다. 그래서 (5,) 계약을 그 형식으로
  옮기는 변환 단계가 필요합니다.
- [A Recipe for Training Neural Networks (Karpathy)](https://karpathy.github.io/2019/04/25/recipe/)는
  데이터와 하나가 되고 모델을 믿기 전에 평가부터 검증하라고 말합니다. → 이 프로젝트는 손실을 믿는
  대신 쌍 성공과 문장 교체 실험(swap test)으로 정책을 직접 확인합니다.

## 요약 (STAR)

<div class="star-summary" markdown="1">

<h3 class="star-h"><span class="star-tag">S</span>상황</h3>

앞의 세 편에서 시뮬레이터, 점수를 매길 수 있는 과제, 그리고 counterfactual 비행 100쌍을
만들었습니다. 각 쌍에서는 같은 이미지에 서로 다른 기둥을 가리키는 두 문장이 붙습니다.

<h3 class="star-h"><span class="star-tag">T</span>과제</h3>

첫 RGB+텍스트 정책을 행동 복제로 학습하고 검증 쌍 10개와 학습 시드 세 개로 문장이 실제로 목적지
기둥을 정하는지 확인하는 것이 과제였습니다.

<h3 class="star-h"><span class="star-tag">A</span>행동</h3>

사전 학습 부분이 없는 480,901개 파라미터 정책을 노트북 CPU에서 학습했습니다(20 에폭, 마지막 에폭
사용, 시드당 7–8.5분). 평가 규칙은 실행 전에 미리 적어 두었습니다. 폐루프로 채점한 다음 모든 학습
상태에서 문장을 쌍 상대의 것으로 바꿔 넣고 좌우 명령의 변화를 전문가와 비교했습니다.

<h3 class="star-h"><span class="star-tag">R</span>결과</h3>

정책은 기둥 쪽으로 날지만 두 문장이 서로 다른 타깃으로 이어진 쌍은 10개 중 0, 2, 0개였습니다.
모든 시드가 지시된 타깃에 정확히 20개 중 10개만 갔고 쌍 성공은 세 시드 모두 0/10이었습니다. 첫
프레임에서 문장을 바꾸면 정책의 좌우 명령은 0.004–0.007 m/s 변해 전문가의 0.200 m/s에 한참
못 미쳤습니다. 말이 필요한 프레임은 첫 프레임(학습 행의 2%)뿐이어서 BC는 이미지만 읽는 쪽으로
학습됐습니다.

</div>

</div>
