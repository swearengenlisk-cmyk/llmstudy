# 超导量子芯片中 λ/2 与 λ/4 CPW/SCPW 读取谐振腔：完整调研报告

> 生成日期：2026-09-19  
> 说明：本文件是本次对话中最开始生成的完整调研报告的 GitHub 版。为便于核查，我保留了原报告的主要结构、判断和参数，并把可公开访问的英文文献链接整理为普通 Markdown 链接。  
> **重要：** 本仓库同分支另有 \`superconducting-readout-resonator-literature-audit-2026-09-19.md\`，其中对部分文献的 λ/2 / λ/4 分类和证据强度做了后续复核与修正。检查本报告时请同时对照该复核文件。

---

## 1. 结论摘要

### 1.1 两篇学位论文的关键核对

#### 王晋哲：《超导量子比特的制备和表征》（2024）

王晋哲论文**不能仅因为第 2 章出现 λ/2 示意图，就断定实际 10 比特芯片使用 λ/2**。

论文第 2 章理论介绍明确把 Fig. 2-1(a)描述为“λ/2 共面波导谐振器与其电容耦合输入输出端口”，但这属于理论模型。真正的实际芯片出现在第 3 章：作者明确说这是其制备的 10 比特 Xmon 芯片，Fig. 3-1 有十个读取腔，并使用 HFSS 得到读取腔—传输线电场图。

对实际版图的判断是：

> **[推断，而非作者明示] 王晋哲实际芯片中的读取腔很可能是开路—开路型、以电场/电容方式侧耦合读取线的 λ/2 resonator。**

理由：
- 没有看到读取腔中心导体一端明确 galvanic short 到地；
- 靠读取线一端表现为非接触耦合区；
- 另一端与 Xmon 明确为电容耦合；
- 作者明确写出读取腔与量子比特“通过电容耦合”。

但论文没有一句“本芯片采用开路—开路 λ/2 读取腔”的明确文字，因此只能标记为：

**“高度可信的版图推断，非作者明示。”**

该芯片确实制备并低温测量。S21 中测得 10 个读取腔 notch，频率从 **7.2913 到 7.5108 GHz**。

#### 梁珪涵：《超导量子计算多比特芯片》（2025）

梁珪涵论文不存在上述歧义。

作者明确写出：

> **λ/4 SCPW 读取腔，开路端与比特电容耦合，短路端与读取线电感/互感耦合。**

并且作者**实际制备了这种 λ/4 SCPW 腔**，明确标注开路端和短路端：
- 仿真基频：**7.08 GHz**
- 10 mK 实测：**6.77 GHz**
- Al/sapphire
- 示例截面：εr=11.5，D=2 µm，W=4 µm，膜厚 100 nm，蓝宝石厚度 430 µm

论文还给出与你当前问题高度相关的设计流程：
- Eigenmode → 腔频
- Q3D → qubit–resonator coupling capacitance
- HFSS modal S21 → resonator–readout-line coupling
- 拟合 S21 → Qc → κR
- 建议 mesh：读取线 10 µm、读取腔 5 µm、读取腔附近地 10 µm
- 宽扫：fr±1 GHz，Interpolating，1001 points

---

## 2. λ/2 与 λ/4 的理论对比

对于准 TEM CPW：

\[
v_p=\frac{1}{\sqrt{L'C'}}
\simeq\frac{c}{\sqrt{\epsilon_{\rm eff}}},
\qquad
\beta=\frac{\omega}{v_p}.
\]

忽略端部电容、短路电感、弯折和 kinetic inductance 的一阶模型。

### 2.1 开路—开路 λ/2

\[
f_n=\frac{nv_p}{2l},
\qquad n=1,2,3,\ldots
\]

基模：

\[
l_{\lambda/2}\simeq\frac{v_p}{2f_0}.
\]

驻波：

\`\`\`text
open                center                 open
 V max  ─────────── V≈0 ────────────────  V max
 I=0    ─────────── I max ───────────────  I=0
\`\`\`

两端：
- 电压波腹
- 电流节点

因此两端特别适合**电容耦合**；中心附近适合互感耦合。

### 2.2 开路—短路 λ/4

\[
f_n=\frac{(2n-1)v_p}{4l},
\qquad n=1,2,3,\ldots
\]

基模：

\[
l_{\lambda/4}\simeq\frac{v_p}{4f_0}.
\]

\`\`\`text
open                                       short/GND
 V max  ────────────────────────────────>  V=0
 I=0    ────────────────────────────────>  I max
\`\`\`

因此理想结构自然产生两个功能不同的端点：
- 开路端 → 电压波腹 → 电容耦合
- 短路端 → 电流波腹 → 电感/互感耦合

梁珪涵论文也明确总结：
- λ/4 可在短路端以互感耦合读取线；
- 若改为电容耦合，应尽量靠近开路端；
- λ/2 开路腔两端都适合电容耦合；
- λ/2 中间位置可用于电感耦合。

### 2.3 同频下长度

近似：

\[
l_{\lambda/2}\approx2l_{\lambda/4}.
\]

因此 λ/4 的直接优势：
- 更短
- 更小
- 更适合高密度布线
- 更适合大量频分复用读取腔

近期专门针对超导量子读取腔的设计工作也把 λ/4 transmission-line resonator 作为常用 readout topology：
- Luo et al., IEEE TMTT 2025  
  https://doi.org/10.1109/TMTT.2025.3578414

---

## 3. 王晋哲 vs 梁珪涵：逐项对比

| 项目 | 王晋哲，2024 | 梁珪涵，2025 |
|---|---|---|
| 机构 | 中科院物理所 | 中科院物理所 |
| 实际制备 | 是，10-qubit Xmon | 是，多代单层/倒装，30/78/106 qubit |
| 实际低温测试 | 是 | 是 |
| 实际读取腔拓扑 | **λ/2? [推断]** | **λ/4 [明示]** |
| 边界 | [推断] 两端开路 | [明示] 一端 open，一端 short |
| resonator→qubit | [明示] 电容 | [明示] 开路端电容 CQR |
| resonator→feedline | [推断] 电容/电场耦合 | [明示] 短路端互感 MR |
| feedline 耦合驻波位置 | [推断] V-antinode | [明示+理论] short end / I-antinode |
| 测量形式 | S21 notch；另有 S11 样品 | S21 / IQ |
| 实测 cavity | 7.2913–7.5108 GHz | λ/4 test cavity 6.77 GHz |
| Qi,Qc | 讲理论，未发现逐腔数值表 | 明确通过实际曲线拟合 |
| κR | 理论关系 | 实验/设计中使用 |
| 2χ | 有 dispersive response | 后期芯片约 1.5 MHz |
| 读取 fidelity | 有 IQ cloud；数值未明确找到 | 后期芯片平均激发态 >90%，单个 >95% |
| Purcell filter | 不是主要结构 | 后期芯片明确加入 |
| 关键证据 | Fig.3-1/3-2/3-3，Fig.4-7，Table 4-1 | Fig.2-3/2-4/2-9，Fig.4-5，Fig.5-5，Fig.7-8 |

---

## 4. 文献证据表：15+ 篇

符号：
- ✓ = 实际制备并低温测量
- NR = 当前全文/可访问资料未报告
- ? = 证据不足，不强行分类

### 4.1 结构、制备与耦合

| 文献 | 实验 | Qubit | readout topology | 边界/耦合 | 备注 |
|---|---:|---|---|---|---|
| 王晋哲, 2024 | ✓ | Xmon | **λ/2? [推断]** | [推断] open-open；qubit:C；feedline:C | 实际 10q |
| 梁珪涵, 2025 | ✓ | Xmon | **λ/4 [明示]** | open→qubit C；short→feedline M | Al/sapphire；实测 6.77 GHz |
| Majer et al., Nature 2007 | ✓ | transmon | **λ/2 [明示]** | λ/2 CPW common cavity | fr=5.19 GHz，L=12.3 mm |
| Krantz et al., Nat. Commun. 2016 | ✓ | transmon | **λ/4 [明示]** | open-side capacitive line coupling；short by SQUID | JPO，不是普通 passive hanger |
| Jeffrey et al., PRL 2014 | ✓ | Xmon/transmon-family | **λ/4 [明示，后续复核修正]** | λ/4 measurement resonator + λ/4 bandpass filter | fast readout |
| Heinsoo et al., PRApplied 2018 | ✓ | transmon | **λ/4 [明示，后续复核修正]** | λ/4 readout + λ/4 Purcell filter | 5q multiplexed |
| Lienhard et al., PRApplied 2022 | ✓ | transmon | **λ/4 [明示]** | qubit capacitive；resonator inductively couples to filtered feedline | 5q，≈7 GHz |
| Opremcak et al., PRX 2021 | ✓ | transmon | **λ/2 [明示]** | half-wave measurement resonator | raw fidelity >98% |
| Hertzberg et al., npj QI 2021 | ✓ | transmon | **λ/2 [明示]** | dispersive readout through half-wave resonators | 31 qubits measured |
| Sunada et al., PRApplied 2022 | ✓ | transmon | **λ/2 [明示，后续复核修正]** | open-open half-wave，special output coupling position | intrinsic Purcell filtering |
| Teixeira et al., Sci. Rep. 2024 | ✓ | transmon | **λ/4 [明示]** | conventional dispersive readout | fRO=7.437 GHz |
| Spring et al., PRX Quantum 2025 | ✓ | transmon | **λ/4 readout + λ/4 filter** | distributed C+M coupling | linewidth up to 42 MHz |
| Luo et al., IEEE TMTT 2025 | ✓ test chip | resonator test | **λ/4 [明示]** | feedline-coupled | cryogenic fr,Qc validation |
| Bhattacharjee et al., Sci. Rep. 2025 | ✓ | transmon | **λ/2 [明示]** | individual λ/2 resonators used for control/readout | sapphire/Ta |
| Kreikebaum PhD, Berkeley 2020 | ✓ | transmon | **λ/2 readout + λ/4 Purcell** | 4 qubits share one λ/2 readout | fr=6.391 GHz |
| Wallraff et al., Nature 2004 | ✓ | CPB | early CPW-cQED | topology在本报告中不强行重分类 | foundational |

### 4.2 主要测量结果

| 文献 | fr | Qi/Qc/QL | κ/2π | 2χ/2π | readout |
|---|---:|---|---:|---:|---|
| 王 2024 | 7.2913–7.5108 GHz | 数值 NR | 数值 NR | 有 dispersive shift | IQ cloud |
| 梁 2025 | λ/4 test 6.77 GHz | 可由实测曲线拟合 | 后期约与 1.5 MHz 匹配 | ≈1.5 MHz | single >95% |
| Majer 2007 | 5.19 GHz | — | 33 MHz | — | multiplexed cavity measurement |
| Krantz 2016 | tunable，bare λ/4≈5.55 GHz | Qext=2555 | Γ0/2π=1.02 MHz | — | measured 81.5%；corrected estimate 98.7±1.2% |
| Jeffrey 2014 | multiplexed | — | — | — | 99.8%，140 ns |
| Heinsoo 2018 | multiplexed band | — | — | — | 97% avg，80 ns pulse |
| Lienhard 2022 | ≈7 GHz | — | — | — | 5q mux |
| Opremcak 2021 | measurement resonator | — | — | — | raw >98%，<500 ns |
| Sunada 2022 | λ/2 multimode engineered | — | high external decay | — | 99.1%，40 ns |
| Teixeira 2024 | 7.437 GHz | relatively low Q | NR | NR | single-shot |
| Spring 2025 | multiplexed | — | linewidth ≤42 MHz | — | 99.77%，56 ns |
| Luo 2025 | multiple λ/4 | measured Qc | calculable | N/A | model/FEM/cryo validation |
| Kreikebaum 2020 | 6.391 GHz | — | 0.816 MHz external | χ/2π≈175 kHz per q | 4-qubit shared readout |

---

## 5. 7 个最有参考价值的案例

### Case A — 梁珪涵：与你当前结构最接近

作者明确采用：

\[
\lambda/4:
\quad
\text{open}\xrightarrow{C_{QR}}\text{qubit},
\quad
\text{short}\xrightarrow{M_R}\text{feedline}.
\]

尤其关键的是论文对读取腔—读取线耦合的描述：

> 读取腔和读取线在同一布线层，并通过读取线的接地端与读取腔形成电感耦合。

这意味着并不是简单：

\`\`\`text
resonator short end  ||  feedline center strip
\`\`\`

而是 **feedline ground-return structure 参与耦合**。

因此它对你当前 S21 不可见问题最具直接参考价值。

### Case B — Lienhard / MIT：真实多比特 λ/4 + inductive feedline coupling

原文明确写：

> each qubit is capacitively coupled to a quarter-wave readout resonator that couples inductively to a bandpass-Purcell-filtered feedline.

论文：
- https://doi.org/10.1103/PhysRevApplied.17.014024
- https://link.aps.org/accepted/10.1103/PhysRevApplied.17.014024

拓扑与梁论文接近：

\`\`\`text
Qubit
  │ C
  │
open ───── λ/4 ───── short
                         )) M
                 Purcell-filter/feedline
\`\`\`

### Case C — Krantz / Chalmers：λ/4 的开路侧电容耦合

关键参数：
- Cc=11.9 fF
- Qext=2555

论文：
- https://doi.org/10.1038/ncomms11417
- https://www.nature.com/articles/ncomms11417

需要注意：这是 **λ/4 Josephson parametric oscillator**，不是普通 passive hanger readout resonator。

### Case D — Majer / Yale：经典 λ/2

关键参数：
- fr=5.19 GHz
- L=λ/2=12.3 mm
- λ/2 CPW cavity
- κ/2π=33 MHz

论文：
- https://doi.org/10.1038/nature06184
- https://www.nature.com/articles/nature06184

它证明 λ/2 两端高电压区域适合电容耦合，但该 cavity 更偏 common cavity/bus，不等同于现代每比特独立 readout resonator。

### Case E — Opremcak：现代 λ/2 measurement resonator

论文：
- https://doi.org/10.1103/PhysRevX.11.011027
- https://journals.aps.org/prx/abstract/10.1103/PhysRevX.11.011027

采用 half-wave CPW measurement resonator，实现：
- raw single-shot fidelity >98%
- 总测量时间 <500 ns

### Case F — Luo / TUM / Fraunhofer：λ/4 的 Qc 几何设计

论文：
- https://doi.org/10.1109/TMTT.2025.3578414
- https://mediatum.ub.tum.de/1795639
- https://publica.fraunhofer.de/entities/publication/450a3678-c2ee-4962-9d00-f4a9bb18211e

特点：
- feedline-coupled λ/4 CPW
- 多种 coupling geometry test chip
- cryogenic measurement
- analytical model / FEM / experiment 对比
- 重点验证 fr,Qc

### Case G — Spring 2025：电容与电感并非只能二选一

论文：
- https://doi.org/10.1103/PRXQuantum.6.020345
- https://journals.aps.org/prxquantum/abstract/10.1103/PRXQuantum.6.020345

该工作让 readout resonator 和 filter resonator 同时存在 capacitive 和 inductive coupling，利用路径相消形成 compact Purcell notch。

实现：
- linewidth up to 42 MHz
- 56 ns integration
- average assignment fidelity 99.77%

---

## 6. 真实版图中的读取腔—读取线耦合分类

### A. 开路端电容 hanger

\`\`\`text
feedline ───────────────────
               ││ Cc
               │
              open
               │
               │ λ/4
               │
             GND short
\`\`\`

特点：
- coupling point = voltage antinode
- gap / overlap / paddle / finger 控制 Cc
- S21 常表现为 notch
- Qc 易通过几何连续调节

### B. λ/2 两端电容

\`\`\`text
feedline ──||── open ===== λ/2 ===== open ──||── qubit
            Cc                         CQR
\`\`\`

两端均为电压波腹。

### C. λ/4：open→qubit，short→feedline mutual inductance

\`\`\`text
qubit
  │ CQR
 open
  │
  │ λ/4
  │
 short════ grounded current structure
        )) magnetic flux
feedline return/ground structure
\`\`\`

真正决定 M 的因素：
- short-end current magnitude
- parallel current-path length
- loop orientation
- return-current distribution
- ground-neck / shared-ground geometry
- separation
- surrounding ground
- airbridges

**两个中心导体之间距离很近，不足以证明存在有效 M。**

### D. Purcell-filter / hybrid network

\`\`\`text
qubit -- readout resonator -- [C and/or M] -- Purcell filter -- feedline
\`\`\`

必须区分：
- readout resonator
- Purcell filter
- bus resonator
- feedline

---

## 7. S21 / S11、Qc 和 κ 提取

### 7.1 Hanger / notch

理想模型：

\[
S_{21}(f)
=
1-
\frac{Q_L/Q_c}
{1+2iQ_L(f/f_r-1)}.
\]

并且：

\[
\frac1{Q_L}
=
\frac1{Q_i}+\frac1{Q_c}.
\]

因此：

\[
\boxed{
\frac{\kappa_e}{2\pi}=\frac{f_r}{Q_c}
}
\]

\[
\boxed{
\frac{\kappa_i}{2\pi}=\frac{f_r}{Q_i}
}
\]

\[
\boxed{
\frac{\kappa_{\rm tot}}{2\pi}=\frac{f_r}{Q_L}
}
\]

若采用梁论文的 κR 定义，即 resonator→readout-line leakage 且 Qi≫Qc：

\[
\boxed{
\frac{\kappa_R}{2\pi}
\simeq
\frac{f_r}{Q_c}
}
\]

实际测量最好拟合 **complex S21 circle**，并校正：
- cable delay
- amplitude slope
- phase rotation
- impedance mismatch
- asymmetric/Fano distortion

参考：
- Khalil et al., JAP 2012: https://doi.org/10.1063/1.3692073
- Probst et al., RSI 2015: https://doi.org/10.1063/1.4907935

### 7.2 Inline / band-pass

如果 resonator 串接在传输路径上，S21 可能表现为**峰**而不是 notch。

所以：

> “S21 没有 notch”只有在确认你的网络确实是 hanger topology 后，才能当成“耦合失败”的证据。

### 7.3 Reflection / S11

单端 resonator 可写成近似：

\[
S_{11}(\Delta)
=
1-\frac{2\kappa_e}
{\kappa_i+\kappa_e+2i\Delta}.
\]

线宽：

\[
\kappa_{\rm tot}=\kappa_i+\kappa_e.
\]

S11 与 S21 都可以提取 Q，但 line shape 模型不能混用。

---

## 8. 与你当前 Q1 / λ/4 结构的直接对照

当前结构：
- open end → qubit 电容
- short end → ground
- short end 靠近 feedline
- 希望形成 readout coupling
- HFSS Driven Modal 中没有稳定可信 notch

前半部分是合理的：

\[
\lambda/4,\qquad
\text{open}\rightarrow C_{QR}\rightarrow Q.
\]

问题集中在：

\[
\text{short end}\rightarrow ? \rightarrow \text{feedline}.
\]

如果中间隔着几微米连续地金属带，不能简单说“有 ground，所以是电感耦合”。

可能存在：
1. ground strip 是共同/相邻 return-current path → 可有明显 M。
2. ground strip 只是 electrostatic shield → C 被压低，同时 M 也可能很弱。
3. resonator short 与 feedline return-current loop 几乎不共享 magnetic flux → M→0，Qc→∞，S21 notch 极浅甚至不可见。

因此：

> **“短路端与读取线平行靠近”远远不够；需要证明存在相互链接的电流回路和磁通。**

---

## 9. 两套可执行设计路线

### 路线 A — 保留 λ/4，修复 short-end inductive coupling

建议：
1. open end 保持 qubit CQR
2. short end 保持真实低电感 ground termination
3. 在 short/current-antinode 附近明确构造 resonator current loop
4. 让 feedline return-current path 与之平行/相邻
5. 扫：
   - coupling length
   - gap
   - ground neck
   - relative orientation
   - shared-return geometry
6. 看 surface current / H-field，而不只是 E-field
7. Driven Modal S21 扫 Qc
8. 最后再改远离 coupler 的 resonator length 恢复 fr

优点：
- 同频长度约为 λ/2 一半
- 更适合高密度频分复用
- 理想下一模约 3fr
- 大量现代多比特实验采用类似架构

风险：
- 如果 magnetic coupling topology 本身不明确，仅继续微调 gap，风险较高。

### 路线 B — λ/2 两端电容耦合，重新设计至 6.7 GHz

如果 open-open λ/2 control：

\[
l_0=4.40483~{\rm mm}
\]

对应：

\[
f_0\simeq13.1~{\rm GHz},
\]

则一阶：

\[
l_{\rm new}
\simeq
l_0\frac{13.1}{6.7}
\simeq8.61~{\rm mm}.
\]

目标区间：

\[
f=6.6~{\rm GHz}\Rightarrow l\simeq8.75~{\rm mm}
\]

\[
f=6.8~{\rm GHz}\Rightarrow l\simeq8.49~{\rm mm}
\]

初始设计窗：

\[
\boxed{l_{\lambda/2}\approx8.5-8.8~{\rm mm}}
\]

这只是首轮 seed。

必须重新完成：
- meander
- Eigenmode
- second mode
- feedline Cc
- Qc
- qubit CQR
- g,χ
- Driven Modal S21
- Purcell
- package mode
- crosstalk
- tolerance

---

## 10. HFSS：宽带 Interpolating、局部 Discrete

### Interpolating
适合：
- 宽频搜索
- 大部分频段响应平滑
- 先定位 resonance

典型：

\[
f_{\rm expected}\pm1~{\rm GHz}
\]

梁论文也是 ±1 GHz / Interpolating / 1001-point output。

### Discrete
每个指定频点真正求场解。

适合：
- 已知 resonance
- 小范围精细扫描
- complex-circle fit
- 精确提取 QL,Qc,Qi,κ

### 特别注意
极窄高-Q resonance 可能被 Interpolating 漏掉。

所以“完全看不到 notch”时一起检查：
1. coupler 是否真的存在有效 C 或 M
2. port / reference conductor
3. current return path
4. mesh
5. interpolation basis / minimum solutions

HFSS 官方：
- https://ansyshelp.ansys.com/public/Views/Secured/Electronics/v251/en/Subsystems/HFSS/Content/HFSS/InterpolatingFrequencySweepsinHFSS.htm
- https://ansyshelp.ansys.com/public/Views/Secured/Electronics/v261/en/Subsystems/HFSS/Content/HFSS/SelectingtheSweepType.htm

---

## 11. λ/2 vs λ/4 工程差别

| 属性 | λ/4 | λ/2 |
|---|---|---|
| 同频长度 | **约 0.5×** | 约 1× |
| 面积 | 更小 | 更大 |
| qubit capacitive coupling | open end 很自然 | 两端均自然 |
| feedline capacitive coupling | 可在 open end | 两端非常自然 |
| feedline inductive coupling | short end 最自然 | center 最自然 |
| next ideal mode | ~3fr | ~2fr |
| mux 密度 | 较有利 | 较占地 |
| Qc 调试 | short-end M 依赖 current geometry | 两端 C 通常更直接 |
| TLS损耗 | 无先验必然优劣 | 无先验必然优劣 |
| current crowding | short/ground junction 需注意 | 无 λ/4 ground short |
| E-field hot spot | 一个 open end | 两个 open ends |
| parasitic capacitive coupling | 面积较小 | 长度更大，双 V-antinode |
| Purcell | 由完整 impedance 决定 | 同左 |
| litho length sensitivity | Δf/f≈−Δl/l | 同左 |
| end-loading sensitivity | short inductance + open capacitance | 两端 open capacitance |

---

## 12. 对重点问题的直接回答

1. **现代 dedicated/multiplexed readout 中，本次核对样本 λ/4 更常见。**
2. **λ/4 尺寸优势成立。**
3. **λ/2 两端电压波腹适合双端电容耦合，这个优势成立。**
4. **λ/4 靠读取线的一端没有唯一答案：capacitive hanger 常用 open end；inductive architecture 可用 short end。**
5. **short-end inductive coupling 必须有真实 current-return / magnetic-flux geometry。**
6. **几微米 ground strip 不能自动判定成电感耦合。**
7. **未找到公开论文专门发表“错误 short-end parallel geometry 导致 S21 完全不可见”的失败对照。**
8. **λ/4 更紧凑；λ/2 second mode 更近；损耗和 Purcell 不能只由 λ 标签决定。**
9. **Qc、κR 优先通过 complex S21/S11 fit 提取；κe/2π=fr/Qc。**
10. **Interpolating 用于快速搜索；Discrete 用于局部高精度拟合。**
11. **当前调试阶段，λ/2 双端电容 baseline 风险更低。**
12. **最终 λ/2 / 6.7 GHz 必须重新完成长度、meander、Eigenmode、Cc、CQR、Qc、g、χ、Purcell、package/crosstalk/tolerance。**

---

## 13. 最终推荐

### 当前阶段：优先 λ/2 双端电容 baseline

\`\`\`text
feedline
   │
  Cc
   │
open ======== λ/2 ≈ 8.6 mm ======== open
                                      │
                                     CQR
                                      │
                                    qubit
\`\`\`

目标：
- Eigenmode frequency 正确
- Driven S21 有 clean notch
- Qc 随 capacitive gap/overlap 可调
- fr/Qc 与目标 κR/2π 一致
- CQR 可得到目标 g,χ

如果这个 baseline 也没有 notch，问题更可能在：
- port
- reference conductor
- ground connectivity
- boundary
- mesh
- S-parameter topology

而不是 λ/4/λ/2 本身。

### 长期大规模版本：成熟后返回 λ/4

如果密度成为约束，则优先考虑实验上已被反复验证的：

\[
\boxed{
\text{open C-to-qubit}
+
\text{short M-to-feedline/ground-return}
}
\]

因此：

> **当前调试风险最低：λ/2 + 两端电容。**  
> **长期高密度扩展潜力更好：成熟的 λ/4 + 明确 short-end inductive coupler / Purcell network。**

---

## 14. 建议的下一轮仿真计划

本报告不启动仿真，只列计划。

### Phase 1 — λ/2 control validation
1. 继续 4.40483 mm open-open control
2. Eigenmode 确认 fundamental 是否约 13.1 GHz
3. 检查 E/H/current 驻波
4. 加明确 capacitive feedline coupler
5. Driven Modal 宽扫
6. 局部 Discrete S21
7. complex circle fit：fr,QL,Qc,Qi

### Phase 2 — 6.7 GHz λ/2
初始：
\[
8.5\text{–}8.8~{\rm mm}
\]

独立扫描：
\[
C_c\Rightarrow Q_c
\]

\[
C_{QR}\Rightarrow g,\chi
\]

### Phase 3 — λ/4 repaired control
保留约 4.4 mm 量级，重新设计 short-end coupler。

做三个结构对照：
- A：原“仅平行靠近”
- B：明确 short-end mutual-inductive coupler
- C：λ/4 open-side capacitive coupler

### Phase 4 — full-chip
最后加入：
- 多 resonator
- Xmon
- control lines
- airbridges
- package
- Purcell filter
- neighboring-mode/crosstalk

---

## 15. 尚不能充分确认的事项

1. **王晋哲实际芯片“作者明确使用 λ/2”——不能确认。**
2. 王论文实际 10-qubit 芯片的 Qi,Qc,κR 数值未找到系统表。
3. 王论文中未找到明确可引用的定量 readout fidelity。
4. 不应因为理论章节提到 λ/2 就直接断定实际芯片为 λ/2。
5. 不应把 Purcell filter、bus、readout resonator 混为一谈。
6. 未找到公开论文专门发表“错误 short-end parallel geometry 导致 S21 完全看不见”的失败对照。
7. 不存在“λ/4 的 Qi 一定高于 λ/2”或反之的一般定理。
8. 不存在“λ/2 Purcell 一定更好”的一般结论。
9. 4.40483 mm → 13.1 GHz 以及 8.49–8.75 mm 只是首轮 seed；实际长度会受 sapphire、meander mutual capacitance、kinetic inductance、end capacitance、airbridges、package 影响。

---

## 16. 完整参考文献与可访问链接

1. **王晋哲**，《超导量子比特的制备和表征》，中国科学院物理研究所，2024。  
   当前依据：本次会话用户提供的原始 PDF。

2. **梁珪涵**，《超导量子计算多比特芯片》，中国科学院物理研究所，2025。  
   当前依据：本次会话用户提供的原始 PDF。  
   团队公开页面：https://cqcp.baqis.ac.cn/Research.html

3. **Majer, J. et al.**, “Coupling superconducting qubits via a cavity bus,” *Nature* 449, 443–447 (2007).  
   https://doi.org/10.1038/nature06184  
   https://www.nature.com/articles/nature06184

4. **Krantz, P. et al.**, “Single-shot read-out of a superconducting qubit using a Josephson parametric oscillator,” *Nature Communications* 7, 11417 (2016).  
   https://doi.org/10.1038/ncomms11417  
   https://www.nature.com/articles/ncomms11417

5. **Jeffrey, E. et al.**, “Fast Accurate State Measurement with Superconducting Qubits,” *Physical Review Letters* 112, 190504 (2014).  
   https://doi.org/10.1103/PhysRevLett.112.190504  
   https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.112.190504  
   https://harvest.aps.org/v2/journals/articles/10.1103/PhysRevLett.112.190504/fulltext

6. **Heinsoo, J. et al.**, “Rapid High-fidelity Multiplexed Readout of Superconducting Qubits,” *Physical Review Applied* 10, 034040 (2018).  
   https://doi.org/10.1103/PhysRevApplied.10.034040  
   https://link.aps.org/accepted/10.1103/PhysRevApplied.10.034040

7. **Lienhard, B. et al.**, “Deep-Neural-Network Discrimination of Multiplexed Superconducting-Qubit States,” *Physical Review Applied* 17, 014024 (2022).  
   https://doi.org/10.1103/PhysRevApplied.17.014024  
   https://link.aps.org/accepted/10.1103/PhysRevApplied.17.014024

8. **Opremcak, A. et al.**, “High-Fidelity Measurement of a Superconducting Qubit Using an On-Chip Microwave Photon Counter,” *Physical Review X* 11, 011027 (2021).  
   https://doi.org/10.1103/PhysRevX.11.011027  
   https://journals.aps.org/prx/abstract/10.1103/PhysRevX.11.011027

9. **Hertzberg, J. B. et al.**, “Laser-annealing Josephson junctions for yielding scaled-up superconducting quantum processors,” *npj Quantum Information* 7, 129 (2021).  
   https://doi.org/10.1038/s41534-021-00464-5  
   https://www.nature.com/articles/s41534-021-00464-5

10. **Sunada, Y. et al.**, “Fast Readout and Reset of a Superconducting Qubit Coupled to a Resonator with an Intrinsic Purcell Filter,” *Physical Review Applied* 17, 044016 (2022).  
    https://doi.org/10.1103/PhysRevApplied.17.044016  
    https://link.aps.org/accepted/10.1103/PhysRevApplied.17.044016

11. **Teixeira, W. et al.**, “Many-excitation removal of a transmon qubit using a single-junction quantum-circuit refrigerator and a two-tone microwave drive,” *Scientific Reports* 14, 13755 (2024).  
    https://doi.org/10.1038/s41598-024-64496-5  
    https://www.nature.com/articles/s41598-024-64496-5

12. **Spring, P. A. et al.**, “Fast Multiplexed Superconducting-Qubit Readout with Intrinsic Purcell Filtering Using a Multiconductor Transmission Line,” *PRX Quantum* 6, 020345 (2025).  
    https://doi.org/10.1103/PRXQuantum.6.020345  
    https://journals.aps.org/prxquantum/abstract/10.1103/PRXQuantum.6.020345

13. **Luo, Z. et al.**, “A Versatile Analytical Model for Fast and Accurate Determination of Feedline-Coupled Resonators for Superconducting Qubit Readout,” *IEEE Transactions on Microwave Theory and Techniques* 73(10), 8059–8070 (2025).  
    https://doi.org/10.1109/TMTT.2025.3578414  
    https://mediatum.ub.tum.de/1795639  
    https://publica.fraunhofer.de/entities/publication/450a3678-c2ee-4962-9d00-f4a9bb18211e

14. **Bhattacharjee, A. et al.**, “Demonstration of two qubit entangling gates in a 2D ring resonator based coupler architecture,” *Scientific Reports* 15, 4426 (2025).  
    https://doi.org/10.1038/s41598-025-87410-z  
    https://www.nature.com/articles/s41598-025-87410-z

15. **Kreikebaum, J. M.**, “Superconducting Qubit Enabled Single Microwave Photon Detection,” PhD dissertation, UC Berkeley (2020).  
    https://escholarship.org/uc/item/0834k8bw

16. **Wallraff, A. et al.**, “Strong coupling of a single photon to a superconducting qubit using circuit quantum electrodynamics,” *Nature* 431, 162–167 (2004).  
    https://doi.org/10.1038/nature02851  
    https://www.nature.com/articles/nature02851

17. **Khalil, M. S. et al.**, “An analysis method for asymmetric resonator transmission applied to superconducting devices,” *Journal of Applied Physics* 111, 054510 (2012).  
    https://doi.org/10.1063/1.3692073  
    https://arxiv.org/abs/1108.3117

18. **Probst, S. et al.**, “Efficient and robust analysis of complex scattering data under noise in microwave resonators,” *Review of Scientific Instruments* 86, 024706 (2015).  
    https://doi.org/10.1063/1.4907935  
    https://arxiv.org/abs/1410.3365

---

## 17. 与后续复核文件的关系

本文件保留最开始生成的完整调研逻辑和工程建议。

后续独立复核文件：

\`research/superconducting-readout-resonator-literature-audit-2026-09-19.md\`

专门处理：
- 哪些文献真实存在
- 哪些拓扑分类需要修正
- 哪些文献其实是 bus / Purcell filter / JPO，而不是普通 dedicated readout
- 两篇中文论文哪些是附件原文明示、哪些只是版图推断

建议最终设计决策时，以**本完整报告 + 后续复核文件**联合使用。
