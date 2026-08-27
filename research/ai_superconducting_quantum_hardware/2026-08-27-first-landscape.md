# AI × 超导量子芯片设计自动化：第一轮文献调研与硕士课题地图

> 调研日期：2026-08-27  
> 目标：寻找“AI → superconducting quantum hardware design”方向中，能够快速实现、主要依赖仿真、可嵌入 Qiskit Metal + GDS + Ansys/HFSS/Q3D 流程，并具有 AI / EDA / scientific ML 发表潜力的硕士课题。  
> 方法：Breadth before depth；优先 2024–2026，必要时回溯经典工作；热点（Hotspot）与研究空白（Gap）分开评估。

---

## 0. 结论先行

本轮调研最重要的结论有四个。

1. **AI surrogate / inverse design 已经从“可迁移方法”进入“超导量子硬件直接应用”阶段。**  
   2026 年的 *Component-Level Inverse Design of Transmon Qubits Using Neural Networks* 已直接针对 planar transmon，把目标 Hamiltonian 参数 `(f_q, alpha)` 映射到几何参数，并用传统 EM solver 复验：97% 生成设计可用，`f_q` 平均百分比误差约 0.73%，`alpha` 约 1.58%；单次 CPU 查询约 56 ms，而传统 EM 电容提取约 2 min，速度提升 >2100×。这证明“EM 数据 → surrogate → inverse design → EM verification”不是概念，而是已经出现强基线。

2. **数据与基准正在快速补齐。**  
   SQuADDS（Quantum 2024）提供参数化超导器件设计、有限元电磁仿真和实验验证；SQChip-EM（KDD 2026）更进一步，将 Qiskit Metal 参数、GDSII、HFSS/Q3D 派生指标和 ML benchmark 绑定起来，包含 regression、feasibility classification、inverse-design retrieval 等任务。你的现有工具链与这个方向高度一致。

3. **Agentic EDA 在传统芯片领域已经形成可迁移范式，但在超导量子“版图-EM-再设计”闭环中仍非常早期。**  
   ChatEDA（IEEE TCAD 2024）、EDAid（NAACL 2025）、AnalogCoder（AAAI 2025）、ORFS-agent（MLCAD 2025 Best Paper）分别证明了：LLM 可进行 EDA 工具调用、长链任务分解、多 Agent 自一致性、仿真反馈自修复、参数优化。超导量子侧已有 SQuADDS MCP 接口、Quantum Metal 的 orchestration-friendly 路线，以及 Vibe Calibration（2026）这种真实超导处理器自主校准 Agent，但本轮扫描未发现一篇已正式发表、完整演示“Agent 修改 Qiskit Metal 几何 → 调用 Ansys/HFSS → 解析 EM 结果 → 自动迭代版图”的论文。

4. **最适合硕士阶段的主线不是“纯 LLM 生成量子芯片”，而是“确定性仿真 + ML surrogate + Agent orchestration”的混合闭环。**  
   最优策略是让：
   - Ansys/HFSS/Q3D：保持 physics authority；
   - surrogate：负责低成本预测、灵敏度和候选筛选；
   - optimizer（BO/CMA-ES/DE/gradient-free）：负责数值搜索；
   - Agent：负责工具调用、策略选择、错误恢复、实验记录、动态决定“何时调用真仿真”。

### 本轮最推荐的三个课题

**TOP-1：不确定性感知的 surrogate + active learning 超导器件优化**  
目标：用少量 Ansys 数据训练 surrogate；模型不确定时才调用 Ansys，持续增量训练；评价“达到目标规格所需的真实仿真次数”。  
优势：最容易做出明确 quantitative contribution，AI/EDA/scientific ML 都能讲。

**TOP-2：Agentic Qiskit Metal–Ansys 闭环优化系统**  
目标：Agent 自动修改设计参数、生成版图、发起仿真、解析结果、诊断失败、选择下一轮参数。  
优势：与现有工程流程直接衔接；传统 EDA 已有强方法可迁移，而量子版图闭环仍明显稀缺。

**TOP-3：Tunable coupler 的 surrogate inverse design / multi-objective optimization**  
目标：针对 coupler 的 on/off coupling、ZZ、频率、避免碰撞等多目标，以 surrogate 降低 EM 仿真次数。  
优势：比单 transmon 更有新颖性，又不必一开始做整芯片。

---

# A. Research Topic Taxonomy

```text
AI for Superconducting Quantum Hardware Design
├── 1. Data / Benchmark Infrastructure
│   ├── parameterized layout datasets
│   ├── GDS-grounded EM datasets
│   ├── simulation metadata / provenance
│   └── reproducible benchmark tasks
│
├── 2. Forward Surrogate Modeling
│   ├── geometry → capacitance / inductance
│   ├── geometry → f_q / f_r / alpha
│   ├── geometry → g / chi / kappa
│   ├── layout → crosstalk / frequency crowding
│   └── multi-fidelity surrogate
│
├── 3. Inverse Design
│   ├── target Hamiltonian → geometry
│   ├── target readout specs → resonator geometry
│   ├── target coupling / ZZ → coupler geometry
│   └── multi-objective inverse design
│
├── 4. Simulation-Efficient Optimization
│   ├── Bayesian optimization
│   ├── active learning
│   ├── surrogate-assisted evolutionary search
│   ├── uncertainty-aware sampling
│   └── trust-region / feasibility-aware optimization
│
├── 5. Agentic Quantum EDA
│   ├── tool calling
│   ├── Qiskit Metal code editing
│   ├── GDS generation
│   ├── Ansys job orchestration
│   ├── result parsing
│   ├── failure recovery
│   ├── experiment memory / provenance
│   └── closed-loop parameter update
│
├── 6. Layout / Physical Design Automation
│   ├── placement
│   ├── routing
│   ├── frequency allocation
│   ├── crosstalk-aware floorplanning
│   ├── geometry constraints
│   └── DRC / fabrication-aware optimization
│
├── 7. Adjacent Hardware AI
│   ├── qubit calibration agents
│   ├── readout optimization
│   ├── pulse optimization
│   └── cryogenic / infrastructure diagnosis
│
└── 8. Cross-Domain Transfer
    ├── classical EDA agents
    ├── analog / RF SPICE-in-loop design
    ├── antenna full-wave surrogate optimization
    ├── photonic inverse design
    └── autonomous scientific experimentation
```

**主题边界：**本调研把“AI 用于量子算法、QML、QNN”排除在核心集合之外；只有当工作能为 AI 驱动的物理硬件设计、仿真、校准或 EDA 工作流提供可迁移方法时才保留。

---

# B. Keyword Lattice

## B1. Quantum Hardware

```text
superconducting quantum hardware
├── superconducting quantum chip
├── superconducting quantum circuit
├── superconducting quantum processor
├── transmon
│   ├── fixed-frequency transmon
│   ├── tunable transmon
│   ├── planar transmon
│   └── cross transmon / TransmonCross
├── coupler
│   ├── tunable coupler
│   ├── flux-tunable coupler
│   ├── capacitive coupler
│   └── qubit coupler
├── readout
│   ├── readout resonator
│   ├── dispersive readout
│   ├── Purcell filter
│   └── multiplexed readout
├── resonator
│   ├── CPW resonator
│   ├── lumped-element resonator
│   └── SRF cavity
└── multi-qubit layout
    ├── frequency crowding
    ├── parasitic coupling
    ├── crosstalk
    └── connectivity
```

## B2. Engineering Task

```text
design automation
├── parameter optimization
├── design space exploration
├── inverse design
├── topology search
├── circuit synthesis
├── layout optimization
├── placement / routing
├── simulation acceleration
├── feasibility classification
├── design retrieval
├── sensitivity analysis
└── closed-loop optimization
```

## B3. AI Method

```text
AI / ML
├── surrogate model
│   ├── neural surrogate
│   ├── MLP
│   ├── CNN
│   ├── Transformer
│   ├── Gaussian process
│   ├── random forest
│   └── graph neural network
├── inverse model
│   ├── tandem network
│   ├── conditional generative model
│   ├── normalizing flow
│   └── diffusion / generative design
├── optimization
│   ├── Bayesian optimization
│   ├── active learning
│   ├── evolutionary search
│   ├── reinforcement learning
│   └── multi-objective optimization
└── agent
    ├── LLM agent
    ├── tool-using agent
    ├── multi-agent
    ├── self-correcting agent
    ├── planning
    ├── skill library
    ├── memory
    └── failure recovery
```

## B4. EDA / Simulation

```text
EDA / EM
├── Qiskit Metal / Quantum Metal
├── GDSII
├── Ansys
│   ├── HFSS eigenmode
│   ├── HFSS driven modal
│   └── Q3D
├── finite-element electromagnetic solver
├── EPR / pyEPR
├── LOM
├── capacitance extraction
├── S-parameters
├── meshing / convergence
└── simulation provenance
```

---

# C. Search Query Families

以下 26 条检索式相互独立，避免“几十个 OR 拼成一个超级式”。

| # | 检索式 | 搜什么 / 为什么 |
|---|---|---|
| 1 | `("superconducting quantum circuit" OR transmon) AND ("surrogate model" OR "neural surrogate") AND design` | 直接找超导器件 surrogate |
| 2 | `transmon AND "inverse design" AND ("neural network" OR "deep learning")` | transmon 逆向设计 |
| 3 | `("tunable coupler" OR "quantum coupler") AND ("machine learning" OR surrogate) AND optimization` | coupler AI 优化 |
| 4 | `("readout resonator" AND superconducting) AND ("inverse design" OR optimization) AND ("machine learning" OR surrogate)` | readout 几何设计 |
| 5 | `("superconducting quantum chip" OR "quantum chip layout") AND ("machine learning" OR AI) AND ("electromagnetic simulation" OR HFSS)` | layout→EM |
| 6 | `("superconducting quantum" AND Qiskit Metal) AND ("machine learning" OR inverse)` | Qiskit Metal + AI |
| 7 | `("Qiskit Metal" OR "Quantum Metal") AND (agent OR orchestration OR autonomous)` | Agent 插入设计工具 |
| 8 | `("superconducting quantum chip" OR transmon) AND ("Bayesian optimization" OR "active learning") AND design` | 少样本优化 |
| 9 | `("superconducting circuit" AND "automated design") AND coupler` | 经典自动设计 |
| 10 | `("quantum device" AND "design space exploration") AND superconducting` | DSE |
| 11 | `("superconducting quantum chip" AND dataset) AND (GDS OR electromagnetic)` | 数据集 / benchmark |
| 12 | `("Hamiltonian parameters" AND geometry) AND transmon AND design` | Hamiltonian→geometry |
| 13 | `("frequency crowding" OR crosstalk) AND superconducting AND layout AND optimization` | 多器件版图问题 |
| 14 | `("EDA agent" OR "EDA copilot") AND ("tool calling" OR automation)` | EDA Agent 基础范式 |
| 15 | `("LLM agent" AND OpenROAD) AND optimization` | 工具型 Agent 优化 |
| 16 | `("multi-agent" AND "electronic design automation")` | 多 Agent EDA |
| 17 | `("analog circuit" AND "LLM agent") AND SPICE` | SPICE-in-loop 迁移 |
| 18 | `("analog circuit design" AND code generation) AND LLM` | 代码生成 + 仿真反馈 |
| 19 | `("RF circuit" OR microwave) AND LLM AND design optimization` | RF 迁移 |
| 20 | `antenna AND ("surrogate model" OR "machine learning-assisted optimization") AND "full-wave"` | 全波仿真 surrogate |
| 21 | `photonic AND "inverse design" AND ("neural network" OR surrogate)` | 光子逆设计 |
| 22 | `("electromagnetic simulation" OR HFSS) AND ("surrogate model" OR "neural network") AND geometry` | 通用 EM 加速 |
| 23 | `("scientific agent" OR "engineering agent") AND simulation AND "closed-loop"` | 科学 Agent |
| 24 | `("autonomous experimentation" AND simulation) AND optimization` | 自主实验范式 |
| 25 | `("superconducting qubit" AND agent) AND calibration` | 超导硬件 Agent 邻近方向 |
| 26 | `("superconducting qubit" AND reinforcement learning) AND (readout OR control)` | 闭环优化可迁移方法 |

---

# D. Literature Landscape

## D1. 直接相关：AI → Superconducting Quantum Hardware

### 1. Seidel et al., 2026 — Component-Level Inverse Design of Transmon Qubits Using Neural Networks
- **状态**：arXiv:2607.20795，2026-07，预印本。
- **对象**：planar transmon component geometry。
- **WHY**：传统 forward loop 要反复改几何、做 EM 电容提取、再算 Hamiltonian，计算昂贵。
- **HOW**：forward MLP surrogate + inverse MLP 的 tandem architecture；inverse model 的 loss 在 Hamiltonian space 计算。
- **WHAT**：目标 `(f_q, alpha)` → 几何；97% 生成几何可用；`f_q` MPE 0.73%，`alpha` MPE 1.58%；单 query 56 ms CPU，对比约 2 min EM extraction，>2100× speedup。
- **数据规模**：论文摘要明确指出约 1000 个样本量级也可工作。
- **与你的相关性**：极高。可以直接把“transmon”扩展到“tunable coupler / readout / joint design”，或加入 active learning、uncertainty、multi-fidelity。
- **链接**：https://arxiv.org/abs/2607.20795

### 2. Peng et al., 2026 — SQChip-EM: A Qiskit-Metal-Based Layout-to-EM Simulation Dataset for Superconducting Quantum Chips
- **状态**：KDD 2026，ACM，DOI: 10.1145/3770855.3817558。
- **对象**：1–8 qubit parameterized layout；公开仓库当前明确包含 1q/2q 数据，并为更大规模准备 schema。
- **WHY**：抽象电路数据会丢失 layout-dependent EM effects。
- **HOW**：Qiskit Metal → GDSII → HFSS/Q3D-derived annotations → CSV/JSON → ML benchmark。
- **任务**：design-to-metric regression；specification feasibility classification；inverse-design retrieval。
- **指标**：`fq, fr, Delta, g/2pi, chi, kappa/2pi, chi/kappa, Cin, Ceff, chi_ij, estimated T1/T2` 等。
- **与你的相关性**：最高。几乎是你的现有流程的公开 benchmark 版本。
- **链接**：https://github.com/Secbrain/SQChip-EM

### 3. Shanto et al., 2024 — SQuADDS: A validated design database and simulation workflow for superconducting qubit design
- **状态**：Quantum 8, 1465 (2024)，同行评审。
- **WHY**：超导器件设计依赖大量计算密集型仿真和 trial-and-error。
- **HOW**：参数化器件由 Qiskit Metal 生成，有限元 EM 仿真；数据库中多项设计具有实验验证。
- **WHAT**：输入目标器件属性，返回已模拟过的“best-guess”设计，作为后续 refinement 起点。
- **2026 扩展**：项目已提供 ML inverse-design 模型和 MCP server，使数据库可被 AI Agent 调用。
- **与你的相关性**：可作为预训练数据、baseline、检索式 inverse design 基线、Agent 工具接口。
- **链接**：https://quantum-journal.org/papers/q-2024-09-09-1465/  
  https://github.com/LFL-Lab/SQuADDS

### 4. Yaker et al., 2026 — Neural-Network Inverse Design of SRF Cavities and Transmons for Bosonic Quantum Computation
- **状态**：arXiv:2607.02289，2026-07，预印本。
- **对象**：3D SRF cavity + transmon。
- **HOW**：两个 DNN inverse-design 层次：cavity geometry → cavity targets；transmon geometry/position → `(g, nu_q, alpha)`。
- **结果**：end-to-end re-simulation 后，cavity 目标约 5% 内，transmon 目标约 2% 内。
- **价值**：说明 inverse design 已开始从平面 transmon 扩展到 cavity–qubit 联合几何。
- **链接**：https://arxiv.org/abs/2607.02289

### 5. Zhao et al., 2025 — EDA-Q: Electronic Design Automation for Superconducting Quantum Chip
- **状态**：arXiv:2502.15386，预印本；有公开软件仓库。
- **对象**：量子芯片全栈 EDA。
- **HOW**：覆盖 topology/device mapping、equivalent circuit、GDS layout/routing、simulation，并强调 process mapping 和可扩展接口。
- **价值**：说明量子芯片 EDA 正在从“单工具”走向 full-stack；但论文核心不是 AI 优化。
- **研究机会**：在其设计/仿真流水线上加入 agent、surrogate、closed-loop optimization。
- **链接**：https://arxiv.org/abs/2502.15386

### 6. Menke et al., 2021 — Automated design of superconducting circuits and its application to 4-local couplers
- **状态**：npj Quantum Information 7, 49 (2021)。
- **对象**：超导 circuit topology / coupler。
- **HOW**：SCILLA 并行 closed-loop optimization，在预定义 spectral properties 与 noise sensitivity 下搜索电路。
- **价值**：证明“自动设计超导电路”并非全新问题；新意要落在现代 ML surrogate、agent、layout/EM grounding 或 sample efficiency 上。
- **链接**：https://doi.org/10.1038/s41534-021-00382-6

### 7. Yan et al., 2020 — Engineering Framework for Optimizing Superconducting Qubit Designs
- **状态**：arXiv:2006.04130，经典设计框架。
- **对象**：多指标 qubit design trade-off。
- **指标示例**：anharmonicity、T1、T2Echo 等。
- **价值**：提醒 AI objective 不应该只优化一个频率；应考虑物理 trade-off 与约束。
- **链接**：https://arxiv.org/abs/2006.04130

### 8. Xu et al., 2026 — Vibe Calibration: Autonomous Bring-up of a 112-Qubit Superconducting Quantum Processor by a Skill-Orchestrating Language Agent
- **状态**：arXiv:2606.22376，预印本。
- **对象**：真实超导量子处理器自动校准，而非版图设计。
- **HOW**：Skill-orchestrating language agent；decision-tree skills；quality gates；self-healing；人类经验蒸馏。
- **规模**：从小设备→16-qubit→112-qubit 的 staged validation。
- **质量门**：例如 curve fit `R² > 0.9`、signal `SNR >= 10 dB`。
- **可迁移点**：你的 Agent 也应有 deterministic acceptance criteria、failure recovery、audit trail，而不是“LLM 自己判断仿真成功”。
- **链接**：https://arxiv.org/abs/2606.22376

### 9. Chatterjee et al., 2025 — Enhanced qubit readout via reinforcement learning
- **状态**：Physical Review Applied 23, 054057 (2025)。
- **对象**：超导 qubit readout pulse + resonator reset。
- **HOW**：model-free RL。
- **结果**：在 IBM quantum device 上，优化 readout 并把 readout+reset 总流程相对默认过程加速接近 3×。
- **可迁移点**：多目标闭环评价、真实硬件反馈、RL/black-box optimization。
- **链接**：https://doi.org/10.1103/PhysRevApplied.23.054057

### 10. 2025 — Designing fast quantum gates using optimal control with a reinforcement-learning ansatz
- **状态**：Physical Review Applied 23, 014015 (2025)。
- **对象**：两个 transmon + tunable coupler 的 CZ 控制。
- **HOW**：RL 先找 ansatz，再用 gradient-based controller 精修。
- **结果**：20 ns gate 可把 error 降至 `1e-4` 以下。
- **可迁移点**：混合智能策略优于单一 AI；可类比为“Agent/ML 提议 + 数值优化/EM 求解器验证”。
- **链接**：https://doi.org/10.1103/PhysRevApplied.23.014015

### 11. Di Guglielmo et al., 2025 — End-to-End Workflow for Machine-Learning-Based Qubit Readout With QICK and hls4ml
- **状态**：IEEE Transactions on Quantum Engineering, 2025。
- **WHAT**：NN 被部署进 FPGA readout chain。
- **结果**：约 96% single-shot fidelity，32.25 ns latency，<16% FPGA LUT resource。
- **价值**：证明超导硬件 AI 论文可以同时强调 accuracy、latency、resource cost，而不是只报模型误差。
- **链接**：https://doi.org/10.1109/TQE.2025.3604712

---

## D2. 可迁移：Agent / EDA / Analog / RF / Photonics

### 12. Wu et al., 2024 — ChatEDA
- **状态**：IEEE TCAD 43(10), 2024。
- **核心**：LLM controller + EDA tools executors；任务分解、脚本生成、工具执行；覆盖 RTL-to-GDSII。
- **迁移**：把 OpenROAD API 换成 Qiskit Metal / Ansys API；保留“LLM planning + deterministic executors”的分层。
- **链接**：https://doi.org/10.1109/TCAD.2024.3383347

### 13. Wu et al., 2025 — EDAid
- **状态**：NAACL 2025 Main。
- **核心**：多 Agent divergent thoughts + decision agent，解决长链 tool calling 中中间错误累积。
- **迁移**：可构建“design proposer / simulator / critic / recovery”角色分工。
- **限制**：多 Agent 增加 inference latency。
- **链接**：https://aclanthology.org/2025.naacl-long.83/

### 14. Lai et al., 2025 — AnalogCoder
- **状态**：AAAI 2025。
- **核心**：training-free LLM agent；Python/PySpice code generation；仿真反馈自修复；成功电路存入 tool library。
- **结果**：24-task benchmark 中成功设计 20 个电路，比标准 GPT-4o 多 5 个。
- **迁移**：把“subcircuit skill library”换成“transmon/coupler/readout component skill library”。
- **链接**：https://doi.org/10.1609/aaai.v39i1.32016

### 15. Ghose et al., 2025 — ORFS-agent
- **状态**：ACM/IEEE MLCAD 2025，Best Paper。
- **核心**：LLM tool-using iterative optimizer，直接调 OpenROAD-flow-scripts 参数。
- **结果（2025 版本）**：wirelength / effective clock period 改善超过 13%，比 Bayesian optimization baseline 少约 40% optimization iterations。
- **迁移**：你的关键评价可以直接照搬“达到同等/更优设计质量所需的真实 Ansys 次数”。
- **链接**：https://doi.org/10.1109/MLCAD65511.2025.11189204

### 16. Wu et al., 2025 — OpenROAD Agent
- **状态**：IEEE ICLAD 2025。
- **核心**：自纠错 EDA script generation，真实执行 OpenROAD Python code，通过 tool feedback 动态修复。
- **报告结果**：script generation accuracy 约 94%。
- **迁移**：Ansys 常见失败（mesh、license、non-convergence、port setup、geometry invalid）可以建立 error taxonomy + repair policy。
- **公开代码**：https://github.com/OpenROAD-Assistant/OpenROAD-Agent

### 17. Vijayaraghavan et al., 2025 — AUTOCIRCUIT-RL
- **状态**：ICML 2025。
- **对象**：analog circuit topology synthesis。
- **HOW**：instruction tuning + RL refinement。
- **结果**：相对最佳 baseline，valid circuits +12%，efficiency +14%，duplicate generation -38%；有限训练数据时 >60% valid synthesis success。
- **迁移**：如果未来做 quantum circuit topology synthesis，可参考 constraint reward / validity reward。
- **链接**：https://proceedings.mlr.press/v267/vijayaraghavan25a.html

### 18. WiseEDA, 2025 — LLMs in RF Circuit Design
- **状态**：Microelectronics Journal 158 (2025), 106607。
- **核心**：LLM 选择 topology、配置 PSO optimizer、自动优化 RF netlist。
- **迁移**：LLM 不需要直接承担连续参数优化；可以负责“选择优化策略 + 参数边界 + 目标解释”。
- **链接**：https://doi.org/10.1016/j.mejo.2025.106607

### 19. Machine-Learning-Assisted Optimization for Antenna Geometry Design, 2024
- **状态**：IEEE Transactions on Antennas and Propagation 72(3), 2024。
- **HOW**：CNN/GPR surrogate + full-wave verified iterative optimization。
- **关键模式**：surrogate 先预测候选，full-wave simulation 只验证/更新高价值样本。
- **迁移**：几乎可直接映射到 HFSS 超导器件 geometry optimization。
- **链接**：https://doi.org/10.1109/TAP.2023.3346493

### 20. Khan et al., 2024 — Deep Learning CNN for Antenna Near-Field Prediction and Surrogate Modeling
- **状态**：IEEE Access 12 (2024)。
- **结果**：训练后相对 full-wave simulation >200× faster；nominal average RMSE 0.0174，并预测 resonant frequency、radiation pattern、directivity。
- **迁移**：强调不仅预测 scalar metric，也可预测 field-level representation；未来可做电场分布 / participation surrogate。
- **链接**：https://doi.org/10.1109/ACCESS.2024.3377219

### 21. Kim & Kim, 2025 — Efficient nanophotonic devices optimization using DNN + physics-based transfer learning
- **状态**：Scientific Reports 15, 39854 (2025)。
- **核心**：高数据成本条件下，physics-based transfer learning + surrogate + multi-objective GA。
- **迁移**：SQuADDS/SQChip-EM → 你的 fabrication stack/版图家族 的 transfer learning。
- **链接**：https://doi.org/10.1038/s41598-025-23519-5

---

# E. Research Hotspot Ranking

> 说明：下表不是严格的 bibliometric census，而是本轮 2024–2026 文献扫描后的“活跃度 + 方法进展 + 高质量 venue + 明确瓶颈”综合判断。Hotspot 与 Feasibility 分开打分（10 分制）。

| 主题 | Hotspot | Feasibility | 判断 |
|---|---:|---:|---|
| Surrogate / inverse design for superconducting devices | **9.5** | **9.5** | 2026 出现直接 transmon inverse design + KDD 数据集，且与你工具链高度一致 |
| Agentic EDA / closed-loop simulation orchestration | **9.5** | **8.5** | 传统 EDA 2024–2026 爆发；量子设计侧仍早期，迁移空间大 |
| Active learning / surrogate-assisted EM optimization | **9.0** | **9.5** | 方法成熟、可量化节省仿真次数，最适合硕士 |
| Tunable coupler AI design | **8.5** | **8.0** | 物理价值高，直接 AI 文献少于 transmon；创新空间大 |
| Multi-qubit layout / crosstalk surrogate | **8.5** | **6.5** | KDD 2026 数据开始出现；但高维和标签生成成本高 |
| Readout / resonator ML optimization | **8.0** | **8.5** | 硬件 ML 很活跃，几何版图方向尚可继续挖 |
| Quantum EDA full-stack automation | **8.0** | **7.0** | EDA-Q 等说明需求明确，但范围容易过大 |
| Circuit topology synthesis | **7.5** | **5.5** | 有 SCILLA 经典工作；需要 circuit solver + 搜索空间设计 |
| QEC hardware architecture automation | **7.0** | **4.5** | 热但容易滑向 architecture/compiler，不一定匹配现有 Ansys 流程 |
| Pure LLM-generated quantum chip layout | **7.0** | **4.0** | 新颖但验证困难；LLM 直接数值设计不如“agent + optimizer + solver”稳健 |

---

# F. Simulation Metrics Map

| Metric | 物理意义 | 真实文献/数据中出现 | AI 可否做目标 | Ansys 是否可直接/间接获得 | 适合当前课题 |
|---|---|---|---|---|---|
| `f_q` qubit frequency | qubit 0→1 跃迁频率 | inverse-transmon, SQChip-EM, SQuADDS | 是 | **间接**：Q3D/LOM/EPR + circuit quantization | ★★★★★ |
| `alpha` anharmonicity | 非谐性，影响可控性/泄漏 | inverse-transmon, SRF+transmon | 是 | **间接**：电容/JJ 参数 + quantization | ★★★★★ |
| `f_r` resonator frequency | readout/coupling resonator 模态 | SQChip-EM | 是 | **直接**：HFSS eigenmode | ★★★★★ |
| `g/2π` coupling | qubit–resonator / qubit–coupler 耦合 | SQChip-EM, SRF inverse | 是 | 间接/直接组合 | ★★★★★ |
| `χ` dispersive shift | readout 区分、色散耦合 | SQChip-EM | 是 | EPR/LOM + Hamiltonian postprocess | ★★★★★ |
| `κ/2π` linewidth | resonator decay/readout bandwidth | SQChip-EM | 是 | driven modal / Qext / postprocess | ★★★★☆ |
| `χ/κ` | readout strength vs linewidth | SQChip-EM | 是，多目标 | 由 χ、κ 计算 | ★★★★☆ |
| capacitance matrix | 几何→Hamiltonian 的关键中间量 | inverse-transmon | 是/辅助标签 | **直接**：Q3D | ★★★★★ |
| `C_in`, `C_eff` | 输入/有效电容 | SQChip-EM | 是 | Q3D | ★★★★☆ |
| `χ_ij` / ZZ-like coupling | qubit 间静态/色散串扰 | SQChip-EM 等 | 是 | EPR/电路 postprocess | ★★★★★ |
| T1 estimate | 能量弛豫 | SQChip-EM, classic design | 是，但模型依赖大 | 需 participation / loss model | ★★★☆☆ |
| T2 estimate | 相干性 | SQChip-EM/经典设计 | 不宜早期主目标 | 不能仅靠理想 EM 可靠得到 | ★★☆☆☆ |
| Q / Qext | resonator 品质因子 | readout / resonator design | 是 | HFSS driven/eigenmode | ★★★★☆ |
| electric field distribution | 场热点/参与率/寄生耦合 | EM surrogate 迁移 | 可做 representation target | HFSS field export | ★★★☆☆ |
| S-parameters | 端口响应、带宽、耦合 | RF/antenna 迁移 | 是 | HFSS driven modal | ★★★★☆ |
| crosstalk | 非目标耦合 | multi-qubit layout | 是 | S-parameter / modal / postprocess | ★★★★★ |
| frequency crowding margin | 多器件频率冲突余量 | SQChip-EM discussion | 是 | 由模态结果计算 | ★★★★★ |
| simulation wall time | 计算成本 | surrogate/ORFS 类工作 | **必须** | 日志直接获得 | ★★★★★ |
| number of solver calls | 样本效率 | ORFS-agent/active learning 范式 | **必须** | workflow 统计 | ★★★★★ |
| prediction error | surrogate 准确率 | inverse-transmon/antenna | **必须** | 与真仿真比较 | ★★★★★ |
| valid-geometry rate | 生成设计可制造/可仿真性 | inverse-transmon | **必须** | geometry checks + sim | ★★★★★ |
| optimization success rate | 达到规格的比例 | EDA agents/AutoCircuit | **必须** | 规格判定 | ★★★★★ |

### 建议的最小指标组合

对硕士第一篇论文，优先控制在：

- **物理指标**：`f_q`, `alpha`, `f_r`, `g`（或 coupler 的 on/off coupling + ZZ）
- **AI 指标**：MAE/MAPE/R² + uncertainty calibration
- **EDA 指标**：达到 target spec 所需真实 Ansys calls
- **系统指标**：wall-clock time、failure recovery rate、valid-design rate

---

# G. Cross-domain Transfer Opportunities

## G1. 从 ORFS-agent 迁移：以“真实求解器调用次数”为核心指标

```text
OpenROAD parameter
→ OpenROAD run
→ PPA
→ LLM/optimizer update

替换为

Qiskit Metal geometry
→ Ansys run
→ fq/fr/g/chi/ZZ
→ Agent/optimizer update
```

最有论文价值的指标：**在达到给定 spec tolerance 时，减少多少 HFSS/Q3D calls。**

## G2. 从 antenna full-wave optimization 迁移：surrogate + truth simulation loop
1. 初始 full-wave 样本；
2. surrogate；
3. surrogate 上廉价搜索；
4. 对最有价值候选做 full-wave；
5. 更新 surrogate；
6. 重复。

这几乎就是最适合超导器件的 active-learning 模板。

## G3. 从 AnalogCoder / OpenROAD Agent 迁移：错误恢复而不是“会写代码”
Agent 的论文贡献不应只是“能调用 Ansys”，而应有：
- geometry invalid diagnosis；
- missing material / boundary / port setup detection；
- solver non-convergence；
- mesh failure；
- license / job failure；
- output parser mismatch；
- 自动回滚到 last-known-good design；
- retry budget 与错误分类。

## G4. 从 EDAid 迁移：多 Agent 仅用于高价值分工
不要为了“multi-agent”而 multi-agent。可尝试：Planner、Simulator、Physics Critic、Recovery Agent；最终需要和 single-agent ablation 比较。

## G5. 从 photonic transfer learning 迁移：跨 geometry / fabrication stack 迁移
先用 SQuADDS/SQChip-EM 或自己粗网格数据预训练，再用少量本地 Ansys 高精度数据 fine-tune，研究数据量减少、geometry family transfer、solver fidelity transfer、substrate/material 变化下的 domain shift。

---

# H. Research Gap

## Gap 1：超导量子版图的“Agent + 真 EM solver”闭环仍缺少成熟公开基准
- 传统 EDA 已有 ChatEDA、EDAid、ORFS-agent、OpenROAD Agent。
- 超导量子侧已有 Qiskit/Quantum Metal、SQuADDS、SQChip-EM 数据与工具基础。
- 真实硬件侧已有 Vibe Calibration 这种 LLM Agent 自动校准。
- **但本轮未检索到已正式发表、完整实现 Qiskit Metal geometry → Ansys/HFSS → result parsing → autonomous redesign 的同行评审论文。**

**机会**：做“Quantum-EDA Agent Benchmark”，重点不是聊天，而是 tool reliability + optimization sample efficiency。

## Gap 2：现有 transmon inverse design 已证明可行，但仍偏单器件、低维目标
2026 transmon inverse design 已覆盖 `(f_q, alpha) → geometry`，因此“再做一个 transmon MLP”新颖性不足。

**机会**：tunable coupler、readout + Purcell filter、transmon + readout joint design、multi-qubit frequency-collision / crosstalk、fabrication/process constraints、uncertainty-aware inverse design。

## Gap 3：现有工作较少把“减少多少真实 EM 调用”作为首要目标
**机会**：固定 target spec 与 search budget，比较 random、grid/LHS、BO、pure surrogate、active learning、Agent + surrogate、Agent + BO。

## Gap 4：缺少 uncertainty-aware physics verification policy
**机会**：deep ensemble / MC dropout / GP / conformal interval / OOD detector；“uncertain → Ansys，confident → surrogate”动态路由。

## Gap 5：多保真 simulation 尚未被充分系统化到超导量子 AI 设计闭环
不同求解层级成本不同：analytic / circuit model、Q3D capacitance、HFSS eigenmode、driven modal、EPR / full postprocess。

**机会**：multi-fidelity surrogate / scheduler，让 Agent 学会“用哪一级求解器回答当前问题”。

## Gap 6：layout-level representation 仍很早期
当前很多模型直接吃少量 scalar geometry parameters；大规模 chip 的核心会变成 spatial/layout interactions。

**机会**：layout graph、geometry image/CNN、GNN、hierarchical representation（component → pairwise coupling → chip）。

---

# I. Candidate Research Ideas

## Idea 1 — Uncertainty-Aware Active Surrogate for Transmon/Readout Co-Design
- **Problem**：Ansys 数据昂贵；固定训练集 surrogate 在 inverse search 时容易超出分布。
- **Proposed Method**：Deep ensemble / GP surrogate + acquisition function；高 uncertainty 或高 expected improvement 候选进入 Ansys。
- **Workflow**：`Qiskit Metal → Ansys → dataset → surrogate → acquisition → new geometry → Ansys`
- **Baseline**：Random/LHS、BO、固定 surrogate、SQuADDS nearest retrieval。
- **Evaluation**：target hit rate、Ansys calls、wall time、MAPE、OOD failure。
- **MVP**：3–6 个 geometry variables，两个输出 `f_q, alpha` 或 `f_r, g`，200–1000 个样本。
- **发表定位**：Scientific ML / MLCAD / quantum EDA。
- **可行性**：**9.8/10**

## Idea 2 — Agentic Qiskit Metal–Ansys Closed-Loop Design Optimizer
- **Problem**：现有流程高度人工：改参数、运行、读结果、判断、重跑。
- **Proposed Method**：LLM Agent 负责 plan / tool selection / diagnosis；数值 optimizer 负责连续搜索；Ansys 是 physics authority。
- **Agent tools**：`edit_design_params`, `build_layout`, `export_gds`, `run_q3d`, `run_hfss`, `parse_results`, `check_specs`, `rollback`, `retry`。
- **Baseline**：manual scripted loop、BO、single-agent no-memory、single-agent no-recovery。
- **Evaluation**：task success、Ansys calls、invalid actions、failure recovery、wall time、final physical objective。
- **MVP**：单 transmon 或 transmon+readout，不做整芯片。
- **发表定位**：EDA / Agent systems / AI for engineering。
- **可行性**：**9.2/10**

## Idea 3 — Surrogate Inverse Design for Tunable Couplers
- **Problem**：tunable coupler 设计涉及多目标和强 trade-off，EM + circuit 参数迭代昂贵。
- **Targets**：`g_on`, `g_off`, `ZZ`, coupler frequency, collision margin`。
- **Method**：forward surrogate + tandem inverse network；或 conditional flow 生成多解。
- **Baseline**：manual sweep、BO、nearest-neighbor retrieval。
- **Evaluation**：EM re-simulation error、valid rate、on/off ratio、ZZ constraint、solver calls。
- **MVP**：先做 4–6 个几何变量 + 3–4 个目标。
- **可行性**：**8.8/10**

## Idea 4 — Hybrid Agent + Surrogate: Learn When NOT to Run Ansys
- **Problem**：Agentic closed loop 如果每一步都跑 Ansys，自动化了流程却没有降低计算成本。
- **Method**：Agent 选择 surrogate query / low-fidelity simulation / full Ansys / stop。
- **Research Question**：能否通过 confidence + expected value of information，自动决定仿真层级？
- **Baseline**：always-Ansys、fixed-period Ansys、pure BO。
- **Evaluation**：cost-normalized regret、solver calls、success rate。
- **可行性**：**9.0/10**

## Idea 5 — Multi-Fidelity Surrogate Across Q3D and HFSS
- **Problem**：Q3D 与 HFSS 的成本和可获得物理量不同。
- **Method**：multi-fidelity GP / deep network，把廉价 Q3D 大样本和昂贵 HFSS 小样本联合学习。
- **Novelty**：solver fidelity as an explicit learning dimension。
- **可行性**：**8.7/10**

## Idea 6 — Layout Graph Surrogate for Multi-Qubit Crosstalk / Frequency Crowding
- **Problem**：scalar 参数模型难处理多 qubit spatial interaction。
- **Method**：器件作为 node、距离/耦合路径作为 edge；GNN 预测 `chi_ij`, crosstalk, frequency shifts`。
- **Data**：SQChip-EM + 自生成 2–8 qubit layouts。
- **Evaluation**：跨 qubit-count generalization。
- **可行性**：**7.2/10**

## Idea 7 — Failure-Aware Autonomous Simulation Agent
- **Problem**：工程瓶颈不只在优化，也在仿真失败和人工排错。
- **Method**：建立 Ansys failure taxonomy + parser + deterministic validators + LLM diagnosis。
- **Contribution**：仿真自动恢复率、平均人工干预次数、task completion rate。
- **风险**：若只有工程集成，论文 novelty 可能不足；需要 benchmark / ablation。
- **可行性**：**9.3/10**

## Idea 8 — Transfer Learning from SQuADDS/SQChip-EM to Local Fabrication Stack
- **Problem**：公开数据与本地材料、基底、工艺、solver setup 有 domain shift。
- **Method**：预训练 public surrogate → 少量 local Ansys fine-tune；比较 scratch training。
- **Evaluation**：few-shot curves（10/25/50/100/200 samples）。
- **可行性**：**9.0/10**

---

# J. Feasibility Ranking

| Rank | 课题 | 快速上手 | 无实验硬件 | 数据可自生成 | 工程复杂度 | 创新潜力 | 发表潜力 | 总体 |
|---:|---|---:|---:|---:|---:|---:|---:|---:|
| 1 | Uncertainty-aware active surrogate | 5 | 5 | 5 | 3 | 5 | 5 | **9.8/10** |
| 2 | Failure-aware Agentic Qiskit Metal–Ansys loop | 5 | 5 | 5 | 4 | 5 | 5 | **9.2/10** |
| 3 | Hybrid Agent + surrogate scheduler | 4 | 5 | 5 | 4 | 5 | 5 | **9.0/10** |
| 4 | Public→local transfer learning | 5 | 5 | 5 | 3 | 4 | 4 | **9.0/10** |
| 5 | Tunable coupler inverse design | 4 | 5 | 4 | 4 | 5 | 5 | **8.8/10** |
| 6 | Multi-fidelity Q3D/HFSS surrogate | 4 | 5 | 5 | 4 | 5 | 5 | **8.7/10** |
| 7 | Failure-aware simulation agent | 5 | 5 | 5 | 3 | 3 | 4 | **8.5/10** |
| 8 | Layout GNN for crosstalk | 3 | 5 | 3 | 5 | 5 | 5 | **7.2/10** |

---

# 推荐的研究路线

## 路线 A：最快形成论文结果
```text
单器件 parameterized design
→ 自动批量 Ansys
→ dataset
→ RF / XGBoost / MLP baselines
→ uncertainty-aware surrogate
→ active learning
→ inverse / optimization
→ EM verification
```

**建议器件顺序：**transmon 或 readout resonator（验证 pipeline）→ tunable coupler（主要创新）→ transmon + coupler / readout joint design（扩展）。

## 路线 B：Agent 作为第二阶段增强
```text
Phase 1
确定性 Python pipeline 跑通
    ↓
Phase 2
surrogate + optimizer 提升 sample efficiency
    ↓
Phase 3
Agent 接管 workflow orchestration / diagnostics / recovery
    ↓
Phase 4
Agent 学会在 surrogate / Q3D / HFSS 之间选择
```

这样每一阶段都能独立得到可评估结果，也降低“Agent demo 很炫但论文指标不清楚”的风险。

---

# 最小可行实验（MVE）

1. 选一个已经能稳定仿真的 transmon 或 readout 结构；
2. 参数化 4–6 个 geometry variables；
3. 设计 200–500 个 Latin Hypercube / Sobol 样本；
4. 自动跑 Q3D/HFSS，保存 geometry、solver version/setup、mesh/convergence、outputs、runtime、failure reason；
5. 训练 Random Forest、MLP、Gaussian Process（可加入 XGBoost/LightGBM）；
6. 评价 MAPE/RMSE、valid prediction range、uncertainty、inference latency；
7. 做 active-learning experiment：fixed budget = 50 / 100 / 200 true simulations；比较 random、BO、uncertainty sampling、surrogate-assisted optimization；
8. 最终 EM 复验 top candidate。

**第一篇图建议：**workflow、geometry variables、surrogate accuracy、error vs training samples、target attainment vs number of Ansys calls、active learning trajectory / Pareto front。

---

# 论文池：优先阅读顺序

## 第一优先级（必须精读）
1. Component-Level Inverse Design of Transmon Qubits Using Neural Networks (2026)
2. SQChip-EM (KDD 2026)
3. SQuADDS (Quantum 2024)
4. ORFS-agent (MLCAD 2025)
5. ChatEDA (TCAD 2024)
6. EDAid (NAACL 2025)
7. AnalogCoder (AAAI 2025)
8. Vibe Calibration (2026)

## 第二优先级（方法迁移）
9. Neural-Network Inverse Design of SRF Cavities and Transmons (2026)
10. Machine-Learning-Assisted Optimization for Antenna Geometry Design (IEEE TAP 2024)
11. Deep Learning CNN for Antenna Near-Field Prediction and Surrogate Modeling (IEEE Access 2024)
12. Efficient nanophotonic device optimization with physics-based transfer learning (Scientific Reports 2025)
13. EDA-Q (2025)
14. Automated design of superconducting circuits and 4-local couplers (npj QI 2021)
15. Engineering Framework for Optimizing Superconducting Qubit Designs (2020)

---

# 关键参考文献与链接

1. Seidel, O. et al. *Component-Level Inverse Design of Transmon Qubits Using Neural Networks*. arXiv:2607.20795 (2026). https://arxiv.org/abs/2607.20795
2. Peng, Y. et al. *SQChip-EM: A Qiskit-Metal-Based Layout-to-EM Simulation Dataset for Superconducting Quantum Chips*. KDD 2026. DOI: 10.1145/3770855.3817558. https://github.com/Secbrain/SQChip-EM
3. Shanto, S. et al. *SQuADDS: A validated design database and simulation workflow for superconducting qubit design*. Quantum 8, 1465 (2024). https://quantum-journal.org/papers/q-2024-09-09-1465/
4. Yaker, J. et al. *Neural-Network Inverse Design of SRF Cavities and Transmons for Bosonic Quantum Computation*. arXiv:2607.02289 (2026). https://arxiv.org/abs/2607.02289
5. Zhao, B. et al. *EDA-Q: Electronic Design Automation for Superconducting Quantum Chip*. arXiv:2502.15386 (2025). https://arxiv.org/abs/2502.15386
6. Menke, T. et al. *Automated design of superconducting circuits and its application to 4-local couplers*. npj Quantum Information 7, 49 (2021). https://doi.org/10.1038/s41534-021-00382-6
7. Yan, F. et al. *Engineering Framework for Optimizing Superconducting Qubit Designs*. arXiv:2006.04130. https://arxiv.org/abs/2006.04130
8. Xu, H. et al. *Vibe Calibration: Autonomous Bring-up of a 112-Qubit Superconducting Quantum Processor by a Skill-Orchestrating Language Agent*. arXiv:2606.22376 (2026). https://arxiv.org/abs/2606.22376
9. Wu, H. et al. *ChatEDA: A Large Language Model Powered Autonomous Agent for EDA*. IEEE TCAD 43(10), 3184–3197 (2024). https://doi.org/10.1109/TCAD.2024.3383347
10. Wu, H. et al. *Divergent Thoughts toward One Goal: LLM-based Multi-Agent Collaboration System for Electronic Design Automation*. NAACL 2025. https://aclanthology.org/2025.naacl-long.83/
11. Lai, Y. et al. *AnalogCoder: Analog Circuit Design via Training-Free Code Generation*. AAAI 2025. https://doi.org/10.1609/aaai.v39i1.32016
12. Ghose, A. et al. *ORFS-agent: Tool-Using Agents for Chip Design Optimization*. MLCAD 2025. https://doi.org/10.1109/MLCAD65511.2025.11189204
13. Vijayaraghavan, P. et al. *AUTOCIRCUIT-RL: Reinforcement Learning-Driven LLM for Automated Circuit Topology Generation*. ICML 2025. https://proceedings.mlr.press/v267/vijayaraghavan25a.html
14. *WiseEDA: LLMs in RF Circuit Design*. Microelectronics Journal 158, 106607 (2025). https://doi.org/10.1016/j.mejo.2025.106607
15. *Machine-Learning-Assisted Optimization for Antenna Geometry Design*. IEEE TAP 72(3), 2083–2095 (2024). https://doi.org/10.1109/TAP.2023.3346493
16. Khan, M. R. et al. *A Deep Learning Convolutional Neural Network for Antenna Near-Field Prediction and Surrogate Modeling*. IEEE Access 12 (2024). https://doi.org/10.1109/ACCESS.2024.3377219
17. Kim, G. & Kim, J. *Efficient nanophotonic devices optimization using deep neural network trained with physics-based transfer learning methodology*. Scientific Reports 15, 39854 (2025). https://doi.org/10.1038/s41598-025-23519-5
18. *Enhanced qubit readout via reinforcement learning*. Phys. Rev. Applied 23, 054057 (2025). https://doi.org/10.1103/PhysRevApplied.23.054057
19. *Designing fast quantum gates using optimal control with a reinforcement-learning ansatz*. Phys. Rev. Applied 23, 014015 (2025). https://doi.org/10.1103/PhysRevApplied.23.014015
20. Di Guglielmo, G. et al. *End-to-End Workflow for Machine-Learning-Based Qubit Readout With QICK and hls4ml*. IEEE TQE (2025). https://doi.org/10.1109/TQE.2025.3604712

---

# 最终建议

如果只选一个硕士主线，推荐：

> **Uncertainty-Aware Surrogate-Assisted Closed-Loop Optimization for Superconducting Quantum Device Design**

```text
Design Spec
   ↓
Agent / Optimizer
   ↓
Surrogate (fast)
   ├── confident → evaluate candidate cheaply
   └── uncertain / high-value → Ansys
                              ↓
                         Q3D / HFSS
                              ↓
                    Physics-grounded metrics
                              ↓
                    Dataset + model update
                              ↓
                      next design decision
```

这套系统同时满足：明确 AI 方法贡献、明确 EDA/工程价值、完全可由现有仿真流程生成数据、不依赖新实验硬件、可先单器件后扩展 coupler/readout/multi-qubit，并且可自然加入 Agent，而不会让 LLM 取代真正的数值优化和物理验证。
