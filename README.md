# catwalk_base_C01 — 张靖皋长江大桥 猫道六工况非线性静力计算

张靖皋长江大桥猫道（catwalk）关键图纸自动建模后的**六工况非线性静力复核**完整算例，
包含 CalculiX 输入模型、求解结果、找形模型与复核报告。

- 原始模型：`猫道 - 门架索合建模2.mct`（MIDAS CIVIL NX，单位 KN,MM,C）
- 求解器：CalculiX 2.23（含 UCAB3/UCOR6，WSL 执行）
- 建模/复核平台：BridgeMind Simulation Studio 1.5.2（图纸自动建模，非人工 OCR）
- 结果单位：位移 U = m，应力 S = Pa
- 算例性质：**静力复核演示，不替代正式设计验算，不作工程合格签认**

## 非线性设置

- 每个工况均开启 `*STEP, NLGEOM, INC=200`，即**几何非线性（大变形）静力求解**，
  以适应猫道承重索的悬索几何刚度与成型平衡。
- 材料为线弹性（`*ELASTIC`），承重索/门架索 E = 120 GPa，门架立柱 E = 206 GPa。
- 初始索力按各单元方向展开为全局张量（MCT 的 INIFORCE，1482.7–1572.4 kN），
  先在单步施加 100% 恒载建立成型平衡，C02–C06 再从已平衡状态逐工况叠加。
- 温度荷载通过 `*TEMPERATURE`（基准 0℃，整体降温）实现。

## 六工况定义

| 工况 | 组合 | 结果文件 |
|---|---|---|
| C01 | 恒载（自重 + 二期节点恒载） | `results/C01/model.frd` |
| C02 | 恒载 + 施工荷载（节点等效，合计 FZ ≈ -3344 kN） | `results/C02/model.frd` |
| C03 | 恒载 + 施工荷载 + 均匀降温 15℃ | `results/C03/model.frd` |
| C04 | 恒载 + 施工荷载 + 施工阶段等效风 | `results/C04/model.frd` |
| C05 | 恒载 + 最大阵风（等效节点力） | `results/C05/model.frd` |
| C06 | 恒载 + 施工荷载 + 均匀降温 34℃ | `results/C06/model.frd` |

施工荷载、风荷载均为 MIDAS 已等效到节点的静力节点荷载（施工风 FY 合计 ≈ 2246.9 kN，
阵风 FY ≈ 15303.7 kN）；本算例不重做风工程、不拆分活载，仅复现等效静力。

## 目录结构

```
catwalk_base_C01/
├── results/                 # 六工况 CalculiX 模型与结果
│   ├── C01/model.inp        # 输入模型（节点/单元/材料/边界/荷载/分析步）
│   │   └ model.frd          # 结果（位移、应力）
│   ├── C02 … C06/           # 同上
├── formfinding_0.inp … 4.inp # 找形（formfinding）模型
├── results.json             # 六工况与报告的文件清单及 SHA256
├── 07_report/
│   └── 猫道静力复核报告.pdf   # 25 页复核报告
└── report-pages/
    └── 1bbca7ccab52b55f/    # 报告逐页渲染图（page-001.png … page-025.png）
```

## 复现方法

在安装 CalculiX（ccx）的环境下，对任一工况执行：

```bash
ccx -i model          # 在 results/Cxx/ 目录内运行，生成 model.dat / model.frd
```

## 结果状态说明

- 结果模型（`results/`）为 **2026-10-18 重算**，六个工况的 `.frd` 均以正常结束标记
  完整输出，C05 已完整重算（其文件时间晚于其余工况）。
- `07_report/猫道静力复核报告.pdf` 为 **2026-10-05 版本**，其中"C05 最大阵风三档未收敛"
  的状态描述对应早前批次；该状态已被 2026-10-18 重算更新，报告正文其余模型、
  材料、边界与荷载口径保持一致。
