---
title: "Part 2 · From a real drone's spec sheet to a testable task"
title_ko: "2편 · 실제 드론 스펙에서 검증 가능한 과제로"
title_zh: "第 2 篇 · 从真实无人机的规格表到可检验的任务"
date: 2026-10-06 10:00:00 +0900
permalink: /blog/drone-policy/02-task/
series: drone-policy
part: 2
tags: [drone, simulation, evaluation, metrics, gymnasium]
excerpt: "Part 2 of *Building and Evaluating a Drone VLA from Scratch*. What a 27 g simulated quadrotor can honestly borrow from a 1 kg drone's spec sheet, the input/output contract every policy must satisfy, and how a flight is scored, all fixed before any model is trained."
excerpt_ko: "연재 2편. 27 g 시뮬레이션 쿼드로터가 1 kg 드론 스펙에서 정직하게 가져올 수 있는 것, 모든 정책이 지켜야 할 입출력 계약, 비행을 채점하는 방법. 모델을 학습하기 전에 모두 고정합니다."
excerpt_zh: "系列第 2 篇：27 g 的仿真四旋翼能从 1 kg 无人机的规格表中如实借用什么，每个策略都必须满足的输入/输出约定，以及如何为一次飞行打分。全部在训练任何模型之前确定。"
tldr_en:
  - "A real drone (DJI Mavic 4 Pro, ~1 kg) and the simulated one (Crazyflie, 27 g) differ 40x in mass, so only <b>scale-free</b> specs are copied: camera field of view, a 3-axis gimbal, a 35° tilt limit."
  - "The gimbal makes the image <b>25x steadier</b>, and a smoothed position-noise model was chosen because the scripted expert must still land every Stop."
  - "Every policy gets the same contract: image <code>(96,128,3)</code> + state <code>(11,)</code> + one sentence → action <code>(5,)</code>. A flight is scored by 8 outcomes and <b>pair success</b>: both sentences of a pair must work."
tldr_ko:
  - "실제 드론(DJI Mavic 4 Pro, 약 1 kg)과 시뮬레이션 드론(Crazyflie, 27 g)은 무게가 40배 다릅니다. 그래서 카메라 화각, 3축 짐벌, 35° 기울기 제한처럼 <b>크기와 무관한</b> 스펙만 가져왔습니다."
  - "짐벌로 화면이 <b>25배 안정적</b>이 되었고, 위치 노이즈 모델은 스크립트 전문가가 모든 Stop에 성공할 수 있는 설정으로 골랐습니다."
  - "모든 정책은 같은 계약을 따릅니다: 이미지 <code>(96,128,3)</code> + 상태 <code>(11,)</code> + 문장 1개 → 행동 <code>(5,)</code>. 채점은 결과 8종과 <b>pair 성공</b>(한 쌍의 두 문장 모두 성공)으로 합니다."
tldr_zh:
  - "真实无人机（DJI Mavic 4 Pro，约 1 kg）与仿真无人机（Crazyflie，27 g）质量相差 40 倍，所以只借用<b>与尺度无关</b>的规格：相机视场角、三轴云台、35° 倾角限制。"
  - "云台让画面<b>稳定 25 倍</b>；位置噪声模型的选择标准是脚本专家仍能完成每一次 Stop。"
  - "所有策略遵循同一约定：图像 <code>(96,128,3)</code> + 状态 <code>(11,)</code> + 一句指令 → 动作 <code>(5,)</code>。评分采用 8 种结果和 <b>pair 成功</b>（一对中两句指令都要成功）。"
---

{% include tldr.html %}

<div class="lang-ko pnote" lang="ko" markdown="1">
본문은 영어로 작성되어 있습니다. 그림과 코드는 언어와 상관없이 같습니다.
</div>
<div class="lang-zh pnote" lang="zh-Hans" markdown="1">
正文为英文，图表与代码与语言无关。
</div>

Part 1 ended with a fast simulator. Fast isn't the same as believable, and believable isn't
the same as testable. Before training anything, I wanted three things fixed: **which parts of
a real drone the simulation copies**, **what exactly a policy sees and outputs**, and **how a
flight is scored**. If those move later, every result moves with them.

<figure class="pfig">
  <img src="/images/blog/drone-policy/02/04_task_topdown.png" alt="Top view of the 8 m room: the drone starts on the left, two coloured pillars on the right, two expert flight paths splitting toward them" loading="lazy">
  <figcaption>The whole task in one picture. Same start, two sentences, two flights by the scripted expert. Each circle is the 0.4 m region the drone must stop inside.</figcaption>
</figure>

## 1. A spec sheet, filtered by scale

I took the spec sheet of a real consumer drone, the **DJI Mavic 4 Pro**, and went through it
item by item. The simulated airframe is a **Bitcraze Crazyflie 2.x**: the physics parameters in
gym-pybullet-drones (mass, inertia, thrust, drag) come from that real product.

| | Mavic 4 Pro (spec) | Crazyflie 2.x (simulated) |
|---|---|---|
| take-off mass | ~1,063 g | 27 g |
| where it flies | outdoors, km range | indoors, an 8 m room here |
| top horizontal speed | 15–25 m/s | capped at 0.5 m/s in this task |

A 40x difference in mass means absolute numbers such as "25 m/s" can't transfer. So every spec
item went into one of three bins:

| Bin | Examples | What I did |
|---|---|---|
| **Scale-free: copy it** | camera field of view, 3-axis gimbal, max tilt 35°, camera at the front | copied |
| **Real, but size must be scaled** | hover accuracy ±0.1 m / ±0.3 m, wind resistance | modelled at a smaller size |
| **Out of reach on this laptop** | 1 kg airframe dynamics, 100 MP photoreal camera, battery, radio link | written down, not attempted |

## 2. The camera: a gimbal, tilted down

In Part 1 the camera was bolted to the body and looked straight ahead. That has two problems.
Half of every frame was sky, and when the drone pitches forward to accelerate, the whole image
tilts with it. The Mavic carries its camera on a 3-axis gimbal, which keeps the image level.
So the simulated camera now does the same, tilted 20° down, with the Mavic's 72° (diagonal)
field of view.

<figure class="pfig">
  <img src="/images/blog/drone-policy/02/02_camera_mounts.png" alt="The same instant rendered by three camera mounts, plus a bar chart of how much each image shakes along a route" loading="lazy">
  <figcaption>Left three: the same instant, with the drone pitched 10° while accelerating, seen by three camera mounts. Right: how much the share of sky in the frame jumps around along the same route. The gimbal image is 25x steadier than the Phase 0 camera.</figcaption>
</figure>

Tilting 20° down also moves the pixels from sky to floor and targets, which fixes Part 1's
"half the frame is sky" finding. The targets became **1.2 m pillars** so they stay in view all
the way to the stopping point. At the start, each target fills 1.5–4.5% of the frame; at the
stopping point, the named one fills 36–43%.

## 3. Position noise, chosen against the expert

A real drone doesn't know exactly where it is. Its position estimate drifts slowly. I added a
noise process to the position the controller *sees*. The camera still renders from the true
position, because it is physically on the drone. The size, 3 cm horizontally and 1 cm
vertically, is a **choice**. It keeps the Mavic's 3:1 ratio, scaled down to fit a small room.

The timing of the noise took more work. My first version was jumpy, and the scripted expert
(a controller that knows the true positions) began to fail: it couldn't hold still after
stopping, because the controller chased the jumping estimate as if it were real motion.

<figure class="pfig">
  <img src="/images/blog/drone-policy/02/03_noise_vs_expert.png" alt="Bar charts: expert success and the maximum speed after Stop for five noise settings; only the 3 s correlation time keeps every hold under the 0.1 m/s limit" loading="lazy">
  <figcaption>How slowly the noise drifts (its correlation time) against the scripted expert, 20 episodes each. A drifting estimate makes the drone creep after it stops; the 3 s setting keeps every hold under the 0.1 m/s limit.</figcaption>
</figure>

This choice was tuned on one set of seeds, so those 20/20 don't count as evidence. On 40
**held-out** episodes that I never looked at while choosing, the expert succeeded in all 40.

Wind and a tilt limit exist as well. The dataset in Part 3 uses noise and the tilt limit but
keeps wind off; the details are in the appendix.

## 4. The contract every policy must satisfy

This is the most important picture in the series. Every policy, from the tiny one in Part 4 to
SmolVLA later, has to fit into it.

<figure class="pfig">
  <img src="/images/blog/drone-policy/02/01_interface_card.png" alt="Diagram: camera image, proprioception and instruction go into the policy at 5 Hz; out comes a 5-number action that passes through an action adapter to the PID controller" loading="lazy">
  <figcaption>Inputs, output, units, frames and rates. The policy never sees its x-y position, the target coordinates or anything else the simulator knows but a real drone wouldn't. (Altitude is in the state, as from a real drone's barometer.)</figcaption>
</figure>

Two details matter:

- **The action is a velocity request, not motor speeds.** The policy says "go 0.4 m/s forward,
  0.1 m/s left". A PID controller underneath turns that into motor commands 12 times per
  decision. Learning to fly is not part of the task; learning *where* to fly is.
- **One adapter enforces the limits, for everyone.** The expert, the learned policies and later
  the C++ runtime all go through the same function
  ([full code](https://github.com/Ckck12/Drone_VLA_Simulation/blob/b42a22766427e9e4eba6b8e8c79754e63f69f386/dronevla/action_adapter.py#L47-L80)):

```python
def adapt(raw, limits=ActionLimits(), planar=True):
    """Validate and cap one raw policy action. Never raises on bad numbers -- it reports."""
    raw = np.asarray(raw, dtype=np.float64).reshape(-1)
    if raw.shape != (5,):
        return AdaptedAction(..., rejected=f"wrong shape {raw.shape}, expected (5,)")
    if not np.all(np.isfinite(raw)):
        return AdaptedAction(..., rejected="non-finite value in action")

    vx, vy, vz, yaw_rate, stop_logit = raw
    speed = math.hypot(vx, vy)
    if speed > limits.horizontal_mps:            # 0.5 m/s, capped as a vector, not per axis
        vx, vy = vx * limits.horizontal_mps / speed, vy * limits.horizontal_mps / speed
    ...                                          # same for vz (0.3 m/s) and yaw rate (0.5 rad/s)
    return AdaptedAction(applied=np.array([vx, vy, vz, yaw_rate]),
                         stop_positive=bool(stop_logit > 0.0), flags=flags)
```

The adapter records *which* limit engaged in `flags`, so "the policy asked for too much" is
always visible in the logs instead of silently corrected.

## 5. The task, and how a flight is scored

**The scene.** An 8 x 8 x 3 m room. The drone starts on one side at 1 m altitude. Two pillars
with different colours stand on the other side. Each has a *hover point* 0.75 m in front of it,
where the drone should stop.

**One episode.** The drone gets one sentence, such as *"Go to the red box and stop."*, and
flies. To stop, the policy must signal Stop three decisions in a row (0.6 s). The drone then
holds position for one second while the evaluator watches.

**Success** means all of these, judged on the *true* simulator state:

- within 0.4 m of the named hover point,
- slower than 0.1 m/s for the whole second after Stop,
- no collision, and within 30 s.

Everything else is a named failure, because "failed" alone teaches nothing:

| outcome | meaning |
|---|---|
| `wrong_target` | stopped at the *other* pillar's hover point |
| `stop_elsewhere` | stopped, but at neither hover point |
| `stop_not_settled` | stopped in the right place but kept drifting |
| `collision` / `out_of_bounds` | hit something / left the room |
| `timeout` | still flying after 30 s |
| `invalid_action` | output NaN or the wrong shape |

**Pair success** is the metric the series is built around. Each layout is flown twice from
the identical start, once per sentence. A pair counts only if **both** flights succeed.
A policy that ignores the words and always picks one pillar can still score 50% on single
episodes, but its pair success is zero.

<figure class="pfig pfig--narrow">
  <img src="/images/blog/drone-policy/common/expert_val_pair0.gif" alt="Side-by-side camera views and a top-down map: the expert flies to the blue box for one sentence and to the green cylinder for the other" loading="lazy">
  <figcaption>A pair, flown by the scripted expert. This is what pair success looks like.</figcaption>
</figure>

**Testing the scorer itself.** An evaluator with a bug produces believable wrong numbers. So
each outcome has a test that *causes* it on purpose: fly to the wrong pillar, ram a target,
signal Stop while still moving, and so on. The evaluator must name every one correctly.
The same test suite (79 tests at the commit below) also covers the action contract, the
environment API and pair determinism: both flights of a pair get byte-identical first images.

## What this part does not show

- This is not a Mavic. It is a 27 g quadrotor with a Mavic-like camera and noise profile.
- The 3 cm noise size is a choice, not a measurement. The 72° field of view assumes DJI quotes
  it diagonally, their usual convention.
- Everything is **simulation**.

## Next

The task and scorer are fixed. Part 3 builds the dataset: 120 pairs of flights where the same
image comes with two different sentences.

## Appendix

<details>
<summary>A. The tilt limit and the wind model</summary>

<p><b>Tilt limit.</b> The Mavic's maximum pitch is 35°, so the controller's requested tilt is
capped at 35°. When flying a route three times faster than the task's speed cap, the drone
still reached 39.7°, because the attitude loop overshoots its command by about 5°. The cap
limits the <i>request</i>, not the result. At the task's own speeds it never engages.</p>

<p><b>Wind.</b> The library's drag model uses velocity relative to the ground; mine uses
velocity relative to the air, which is what wind changes. With zero wind it reproduces the
library's trajectories bit for bit. The drag is linear in airspeed, so strong wind is
<b>optimistic</b> here. Results above about 2 m/s understate how hard it would be. The dataset
keeps wind off for now.</p>
</details>

<details>
<summary>B. Reproduce</summary>

<p>Commit <a href="https://github.com/Ckck12/Drone_VLA_Simulation/tree/b42a22766427e9e4eba6b8e8c79754e63f69f386"><code>b42a227</code></a>, set up as in Part 1.</p>

<pre><code>python -m pytest tests -q                    # includes one fixture per outcome
python scripts/try_env.py --headless         # same start, two instructions, two outcomes
python scripts/playground.py --headless      # camera mount, noise, wind: change and re-run
python scripts/figures/blog02_04.py          # every figure in parts 2-4
</code></pre>
</details>

### References: what I took from each

- [DJI Mavic 4 Pro specs](https://www.dji.com/mavic-4-pro/specs) give the camera, gimbal, tilt
  and hover-accuracy numbers. → I copied only the items whose meaning doesn't depend on size.
- [Bitcraze Crazyflie 2.1](https://www.bitcraze.io/products/crazyflie-2-1/) is the real airframe
  behind the simulated one. → Its mass and motor model are why the absolute speeds stay small.
- [Gymnasium: create a custom environment](https://gymnasium.farama.org/introduction/create_custom_env/)
  defines `reset`/`step`, terminated vs truncated. → A timeout is a truncation, not a failure
  state, which is why `timeout` is scored separately.
