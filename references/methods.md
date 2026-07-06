# Metal Yield Damper Data Processing Methods

## 1. Data And Standards

Use original force-displacement data. Normalize column names where needed:
- Displacement: `Defor(mm)`, `Displacement(mm)`, `Disp(mm)`, `位移`
- Force: `Load(kN)`, `Force(kN)`, `力值`, `荷载`

Standards table should include, when available:
- Yield force `Fy_std`
- Yield displacement `Dy_std`
- Pre-yield stiffness `K0_std`
- Post-yield stiffness `Kp_std`
- Design force `Fd_std`
- Design displacement `Dd_std`
- Ultimate displacement `Du_std`

Deviation:

```text
deviation = (measured - standard) / standard
```

Default automatic judgment:
- `abs(deviation) <= 15%`: `±15%内`
- otherwise: `偏差超±15%`
- missing standard: `无标准值`
- unstable extraction: `需人工复核`

## 2. Loading Condition And Cycle Selection

For staged cyclic loading:
- Identify positive and negative displacement extrema.
- Group extrema into loading conditions.
- Use the specified cycle in each condition. For XNQB/QB current workflow, use condition 1 to condition 6, 3rd cycle.
- Ignore fatigue cycles after the standard conditions when calculating standard mechanical parameters.

For each selected condition, record positive/negative peak row number, displacement, force, cycle range, and whether it participates in standard calculation.

## 3. Direct Peak Parameters

Use original peak points:
- `Fd+`, `Dd+`: positive peak force/displacement in design condition, usually condition 5, cycle 3
- `Fd-`, `Dd-`: negative peak force/displacement in design condition
- `Fu+`, `Du+`: positive peak force/displacement in ultimate condition, usually condition 6, cycle 3
- `Fu-`, `Du-`: negative peak force/displacement in ultimate condition

Averages use absolute values:

```text
Fd_avg = (abs(Fd+) + abs(Fd-)) / 2
Dd_avg = (abs(Dd+) + abs(Dd-)) / 2
Fu_avg = (abs(Fu+) + abs(Fu-)) / 2
Du_avg = (abs(Du+) + abs(Du-)) / 2
```

Do not substitute maximum force for force at maximum displacement unless the user explicitly changes the method.

## 4. Method One: Initial Stiffness And Post-Yield Fit Intersection

### Initial Stiffness K0

Use the first condition, first cycle initial loading segment unless the project method says otherwise.

Fit:

```text
F = K0 * D + b0
```

Record fit range, row numbers, and number of points.

If the user chooses mirrored negative K0:
- Fit only the reviewed positive initial segment.
- Use same slope for the negative side.
- Use mirrored intercept for the negative auxiliary line.
- Mark negative K0 as mirrored rather than independently fitted.

### Post-Yield Stiffness Kp

Fit positive and negative 3rd-cycle peak points from the selected standard conditions:

```text
F = Kp+ * D + bp+
F = Kp- * D + bp-
Kp_avg = (abs(Kp+) + abs(Kp-)) / 2
```

### Yield Point

Positive:

```text
Dy+ = (bp+ - b0+) / (K0+ - Kp+)
Fy+ = K0+ * Dy+ + b0+
```

Negative:

```text
Dy- = (bp- - b0-) / (K0- - Kp-)
Fy- = K0- * Dy- + b0-
```

Average:

```text
Fy_avg = (abs(Fy+) + abs(Fy-)) / 2
Dy_avg = (abs(Dy+) + abs(Dy-)) / 2
```

Flag for manual review when K0 is close to Kp, signs are wrong, or yield displacement exceeds design displacement.

## 5. Method Two: Unloading Stiffness Method

### Unloading Stiffness Kun

Default source: design condition, usually condition 5, cycle 3.

Fit positive and negative unloading segments:

```text
F = Kun+ * D + bun+
F = Kun- * D + bun-
Kun_avg = (abs(Kun+) + abs(Kun-)) / 2
```

Record row numbers and point counts.

### Method-Two Yield Point

Draw the origin line:

```text
F = Kun_avg * D
```

Select original points nearest to this line within the relevant positive/negative curve ranges:

```text
error_i = abs(F_i - Kun_avg * D_i)
```

Output:

```text
Dy2+ = D at nearest positive original point
Fy2+ = F at nearest positive original point
Dy2- = D at nearest negative original point
Fy2- = F at nearest negative original point
```

Average:

```text
Fy2_avg = (abs(Fy2+) + abs(Fy2-)) / 2
Dy2_avg = (abs(Dy2+) + abs(Dy2-)) / 2
```

### Method-Two K0 And Kp

Do not reuse method-one K0/Kp in the method-two result sheet.

Method-two initial stiffness:

```text
K0_2+ = Fy2+ / Dy2+
K0_2- = Fy2- / Dy2-
K0_2_avg = (abs(K0_2+) + abs(K0_2-)) / 2
```

Method-two post-yield stiffness:

```text
Kp_2+ = (Fd+ - Fy2+) / (Dd+ - Dy2+)
Kp_2- = (Fd- - Fy2-) / (Dd- - Dy2-)
Kp_2_avg = (abs(Kp_2+) + abs(Kp_2-)) / 2
```

The method-two sheet should show:
- `屈服前刚度 K0（方法二：Fy2/Dy2）`
- `屈服后刚度 Kp（方法二：Fy2-Fd连线）`
- `屈服力 Fy2（方法二）`
- `屈服位移 Dy2（方法二）`
- `卸载刚度 Kun`
- `设计承载力 Fd`
- `设计位移 Dd`
- `极限位移 Du`

## 6. Report And Traceability

For each workbook:
- Keep method-one and method-two deviation sheets separate.
- Include a combined deviation sheet.
- Include a parameter summary with positive, negative, and average values.
- Include pick records with row numbers, ranges, and calculation purpose.
- Include curve review plots with auxiliary lines.

For project Markdown notes:
- Record final workbook path.
- Record script paths if scripts were created.
- Record calculation口径 and any user corrections.
- Add a compact table of main results for future orientation.
