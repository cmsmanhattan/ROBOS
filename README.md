# ROBOS / HUMANOID v3.2

**Project description**

Pack dated 2026-09-21, updated 2026-09-24/25  
(archives `…_2026-09-24k`, `…_2026-09-25a`)

A full-size wearable-armor humanoid: **165 cm**, **~46–48 kg**, aluminum skeleton first, removable PETG plates second.

The robot is still in simulation and STL — nothing is printed yet, there is no stand, and the printer is being assembled.

---

## One-sentence pitch

A 165 cm home-worker / security-assistant humanoid whose load-bearing structure is an aluminum “skeleton,” covered by knight-style removable plates so every joint, motor, and harness can be serviced without destroying the shell.

---

## What it is

| Spec | Current baseline |
|---|---|
| Height | 1650 mm |
| Mass | ~45.6 kg PETG shell + Al frame; full robot with electrics ~46–48.1 kg depending on pack |
| Design language | Human skeleton in knight armor; plates are removable; repairability beats cosmetics |
| Drive mix | RobStride / DaMiao (DM) / CubeMars / Xrobotek / Dynamixel |
| Power | 48 V bus; pack target ~500 Wh (13S4P originally; 11S4P / 12S4P under study because the 13S boxes do not fit) |
| Actuators | 30 rotary body joints + 12 in the hands; 9 motor families in map v8 |
| Hands | Tendon / XC330 fingers, knuckle bar, fingertip BMP390 |
| Sim | MuJoCo 3.13 (`humanoid_v32_sim.xml`) |
| Print | PETG plates + selected metal nodes (knee bowl/cheeks, ankle crank) |

Principle stated in the pack: **mechanics before the shell.**

The silhouette is one source file (`silhouette_v32.py`). Frame scripts and mass tables are kept line-for-line consistent.

---

## Current mechanical state

### Closed or accepted

- Actuator map **v8** (24 Sep): knee = DM-J10010-2EC on axis; ankle = 2× RS06 on r = 18 mm cranks; battery treated as 11S4P in that map.
- Unification path **variant A**: fewer motor types (11 → 8 in an earlier cut); elbow XRA030N → RS06; waist RS03 → DM-J8009.
- Knee class G1: metal bowl plate + D100 cheeks; hub cap reprinted to passport PCD of DM-J10010-2EC.
- Ankle crank v1c in metal; sim on an honest lever (RS06 peak ~38–43 N·m vs 36 rated; RMS ~11 = nominal).
- Thigh STL v3: 8 plates, ~670 g, window for G1 knee.
- Torso skin v8c (smooth) / v8e (wrist lip D46); first full torso recut (18 plates + deck + hood, 1771 g).
- Both arms cut: `arm_v1` + mirrored `arm_v1_R` (~542 g left with 2-screw cap); 21 standoffs to the frame on the left arm.
- Hand v8d: palm-edge variant C accepted; finger + knuckle STL started.
- Static A/B in MuJoCo: G1 knee is clean; some “one-leg rider / 75 N push” failures were test artifacts (stiff servos ×10, push point, coarse grid).

### Not closed

- Rider (non-structural) plates: holes + countersinks not generated for the full set.
- Frame standoff points K are indicative, not frozen.
- TPU 4×6 gasket groove unproven — do not claim IP67.
- Foot / boot / sole still a placeholder.
- Full 48 V power schematic, wire gauges, bus caps, CAN topology, e-stop, charging, indication — electrical design exists as a v1 sheet/note, not as a build pack.
- Walking controller: stance, squat, and shove hold; **steps fall** — gait controller required.
- Strict arm method: sustained outstretched load ~0.7 kg (shoulder DM-J8009), peak ~2.5 kg. Ankle on one foot at the toe does not make the RS06 pair. Choice still open: larger motors / lower payload / restricted poses.
- Fastener and bearing BOM not counted per robot.
- Joint-zero calibration procedure after assembly.
- First physical test article: one leg (HJL12 + AK80-64 + linkages).

### Approved “do not” list

- No RS04 in pelvis-roll.
- No AK80-64 in shoulder-pitch.
- No XM540 back in the forearm.
- No return of shoulder shields.
- Silhouette kinks at z ≈ 1400 and z ≈ 1070–860 are intentional.

---

## How the robot is organized

1. **Skeleton** — 6061 aluminum tubes, cheeks, brackets (`skel_leg.py`, `skel_arm.py`, `skel_torso.py`, `lattice.py`). Motors bolt to named frame parts.
2. **Actuators** — single source list `M` in `fit_final.py`. Maps and X-rays live under `renders/ETALON7/` (`MOTOR_MAP_v8`, torque-margin sheet, zone map: water / metal-instead-of-plastic / sensors).
3. **Shell** — printed plates in two roles: **bearing** (orange, to frame at points K) and **riding** (blue, only to bearing plates). Assembly rules: `SHELL_ASSEMBLY_v32.md`.
4. **Hands** — 24 parts historically; 6× XC330-T288; tendons; fingertip BMP390 (`FINGERS_HAND_v32.md`, `FINGER_TIP_SENSOR_v32.md`).
5. **Electrics (intent)** — 48 V from pack → fuses → bus → motors; PDB; precharge contactor; 12/5 V DC-DC for Dynamixel; 4 CAN + 2 TTL; e-stop. Mass add ~1.8–2.7 kg in the v1 estimate.
6. **Sim** — `build_mjcf.py` → MJCF; stance holds, push holds, squat OK; walking needs a controller. Battery inertia moved into the chest in v8d.

Primary drawings to read first are the ETALON7 sheets: ankle crank, ankle sim run, shin v4, metal knee, motor map v8, plate-mount (left leg as the reference), torque margin, seams, and skeleton close-ups.

---

## Software / pack layout

Two delivery styles:

- Full pack (~157 MB) including intermediate renders.
- Light docs pack: documents, scripts, STL, sim, final sheets only.

Scripts run from `v32/` with `PYTHONPATH=.:../v31`.

Silhouette is the single geometric source (`PRON_COLLAR`, `WRIST`, `UPPER_SMOOTH`, `ELBOW_PUCK`, `PLATE_G`, torso/hand env flags).

- Decision log: `journal.txt`
- Gap list: `MISSING_v32.md`

Latest print-oriented zips (25 Sep): both arms cut; torso already recut under v8c/v8e skin; fingers/knuckles added.

Portable arm cutter: `ARM_CUT_ARMS_2026-09-25.zip` (~3.7 min).

### Key documents (v32, current)

| File | Role |
|---|---|
| `PROJECT_OVERVIEW_v32.md` | Entry point |
| `ACTUATOR_MAP_v8.md` | Current motor list |
| `TORQUE_MARGIN_v32.md` | Need vs can, method and sources |
| `SKELETON_v32.md` | Aluminum frame |
| `SHELL_ASSEMBLY_v32.md` | Plate roles, order of assembly |
| `SILHOUETTE_v32.md` | Outer shape v7 / v8c / v8d / v8e |
| `ELECTRICS_v32.md` | 48 V intent |
| `MISSING_v32.md` | Open work |
| `journal.txt` | Decision chronology |

---

## Intended use

Home assistant / worker / guard:

- Hybrid kinematics (rotary for dynamics, linear drives where high force is needed, e.g. stairs)
- Vest battery on the order of 0.5 kWh and ~1.5 h autonomy in the original concept
- Smoke / gas sensing

Control target is a custom on-robot model with robotics / tool-call tags for walk, balance, and manipulation. The structure is built so that stack can be swapped without reprinting the armor.

---

## Honest status line

The project is a **closed mechanical concept plus an open electrical and gait stack**.

Geometry, mass, motor map, knee passport mount, and first-print STLs for thighs, shins, knee covers, torso, both arms, and a finger exist.

Standing and disturbance rejection are demonstrated in MuJoCo.

Walking, IP rating, full harness, and a physical prototype of even one leg are still ahead.

Payload at the outstretched hand and single-leg toe-off are the two numbers that still force a motor / pose / duty-cycle decision.

---
## Renders

All files: [IMAGES](IMAGES/)

### Full height

| | |
|---|---|
| ![](IMAGES/humanoid_full_size_1.png) | ![](IMAGES/humanoid_full_size_old.png) |
| ![](IMAGES/humanoid_full_size_old_2.png) | |

### Body

| | |
|---|---|
| ![](IMAGES/Boby1.png) | ![](IMAGES/Body_2.png) |
| ![](IMAGES/Body_3.png) | ![](IMAGES/Body_4.png) |
| ![](IMAGES/Body_5.png) | ![](IMAGES/Body_old.png) |
| ![](IMAGES/Body_Hard2_1.png) | |

### Iron

| | |
|---|---|
| ![](IMAGES/Hard1.png) | ![](IMAGES/Hard2.png) |
| ![](IMAGES/Hard3.png) | ![](IMAGES/Hard4.png) |
| ![](IMAGES/Hard5.png) | |

### Legs

| | |
|---|---|
| ![](IMAGES/Legs1.png) | ![](IMAGES/Legs2.png) |
| ![](IMAGES/Legs3.png) | |

### Head

| | |
|---|---|
| ![](IMAGES/head_1.png) | ![](IMAGES/head_overlay_side_old.png) |

### Hand/fingers

| | |
|---|---|
| ![](IMAGES/KNUCKLE_BAR_concept_v1_sheet%20(1).png) | ![](IMAGES/finder2.png) |
| ![](IMAGES/finder3.png) | ![](IMAGES/finder4.png) |


*Source: `README_PACK_v32.md` (assembled 2026-09-21, updated 2026-09-24/25).*
