---
name: metal-yield-damper-data-processing
description: Process multi-level cyclic loading test data for displacement-type metal yielding dampers, including wall-panel dampers QB/XNQB, metal yielding dampers MYD, buckling-restrained braces BRB, and similar devices. Use when Codex needs to archive raw damper test files, identify loading conditions/cycles, calculate method-one and method-two yield parameters, judge deviations from standard/design values, generate Excel reports, or draw curve review plots with auxiliary lines and picked points.
---

# Metal Yield Damper Data Processing

## Purpose

Use this skill to process metal yielding damper mechanical test data where parameters are obtained from staged loading cycles rather than a single monotonic curve.

Supported product families:
- Wall-panel dampers: QB, XNQB, 墙板阻尼器
- Metal yielding dampers: MYD
- Buckling-restrained braces: BRB
- Other displacement-type yielding dampers using multi-level cyclic loading and point-picking rules

## Required Inputs

Collect these before calculating:
- Raw data files, preferably `.dat`, `.csv`, `.xlsx`, or equivalent text exports with displacement and force columns.
- Product/spec table: design yield force, yield displacement, pre-yield stiffness, post-yield stiffness, design force, design displacement, ultimate displacement where available.
- Method document or project note if the user provides one. Follow it over this skill when it conflicts.
- Any drawing/PDF only as reference; do not infer numeric standards from drawings unless explicitly needed.

Default column mapping:
- Displacement: `Defor(mm)` or equivalent displacement column.
- Force: `Load(kN)` or equivalent force/load column.
- Units: displacement in mm, force in kN, stiffness in kN/mm.

## Workflow

1. Archive source files into the project test-data folder if the user asks for archiving. Keep project names in file names as records; do not create separate project files unless requested.
2. Read any provided method Markdown/PDF first. If no method is provided, use `references/methods.md`.
3. Identify standard loading conditions and cycles. For the XNQB workflow, use the first 6 loading conditions and the 3rd cycle of each condition; ignore later fatigue segments for standard parameter calculation.
4. Extract positive and negative peak points from each selected cycle. Use original data points for final reported points whenever the method allows.
5. Calculate both method one and method two, and keep their outputs separate.
6. Compare measured values with standard/design values. Use the user's tolerance if given; otherwise use +/-15% as the warning threshold.
7. Generate an Excel workbook with method-one deviation, method-two deviation, combined deviation, parameter summary, peak records, condition recognition, pick records, calculation log, standards table, curve review, and notes.
8. Record a concise result summary in the relevant project Markdown file if the user wants durable project memory.

## Calculation Rules

Read `references/methods.md` for the full reusable calculation protocol.

Core rules:
- Use only standard-condition cycles for standard parameters unless the user asks for fatigue analysis.
- Design force and ultimate force are the force values at design/ultimate displacement peaks, not the maximum force in the whole cycle.
- Method one: yield point is the intersection of the initial stiffness line and post-yield stiffness fit line.
- Method two: yield point is selected by the unloading-stiffness method; K0 and Kp for method two must be recalculated from method-two points.
- Preserve positive, negative, and average values separately for major parameters.
- All artificial fits and point selections must be traceable in the workbook.

## XNQB/QB Current Project Defaults

When processing the user's XNQB wall-panel damper data unless corrected:
- Design displacement: 28 mm.
- Ultimate displacement: usually 33.6 mm for 70 kN and 140 kN specs.
- File name `XNQB-140-2.2` means design yield force 140 kN and yield displacement 2.2 mm.
- For the 2026-06-25 batch, 140 kN used 8 pieces of 12 mm LY225 steel plates; 70 kN used 4 pieces.
- For the 2026-07 100 kN batch, use 5 pieces of 12 mm LY225 steel plates.
- Positive initial stiffness may be manually reviewed; negative initial stiffness can be mirrored from the reviewed positive K0 when the user chooses that method.

## Output Style

Use Chinese sheet names and project notes by default for this user's Obsidian workflows.

Recommended workbook sheet names:
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
- Method-one K0 and Kp auxiliary lines
- Method-one yield points
- Method-two unloading stiffness line
- Method-two K0 line from origin to Fy2/Dy2
- Method-two Kp line from Fy2/Dy2 to Fd/Dd
- Design and ultimate displacement auxiliary lines

## Validation

Before final response:
- Check that all specimens have output rows in both method sheets.
- Check that method-two K0 uses `Fy2 / Dy2`, not the method-one initial segment.
- Check that method-two Kp uses `(Fd - Fy2) / (Dd - Dy2)`, not the six-peak Kp fit.
- Check that curve images are embedded in the workbook.
- Check for spreadsheet formula/export errors.
- Report any parameters marked `需人工复核`.
