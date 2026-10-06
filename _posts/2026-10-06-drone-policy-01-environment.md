---
title: "Part 1 · Fast frames, missing targets: a drone simulator on a laptop CPU"
title_ko: "1편 · 빠른 프레임, 사라진 타깃: 노트북 CPU로 드론 시뮬레이터 만들기"
title_zh: "第 1 篇 · 帧很快，目标却不见了：用笔记本 CPU 搭建无人机仿真"
permalink: /blog/drone-policy/01-environment/
series: drone-policy
part: 1
tags: [drone, simulation, pybullet, wsl2, profiling]
excerpt: "Part 1 of *Building and Evaluating a Drone VLA from Scratch*. A CPU-only drone simulator records 34.5 frames per second, but the first recording passed every check I had written and still had the targets cut out of view."
excerpt_ko: "연재 *드론 VLA를 처음부터 만들고 평가하기* 1편. CPU만으로 초당 34.5 프레임을 녹화하는 드론 시뮬레이터. 그런데 첫 녹화는 내가 만든 검사를 모두 통과하고도 타깃이 화면 밖으로 잘려 있었습니다."
excerpt_zh: "系列 *从零构建并评估无人机 VLA* 第 1 篇：纯 CPU 的无人机仿真每秒录制 34.5 帧，但第一次录制通过了我写的所有检查，目标却被裁出了画面。"
tldr_en:
  - "A drone simulator that runs entirely on a laptop CPU records <b>34.5 camera frames per second</b>, about 7x faster than real time."
  - "Measuring every stage changed two decisions: the <b>PID controller costs 2.7x the environment steps</b> it drives, and the library's default camera settings double the render stage, so I replaced them."
  - "The first recording passed every check I had written, yet <b>the targets were cut out of most frames</b>. Only looking at the images caught it."
tldr_ko:
  - "노트북 CPU만으로 도는 드론 시뮬레이터가 <b>초당 34.5장의 카메라 프레임</b>을 녹화합니다. 실제 시간보다 약 7배 빠릅니다."
  - "단계별로 시간을 재 보니 결정 두 개가 바뀌었습니다. <b>PID 컨트롤러가 environment step의 2.7배</b> 시간을 쓰고, 라이브러리 기본 카메라 설정은 렌더 시간을 2배로 늘려서 카메라를 직접 만들었습니다."
  - "첫 녹화는 내가 만든 검사를 모두 통과했지만 <b>대부분의 프레임에서 타깃이 잘려 있었습니다</b>. 이미지를 직접 보고서야 잡았습니다."
tldr_zh:
  - "完全运行在笔记本 CPU 上的无人机仿真，每秒录制 <b>34.5 帧相机图像</b>，约为实时的 7 倍。"
  - "逐阶段计时改变了两个决定：<b>PID 控制器耗时是其驱动的环境步的 2.7 倍</b>，而库默认的相机设置会让渲染时间翻倍，于是我换掉了它。"
  - "第一次录制通过了我写的所有检查，但<b>大多数帧里目标被裁出了画面</b>。只有亲眼看图像才发现。"
---

{% include tldr.html %}

<div class="lang-ko pnote" lang="ko" markdown="1">
본문은 영어로 작성되어 있습니다. 그림과 코드는 언어와 상관없이 같습니다.
</div>
<div class="lang-zh pnote" lang="zh-Hans" markdown="1">
正文为英文，图表与代码与语言无关。
</div>

If I change only the words, will the drone fly somewhere else? Before I could even ask that,
my simulator produced 1,000 frames that passed every automated check I had written, and a
contact sheet showed the targets cut out of view. This post is about turning a CPU-only
simulator into something that produces *usable* observations, and measuring what each recorded
frame costs.

<figure class="pfig">
  <img src="/images/blog/drone-policy/common/expert_val_pair0.gif" alt="Two side-by-side camera views from the same start: one instruction sends the drone to the blue box, the other to the green cylinder" loading="lazy">
  <figcaption><b>Scripted expert, not a trained policy.</b> This is where the series is heading: same start, same camera image, two different sentences, two different targets. Whether a small learned policy can do the same is the question of Parts 3 to 5.</figcaption>
</figure>

## The first recording looked valid, and wasn't

The setup is a laptop with **no NVIDIA GPU**: WSL2 + Ubuntu, PyBullet through
[gym-pybullet-drones](https://github.com/learnsyslab/gym-pybullet-drones), and a camera on the
drone that records a 128 x 96 image every 0.2 simulated seconds.

My first 1,000-frame recording passed every check I had written: right count, right shape, no
NaNs, no duplicate images, nothing blank. Then I laid 20 frames out as a contact sheet and looked
at them. In most of them the targets were cut off at the bottom edge.

**Hypothesis:** the drone was flying too high to see objects on the floor.
**Check:** it flew at about 1 m, and the targets sat about 1.2 m ahead at 0.15 m. Looking down at
them takes atan(0.85 / 1.2) ≈ 35°, but the level camera's 60° vertical field of view only
reaches 30° below its centre line.

<figure class="pfig">
  <img src="/images/blog/drone-policy/01/04_clipping_geometry.png" alt="Side view: at 1.0 m the line of sight to the target falls below the camera's field of view; at 0.5 m it falls inside" loading="lazy">
  <figcaption>Camera field of view (blue) against the line of sight to a target. At 1.0 m the target falls below the image; at 0.5 m it is inside.</figcaption>
</figure>

**Fix and verification:** fly at 0.4 to 0.6 m and record again. The new contact sheet shows both
targets in view:

<figure class="pfig">
  <img src="/images/blog/drone-policy/01/07_contact_sheet_after_fix.png" alt="Twenty recorded frames in a grid, each showing a red cube and a blue sphere on a checkered floor" loading="lazy">
  <figcaption>20 frames sampled evenly from the 1,000-frame recording, after the fix. The original sheet was overwritten by the re-recording; the geometry above shows why it failed.</figcaption>
</figure>

A frame of sky and floor is non-blank and unique, so **none of the checks I had written could
see this. Looking at the data did.** I'll come back to this lesson throughout the series. The
guard that would catch it automatically is a target-visibility count from the segmentation mask.

## The minimal simulator

<figure class="pfig">
  <img src="/images/blog/drone-policy/01/00_stack.png" alt="Five stacked layers from the Windows laptop at the bottom to PyBullet's TinyRenderer at the top" loading="lazy">
</figure>

Two choices matter for everything after this:

- **Linux, outside OneDrive.** The code runs in WSL2's own filesystem. Later stages (ROS 2, PX4,
  a C++ core) are Linux-first, and I didn't want a sync client or a folder name with spaces and
  Korean letters in the build path.
- **A CPU renderer.** PyBullet's *TinyRenderer* draws the camera image in software, so the
  missing GPU never enters the picture.

### Three clocks

<figure class="pfig">
  <img src="/images/blog/drone-policy/01/01_clock_rates.png" alt="Timeline of one 0.2 s frame: 48 physics ticks, 12 controller ticks at 60 Hz, 9.6 at 48 Hz, one camera frame" loading="lazy">
  <figcaption>One recorded frame lasts 0.2 s. At 60 Hz the controller updates exactly 12 times inside it; at 48 Hz, the library examples' default, it would be 9.6.</figcaption>
</figure>

- **Physics, 240 Hz.** The engine advances the world 1/240 s per step.
- **Controller, 60 Hz.** A PID controller turns "fly this way" into four motor speeds. It reacts
  to the current error, how that error has added up, and how fast it is changing.
- **Camera and policy, 5 Hz.** Every 0.2 s the drone takes a picture and the policy decides what
  to do next.

So one recorded frame is **48 physics steps and 12 controller updates**. I started with the
examples' 48 Hz, but that puts 9.6 updates in a frame, so I switched to 60 Hz: my fixed-tick
recording loop then runs exactly 12.

## What the drone actually sees

<figure class="pfig pfig--narrow">
  <img src="/images/blog/drone-policy/01/06_onboard_camera.gif" alt="Animated onboard camera view: a red cube and a blue sphere drifting across a checkered floor" loading="lazy">
  <figcaption>One 20-second episode from the drone's camera: 100 frames at 128 x 96, shown at 2x speed. This is all the visual input a policy gets.</figcaption>
</figure>

To see what is *in* those pixels, I rendered one frame with PyBullet's segmentation mask, which
labels every pixel with the object it belongs to, and counted:

<figure class="pfig">
  <img src="/images/blog/drone-policy/01/03_frame_contents.png" alt="A 128x96 frame, the same frame colour-coded by object, and a bar chart of pixel shares" loading="lazy">
  <figcaption>One sampled frame. Half of it is sky, because the level camera puts the horizon on the centre line. The two targets, the only thing the policy must tell apart, take up under 8% of the pixels. The dark wedges at the bottom are the drone's own arms.</figcaption>
</figure>

When so little of the image carries the answer, a small policy can latch onto an easier cue
instead. Part 5 shows a policy doing exactly that.

The camera call is short (simplified; full code in
[`dronevla/camera.py`](https://github.com/Ckck12/Drone_VLA_Simulation/blob/98490502e80c0cf50a65765619e6d72f03ee88b0/dronevla/camera.py#L123-L144)):

```python
def render(self, pos, quat, client=0):
    """Return one frame as (height, width, 3) uint8, alpha dropped."""
    w, h, rgba, _, _ = p.getCameraImage(
        width=self.width, height=self.height,          # 128 x 96
        viewMatrix=self.view_matrix(pos, quat),        # where the drone is and where it looks
        projectionMatrix=self._projection,             # 60 deg vertical FOV, square pixels
        shadow=0,                                      # off: the default doubles render time
        flags=p.ER_NO_SEGMENTATION_MASK,               # off: the policy never sees it
        renderer=p.ER_TINY_RENDERER,                   # CPU software rasteriser, no GPU
        physicsClientId=client,
    )
    return np.asarray(rgba, dtype=np.uint8).reshape(h, w, 4)[:, :, :3]
```

## Where one recorded frame spends its time

I timed every stage of producing a recorded frame over 1,000 frames, after 50 warm-up frames
that were thrown away. I report medians (p50). I repeated the whole run in a second process, and
throughput agreed within 1.7%.

<figure class="pfig">
  <img src="/images/blog/drone-policy/01/02_frame_budget.png" alt="Stacked bars of milliseconds per frame by stage, for this project's camera and for the library defaults" loading="lazy">
  <figcaption>Median milliseconds per recorded frame, by stage. Top: the camera as configured here. Bottom: the same run with the library's default shadow and segmentation on.</figcaption>
</figure>

<div class="kpis">
  <div class="kpi"><span class="kpi__value">34.5 frames / s</span><span class="kpi__label">recorded frames per wall-clock second, whole pipeline</span></div>
  <div class="kpi"><span class="kpi__value">6.9x real time</span><span class="kpi__label">20 simulated seconds take about 2.9 wall seconds</span></div>
  <div class="kpi"><span class="kpi__value">12.8 ms</span><span class="kpi__label">to render one 128 x 96 image on the CPU</span></div>
  <div class="kpi"><span class="kpi__value">113 MB</span><span class="kpi__label">peak resident memory; frames stream to disk</span></div>
</div>

Two findings changed decisions:

1. **The controller costs more than the physics.** Twelve calls to the pure-Python PID
   controller take 9.5 ms. The 12 environment steps they steer, 48 physics steps inside, take
   3.5 ms. Every policy I evaluate later pays this cost, so it is a fixed part of every
   experiment's time budget.
2. **Defaults matter.** The library's built-in camera renders shadows and a segmentation mask.
   Neither is useful here, and together they double the render stage, from 12.8 to 26.5 ms per
   image. That is why the project has its own camera.

The timing loop puts a stopwatch around each stage (simplified;
[full code](https://github.com/Ckck12/Drone_VLA_Simulation/blob/98490502e80c0cf50a65765619e6d72f03ee88b0/dronevla/profile_env.py#L286-L330)):

```python
for i in range(n_frames):
    t_physics, t_control = 0.0, 0.0
    for _ in range(ctrl_per_frame):                  # 12 controller periods per frame
        t0 = time.perf_counter()
        obs, *_ = env.step(action)                   # 4 physics steps each
        t_physics += time.perf_counter() - t0
        t0 = time.perf_counter()
        action[0], *_ = ctrl.computeControlFromState(...)
        t_control += time.perf_counter() - t0

    t0 = time.perf_counter(); rgb = cam.render(pos, quat); t_render = time.perf_counter() - t0
    t0 = time.perf_counter(); png = encode_png(rgb);       t_encode = time.perf_counter() - t0
    t0 = time.perf_counter(); path.write_bytes(png);       t_write  = time.perf_counter() - t0
```

## Can I afford the dataset?

The plan assumed 6 to 15 KB per frame, stored as JPEG. Measured:

<figure class="pfig">
  <img src="/images/blog/drone-policy/01/05_bytes_per_frame.png" alt="Bar chart: PNG 1.4 KB, JPEG q75 2.7 KB, JPEG q90 3.9 KB, against a 6 to 15 KB assumption band" loading="lazy">
</figure>

For these 128 x 96 frames of flat colour, lossless PNG is the *smallest* option: 1.4 KB, against
2.7 to 3.9 KB for JPEG. At the measured rate, the planned 200,000 frames would take **about
1.6 hours and 0.3 GB**, or 2.4 hours if I keep the plan's 1.5x safety margin. These numbers
extrapolate the measured rate; that dataset doesn't exist yet.

## What this part does not show

- The scene is a floor plus two plain shapes. Richer scenes render slower and compress worse.
- The laptop was busy during the runs, with an editor and a browser open. A quiet machine could
  land on either side of these numbers.
- Everything here is **simulation**. Nothing has flown a real drone.

## Next

A fast simulator is not yet a *believable* one. Part 2 asks which simulation assumptions
actually change the task: a gimbal camera, wind, and position noise, each matched against the
spec sheet of a real drone.

## Appendix

<details>
<summary>A. Two reproducibility traps (pybullet build, first CI run)</summary>

<p><b>pybullet built without numpy.</b> On Python 3.12, pybullet has no pre-built wheel, so pip
compiles it. pip compiles in an isolated environment where numpy is invisible. The result still
works, but <code>getCameraImage</code> then returns Python lists, and every render timing quietly
includes a list conversion. I install numpy first, build pybullet with
<code>--no-build-isolation</code>, and CI asserts <code>pybullet.isNumpyEnabled() == 1</code> on
every run. (This was caught by checking before it bit, not after.)</p>

<p><b>The first CI run failed.</b> To save time, I left torch out of the CI install because the
profiler never imports it. <code>pip check</code> in a fresh clone showed why that can't pass:
gym-pybullet-drones requires stable-baselines3, which requires torch. CI now installs the whole
lock file, with torch from PyTorch's CPU-only index rather than the default one, which pulls
several GB of CUDA packages. It then diffs <code>pip freeze</code> against the lock file. The
next run
<a href="https://github.com/Ckck12/Drone_VLA_Simulation/actions/runs/37257954542">passed</a>.</p>
</details>

<details>
<summary>B. Turning on the GPU driver lowered the OpenGL version</summary>

<p>By default, OpenGL in WSL2 runs on the CPU (<code>llvmpipe</code>).
<code>GALLIUM_DRIVER=d3d12</code> routes it through the Intel GPU. Diffing the two
<code>glxinfo</code> outputs showed a trade, not a pure speed-up: the GPU path offers OpenGL
<b>4.1</b>, the software path <b>4.5</b>. TinyRenderer uses no OpenGL, so it doesn't matter yet.
It will matter for Gazebo later.</p>
</details>

<details>
<summary>C. Reproduce (setup, measurement, figures)</summary>

<p>Ubuntu 24.04 (native or WSL2), Python 3.12, <code>build-essential</code>. Commit
<a href="https://github.com/Ckck12/Drone_VLA_Simulation/tree/98490502e80c0cf50a65765619e6d72f03ee88b0"><code>9849050</code></a>.</p>

<pre><code>git clone https://github.com/Ckck12/Drone_VLA_Simulation.git dronevla &amp;&amp; cd dronevla
git checkout --detach 98490502e80c0cf50a65765619e6d72f03ee88b0
SHA=$(sed -n 's/.*gym-pybullet-drones\.git@\([0-9a-f]\{40\}\).*/\1/p' env-lock-candidate.txt)
git clone https://github.com/learnsyslab/gym-pybullet-drones.git third_party/gym-pybullet-drones
git -C third_party/gym-pybullet-drones checkout --detach "$SHA"

python3.12 -m venv .venv &amp;&amp; source .venv/bin/activate
pip install --upgrade pip setuptools wheel
pip install "$(grep '^numpy==' env-lock-candidate.txt)"          # before pybullet
pip install --no-build-isolation "$(grep '^pybullet==' env-lock-candidate.txt)"
pip install "$(grep '^torch==' env-lock-candidate.txt)" --index-url https://download.pytorch.org/whl/cpu
grep -v -e '^-e ' -e '^torch==' env-lock-candidate.txt &gt; /tmp/req.txt
pip install -r /tmp/req.txt
pip install --no-deps -e third_party/gym-pybullet-drones
pip check

python -m dronevla.profile_env --frames 1000 --renderer tiny --out reports/env.json
python scripts/figures/blog01_environment.py      # every figure in this post
</code></pre>
</details>

### References: what I took from each

- [gym-pybullet-drones](https://github.com/learnsyslab/gym-pybullet-drones) provides a quadrotor
  model and a PID controller on top of PyBullet. → I pinned it by commit and kept its
  controller, but replaced its camera, whose defaults cost twice the render time.
- [PyBullet Quickstart Guide](https://docs.google.com/document/d/10sXEhzFRSnvFcl3XxNGhnD4N2SedqwdAvK3dsihxVUA/)
  documents `getCameraImage` and its CPU TinyRenderer. → On a laptop without a GPU, that path
  takes the GPU out of the question entirely.
- [pip: build system interface](https://pip.pypa.io/en/stable/reference/build-system/)
  explains that builds run in an isolated environment by default. → Good for reproducibility,
  but it hid numpy from pybullet's compiler, so I turn isolation off for that one package and
  assert the result.
