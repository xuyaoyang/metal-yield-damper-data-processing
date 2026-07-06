# 金属屈服阻尼器数据处理

Codex skill for processing multi-level cyclic loading test data for displacement-type metal yielding dampers.

适用对象包括：

- 墙板阻尼器：QB、XNQB
- 金属屈服阻尼器：MYD
- 屈曲约束支撑：BRB
- 其他按多级加载取点判定的位移型阻尼器

## What This Skill Does

This skill guides Codex through a repeatable workflow for damper test-data processing:

- Archive and organize raw test files.
- Identify staged loading conditions and cycles.
- Extract positive and negative peak points from selected cycles.
- Calculate method-one and method-two yield parameters.
- Judge deviations from standard/design values.
- Generate Excel reports with traceable calculation sheets.
- Generate curve review plots with auxiliary lines and picked points.
- Record concise results back into project Markdown notes.

## Core Calculation Methods

### Method One

Initial stiffness and post-yield fit intersection:

- Fit initial stiffness `K0` from the initial loading segment.
- Fit post-yield stiffness `Kp` from selected standard-condition peak points.
- Calculate yield force and yield displacement from the intersection of the `K0` and `Kp` lines.

### Method Two

Unloading stiffness method:

- Fit unloading stiffness `Kun` from the design-condition cycle.
- Select `Fy2/Dy2` from the original point nearest to the `F = Kun_avg x D` origin line.
- Calculate method-two initial stiffness:

```text
K0_2 = Fy2 / Dy2
```

- Calculate method-two post-yield stiffness:

```text
Kp_2 = (Fd - Fy2) / (Dd - Dy2)
```

Method-two `K0` and `Kp` must not reuse method-one `K0` and `Kp`.

## Expected Outputs

Recommended workbook sheets:

- `方法一偏差判定`
- `方法二偏差判定`
- `全部偏差判定`
- `参数汇总`
- `第三圈峰值点`
- `工况识别`
- `取点记录`
- `计算日志`
- `标准值`
- `曲线复核`
- `说明`

Curve review plots should include:

- Complete hysteresis curve
- Selected standard cycles
- Peak points
- Method-one `K0` and `Kp` auxiliary lines
- Method-one yield points
- Method-two unloading stiffness line
- Method-two `K0` line from origin to `Fy2/Dy2`
- Method-two `Kp` line from `Fy2/Dy2` to `Fd/Dd`
- Design and ultimate displacement auxiliary lines

## Installation

Clone or copy this folder into a Codex skills directory, for example:

```text
C:\Users\<user>\.codex\skills\metal-yield-damper-data-processing
```

Then invoke it explicitly in Codex:

```text
$metal-yield-damper-data-processing
```

or ask in natural language:

```text
用金属屈服阻尼器数据处理这个 skill 处理这批检测数据。
```

## Files

- `SKILL.md`: Codex skill trigger metadata and main workflow.
- `references/methods.md`: Detailed reusable calculation protocol.
- `agents/openai.yaml`: UI metadata for Codex.

## Notes

This skill encodes the processing口径 developed from XNQB wall-panel damper test data, then generalizes it to QB, MYD, BRB, and other displacement-type metal yielding dampers that use staged cyclic loading and point-picking rules.
