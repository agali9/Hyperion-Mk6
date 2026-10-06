# Hyperion Mk6 (robot-arm)

Hyperion Mk6 is a 6-DOF robotic arm I designed and built: repurposed hoverboard BLDC hub motors on VESC CAN controllers at J1 and J2, serial-bus servos at J3-J6, and a 77-body Fusion 360 assembly with an Isaac Sim digital twin.

Right now there's sim + RL training in Isaac Lab, and a deploy folder for running the trained policy. All six joints are assembled. J1 and J2 have been driven and checked against the Isaac Sim twin; J3-J6 hardware tests are next. The trained policy runs in simulation only so far.

## What's in the repo

```
urdf/        robot model
isaaclab/    Isaac Lab env and reach task
deploy/      export policy and run inference
scripts/     training scripts
docs/guide/  build notes
```

## Training

Needs Isaac Lab and a GPU(quick smoke test).

```bat
isaaclab.bat -p scripts\train_reach.py --num_envs 64 --max_iterations 15 --headless
```

Checkpoints go to `logs/reach/`. There's already an exported policy in `deploy/exported/`.

## Run the policy in sim

```bat
isaaclab.bat -p deploy\scripts\run_inference.py --backend sim --policy deploy\exported\policy.pt
```

## Tests (no Isaac needed)

```bash
python deploy/tests/test_safety.py
```

## Results

- 6-joint PPO reach policy trained across 4,096 parallel Isaac Lab environments: 87.89% deterministic success over 256 evaluation episodes (simulation).
- Action-scale ablation: 49.61% success at 1.0 vs 87.89% at 1.5.
- Safety layer: NaN checks, slew-rate limits, watchdogs, an e-stop latch, and dual enable switches, covered by 54 pytest cases.

More info in `deploy/README.md` and `docs/guide/build.md`.
