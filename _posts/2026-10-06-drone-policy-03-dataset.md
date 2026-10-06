---
title: "Part 3 · Same scene, different words: a counterfactual dataset"
title_ko: "3편 · 같은 장면, 다른 말: counterfactual 데이터셋 만들기"
title_zh: "第 3 篇 · 同一场景，不同指令：反事实数据集"
date: 2026-10-06 11:00:00 +0900
permalink: /blog/drone-policy/03-dataset/
series: drone-policy
part: 3
tags: [drone, dataset, data-pipeline, provenance, simulation]
excerpt: "Part 3 of *Building and Evaluating a Drone VLA from Scratch*. 120 layouts, each flown twice from the identical start: once per sentence. How the pairs are generated, split and verified, and two bugs that only reading the output back could catch."
excerpt_ko: "연재 3편. 120개 배치를 같은 출발점에서 문장마다 한 번씩, 두 번 비행했습니다. pair를 만들고 나누고 검증하는 방법, 그리고 출력을 다시 읽어 봐야만 잡을 수 있었던 버그 두 개."
excerpt_zh: "系列第 3 篇：120 个布局，每个都从完全相同的起点飞两次，每句指令一次。如何生成、划分和验证这些配对，以及只有回读输出才能发现的两个 bug。"
tldr_en:
  - "Every example comes in a <b>pair</b>: the same start and the same first image, flown twice by the scripted expert, once per sentence. So the image alone can never tell which target is meant."
  - "Dataset v0.2: <b>240 episodes, 12,198 frames, 19 MB</b>, generated in 9 minutes on the laptop CPU. 100 training pairs; 10 validation and 10 test pairs held out by layout."
  - "A validator re-reads every file and runs 14 checks. Two bugs were caught only by looking at the output: a visibility check rendered from <b>inside the drone</b>, and a teacher flagged as speeding 37% of the time."
tldr_ko:
  - "모든 예제는 <b>pair</b>로 옵니다. 같은 출발점, 같은 첫 이미지에서 스크립트 전문가가 문장마다 한 번씩 두 번 비행합니다. 그래서 이미지만으로는 어느 타깃인지 절대 알 수 없습니다."
  - "데이터셋 v0.2: <b>240 에피소드, 12,198 프레임, 19 MB</b>, 노트북 CPU로 9분 만에 생성. 학습 100 pair, 검증 10 pair, 테스트 10 pair를 배치 단위로 분리했습니다."
  - "validator가 모든 파일을 다시 읽어 14개 검사를 돌립니다. 버그 두 개는 출력을 직접 봐야만 잡혔습니다: <b>드론 안쪽에서</b> 렌더링하던 가시성 검사, 그리고 37%의 시간 동안 속도 초과로 표시된 전문가."
tldr_zh:
  - "每个样本都是<b>一对</b>：相同起点、相同首帧，由脚本专家飞两次，每句指令一次。因此仅凭图像永远无法判断目标是哪一个。"
  - "数据集 v0.2：<b>240 个回合、12,198 帧、19 MB</b>，在笔记本 CPU 上 9 分钟生成。100 对训练，10 对验证、10 对测试按布局划分。"
  - "验证器回读所有文件并运行 14 项检查。有两个 bug 只有亲眼看输出才发现：在<b>无人机内部</b>渲染的可见性检查，以及被标记为 37% 时间超速的专家。"
---

{% include tldr.html %}

<div class="lang-ko pnote" lang="ko" markdown="1">
본문은 영어로 작성되어 있습니다. 그림과 코드는 언어와 상관없이 같습니다.
</div>
<div class="lang-zh pnote" lang="zh-Hans" markdown="1">
正文为英文，图表与代码与语言无关。
</div>

Part 2 fixed the task and the scorer. Now the policy needs examples to learn from. The question
of this series sets one hard requirement on the data: **listening has to be necessary.** If
one image could only ever lead to one target, a policy could ignore the words and still match
every label. So every example in this dataset comes as a pair.

<figure class="pfig">
  <img src="/images/blog/drone-policy/03/02_pair_strip.png" alt="Two rows of five camera frames from the same start: the top row approaches the red box, the bottom row the green cylinder; the first frames are identical" loading="lazy">
  <figcaption>One training pair. Left column: the identical first frame. From there the scripted expert flies toward whichever pillar the sentence names, and the two rows drift apart.</figcaption>
</figure>

## 1. How one pair is made

<figure class="pfig">
  <img src="/images/blog/drone-policy/03/01_pipeline.png" alt="Flow: seed, sample layout and reject bad ones, one start snapshot, expert flies both targets, recorder, validator, manifest" loading="lazy">
</figure>

1. **A seed picks a layout:** two pillars of different colours, box or cylinder, somewhere
   in front of the drone. Layouts that would make a bad test are thrown away and resampled.
   Examples: hover points closer than 1.5 m, or a straight path to one pillar that brushes the
   other.
2. **One start snapshot** is shared by both flights. Everything random (the layout, the
   position-noise stream) comes from the seed, never from which target is named. That's why
   the two first frames are byte-identical.
3. **The scripted expert flies both targets** through the same action adapter, controller and
   physics a policy would use. It is an *oracle*: it reads true positions, which a policy never
   gets. That's fine for a teacher, as long as the dataset says so (it does).
4. **The recorder** writes every 0.2 s: the PNG image, the 11-number state, the action actually
   applied, the Stop label, and, in separate `priv_*` columns that policies never read, the true
   position for later analysis.

The sentences come from two human-written templates, with no language model involved:
*"Go to the {colour} {shape} and stop."* and *"Approach the {colour} {shape}, then hold
position."* Both flights of a pair use the same template, so the only difference between them
is the colour and shape words.

## 2. What is in v0.2

| | |
|---|---|
| episodes | 240 (120 pairs), every one flown successfully by the expert |
| frames | 12,198 PNG images, 19.05 MB, about 1.6 KB each |
| episode length | median 51 frames (10.8 simulated seconds), range 38–65 |
| generation time | 553 s on the laptop CPU |

<figure class="pfig">
  <img src="/images/blog/drone-policy/03/04_splits.png" alt="Bar: 100 training pairs, 10 validation pairs, 10 test pairs, with their seed ranges" loading="lazy">
</figure>

**Splits are by layout, not by frame.** If frames from one flight landed in both training and
validation, a model could memorise the scene and look good for the wrong reason. Here, both
flights of a pair and every frame of a flight stay in one split, and no layout appears twice.
The validation and test seeds are the same as in the first, smaller version of the dataset, so
results stay comparable across versions. One caveat: those test seeds were looked at during an
earlier, discarded round of experiments, so final test numbers will come from a fresh test split.

<figure class="pfig">
  <img src="/images/blog/drone-policy/03/03_dataset_stats.png" alt="Histogram of episode lengths, a bar chart of label shares (Stop 6.7 percent, at speed cap 75 percent), and a colour by side table" loading="lazy">
  <figcaption>Left: episode lengths. Middle: two lopsided labels. Right: which colour the named target had and which side it stood on, across all 240 episodes.</figcaption>
</figure>

Two label properties shape everything in Part 4:

- **Stop is rare.** Only 6.7% of action rows say "stop". The training loss weights those rows
  up, with the weight computed from training data only.
- **Most of the time the expert flies at full speed.** 75% of commands are at the 0.5 m/s cap.
  The interesting part of each label is its *direction*, and at the very start that direction
  depends only on the sentence.

The colour-by-side table is reported, not enforced: green goals were on the left 42 times and
on the right 26. The splits are small, so a balanced design would have to come from the
generator, not from filtering afterwards.

## 3. Proving the files are what they claim

A dataset that changes silently invalidates every result built on it. So the recorder writes
a **manifest**: the code commit, the simulator commit, the full task configuration and its hash,
the camera parameters, a SHA-256 for every non-image file, and one hash over all 12,198 images.
Re-running the command on the same machine reproduces the images byte for byte.

Then a validator reads everything back *from disk* and runs 14 checks: files present and hashes
matching, images decoding to 96 x 128 x 3, every pair complete and identical at the start,
splits disjoint, every sentence naming its goal, actions aligned with observations. Here is the
pair check, slightly simplified
([full code](https://github.com/Ckck12/Drone_VLA_Simulation/blob/b42a22766427e9e4eba6b8e8c79754e63f69f386/dronevla/dataset.py#L140-L172)):

```python
for pid, eps in by_pair.items():
    a, b = sorted(eps, key=lambda e: e["goal_index"])           # goal 0 and goal 1
    for key in ("split", "layout_id", "snapshot_seed", "first_rgb_sha256", "instruction_family"):
        if a[key] != b[key]:
            pair_problems.append((pid, f"{key} differs"))       # same start, same first image
    if a["instruction"] == b["instruction"]:
        pair_problems.append((pid, "same instruction for both goals"))
    if load_steps(root, a["episode_id"])[0]["proprio"] != load_steps(root, b["episode_id"])[0]["proprio"]:
        pair_problems.append((pid, "first proprio differs"))    # same state, too
check("pairs_complete_and_identical_at_start", not pair_problems, ...)
```

## 4. Two bugs that only reading the output caught

Both happened while I was building the first version of the generator. Each is now covered by a
test.

**A good layout was rejected: the visibility check rendered from inside the drone.** One
candidate layout was thrown away because "a target covers 0 pixels at the start". The real first
frame showed it clearly, at 346 pixels.
*Hypothesis:* the check and the recording render from different places.
*Confirmation:* the check rendered from the *nominal* start position. Position noise leaves the
hovering drone a few centimetres away from there, so the check's camera sat inside the real
drone's arm and propeller, which filled the whole view.
*Fix:* render the check from the pose of the actual first observation. The layout is now test
pair 5, with both targets clearly visible.

**The teacher was "speeding" 37% of the time.** The expert commanded exactly 0.5 m/s. Stored
as float32, that became 0.5000001 m/s, so the adapter flagged a cap on 37% of the expert's
actions. Numerically harmless, but it would have reported a teacher that never exceeds the limit
as clipping more than a third of the time. The expert now commands 0.5 x (1 − 10⁻⁵): zero flags.

## What this dataset can't support

- Two pillars in an empty room, yaw fixed, flat motion. It says nothing about open-vocabulary
  language or real scenes.
- 10 validation and 10 test layouts are few, so success rates will carry wide intervals.
- The two pillars always differ in colour, so colour alone always decides the target.

## Next

Part 4 trains a policy on these 100 pairs, and it does not listen.

## Appendix

<details>
<summary>Reproduce</summary>

<p>Commit <a href="https://github.com/Ckck12/Drone_VLA_Simulation/tree/b42a22766427e9e4eba6b8e8c79754e63f69f386"><code>b42a227</code></a>, set up as in Part 1. Images and parquet files are not in git; the manifest, datasheet, splits and validation report are.</p>

<pre><code>python -m dronevla.record --out data/v0.2 --pairs 100 10 10 --version 0.2.0    # ~9 min on CPU
python scripts/inspect_episode.py --dataset data/v0.2 --episode val-004-g1      # every frame + action arrow
python scripts/figures/blog02_04.py
</code></pre>
</details>

### References: what I took from each

- [Datasheets for Datasets (Gebru et al.)](https://arxiv.org/abs/1803.09010) proposes a standard
  record of a dataset's purpose, composition and collection. → The recorder writes a datasheet
  with every v0.2 number in this post computed from the files.
- [LeRobotDataset](https://huggingface.co/docs/lerobot/lerobot-dataset-v3) stores episodes as
  Parquet plus media with metadata. → I kept a simple Parquet + PNG layout now and will convert
  when SmolVLA needs it.
