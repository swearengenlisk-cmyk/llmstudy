# Lienhard / MIT 与 Luo / TUM-Fraunhofer 超导量子读取谐振腔深度调研

> 日期：2026-09-19  
> 目标：针对两篇用户提供的论文，核对公开资源、提取可复现的器件/读取参数，重点解释 λ/4 读取谐振腔—读取线耦合理论，并判断其中哪些因素可能解释当前 HFSS Driven Modal 中 S21 谐振凹口不明显的问题。  
> 本报告仅进行文献分析，不运行 HFSS，不修改仿真代码。  
> 论文页码以下优先使用论文印刷页码/图表编号，同时注明上传 PDF 中的位置。

---

# 0. 结论摘要

## 0.1 Lienhard / MIT：对“读出系统工作点”很有价值，但不是一篇可复刻版图的硬件设计论文

论文：

**B. Lienhard et al., “Deep-Neural-Network Discrimination of Multiplexed Superconducting-Qubit States,” Physical Review Applied 17, 014024 (2022).**  
DOI: https://doi.org/10.1103/PhysRevApplied.17.014024  
APS: https://journals.aps.org/prapplied/abstract/10.1103/PhysRevApplied.17.014024  
Accepted manuscript: https://link.aps.org/accepted/10.1103/PhysRevApplied.17.014024  
arXiv: https://arxiv.org/abs/2102.12481

**明确可确认的硬件拓扑：**

- 5 个频率可调 transmon；
- 每个 qubit **电容耦合到一个 λ/4 readout resonator**；
- readout resonator **电感耦合到一个 bandpass / Purcell-filtered common feedline**；
- 5 个 readout resonator 约为 7.06–7.25 GHz，邻近谐振器约隔 50 MHz；
- 论文给了 qubit–resonator coupling \(g\)、dispersive shift \(\chi\)、有效衰减率 \(\kappa_{\mathrm{eff}}\)、critical photon number \(n_{\mathrm{crit}}\)；
- 但**没有给出** CPW 中心线宽、缝隙、谐振器物理总长、短路端—feedline 并行长度、耦合间距、有限地带宽度、Purcell filter 物理尺寸、GDS、HFSS/COMSOL 工程文件或 readout coupler 的几何参数表。

所以，对你当前问题而言：

> **Lienhard 是“真实 λ/4 + 电感型输出耦合 + Purcell filter 的实验成功案例”，但不是“如何把这个电感耦合结构画出来”的参数化设计论文。**

截至本次检索，我没有找到由论文作者或论文页面明确链接的 GDS、HFSS/COMSOL 工程、原始器件版图仓库、raw readout data 或 PyTorch 训练仓库。能确认的公开文件包括 APS/CHORUS accepted manuscript、arXiv，以及 Benjamin Lienhard 的 MIT 博士论文：

**Machine Learning Assisted Superconducting Qubit Readout**  
MIT DSpace: https://hdl.handle.net/1721.1/140024

博士论文是开放可读的补充背景材料，但当前上传的 PRApplied 论文本身并不足以精确复刻读取腔几何。

---

## 0.2 Luo / TUM-Fraunhofer：这篇对你当前 HFSS 问题非常重要

论文：

**Z. Luo et al., “A Versatile Analytical Model for Fast and Accurate Determination of Feedline-Coupled Resonators for Superconducting Qubit Readout,” IEEE Transactions on Microwave Theory and Techniques 73(10), 8059–8070 (2025).**  
DOI: https://doi.org/10.1109/TMTT.2025.3578414  
TUM: https://mediatum.ub.tum.de/1795639  
Fraunhofer: https://publica.fraunhofer.de/entities/publication/450a3678-c2ee-4962-9d00-f4a9bb18211e  
arXiv（2026 后补上传）: https://arxiv.org/abs/2607.00490

这篇工作不仅给理论，还给了**足够具体的几何参数、HFSS 对照和低温测试**。最关键的是，它直接研究：

> **一根 λ/4 CPW resonator 与 feedline 通过一段平行耦合区耦合，而两者之间恰好隔着有限宽度的 ground strip。**

这与你现在“读取腔和读取线之间隔着几微米接地带、短路端附近希望形成电感耦合”的问题高度相关。

论文里有几个对你特别关键的结论：

1. **耦合不能只用“电容”或“电感”二选一来理解。**  
   并行 coupled-line section 同时具有 even/odd modes；完整耦合由两条 transmission line 的偶/奇模阻抗 \(Z_{0,e}, Z_{0,o}\)、耦合段电长度、以及 λ/4 两端边界共同决定。

2. **耦合段放在短路端时主要是电感耦合，但在其研究几何中，它明显比开路端电容耦合弱。**  
   这意味着“短路端只是靠近 feedline”非常可能对应一个很高的 \(Q_c\)，S21 notch 极浅，甚至在普通宽扫中几乎不可见。

3. **读取腔—feedline 中间的有限宽 ground strip 是一个核心设计变量，不是无关背景金属。**  
   Luo 的测试设计只把这个宽度 \(d\) 从 2 µm 改到 10 µm，\(Q_c\) 就能从约 \(1.7\times 10^4\) 变到约 \(1.2\times 10^5\)；加上 open-end coupling pad 后可从约 \(3.1\times10^4\) 变到约 \(2.0\times10^5\)。

4. **窄 ground strip 若没有被足够“钉”在同一地电位，会产生 parasitic / spurious modes。**  
   论文明确指出：两侧大地可通过 indium bumps 等做等电位连接，但 resonator 与 feedline 之间那条很窄的 ground strip 太窄，放不下典型 10–20 µm 的 bump，导致 grounding 不充分，形成寄生效应并显著影响 \(Q_c\)。  
   这与你现在几微米地带的结构高度相关。

5. **open-end 的 qubit coupling pad 会同时改变 \(f_r\) 和 \(Q_c\)。**  
   不能先对“裸 λ/4 线”调好 \(Q_c\)，然后再加一个大 qubit pad 并假定 \(Q_c\) 不变。

6. **kinetic inductance 会使实际频率下降。**  
   论文 150 nm Nb 的估算 \(L_k/L_g\approx3.1\%\)，导致约 2% 的频率下移。你的薄膜 Al/SCPW 情况不能直接照抄这个 2%，但“HFSS 纯 PEC/经典模型频率偏高”的方向必须考虑。

7. **端口/过渡本身也会产生假谐振。**  
   实验里约 7.6 GHz 的额外 dip 被归因于 planar CPW 与 top-grounded CPW 之间的 mismatch。对 HFSS 来说，若 port/reference-conductor/地回流过渡不干净，也可能产生“假 dip”，反过来也可能把目标谐振掩盖。

我的判断是：

> 对你目前“λ/4 短端靠近 readout line 但 S21 看不到可靠 notch”的问题，Luo 论文暴露出的最高优先级疑点不是 sweep 类型，而是 **耦合段实际电气位置、有限地带的接地完整性、even/odd-mode coupling 强度、完整 open-end pad loading 和端口回流路径**。

---

# 1. Lienhard / MIT 工作详细复核

## 1.1 论文实际研究重点

这篇论文的核心目标是比较 superconducting-qubit readout 的 discriminator：

- matched filter；
- single-qubit linear SVM；
- multi-qubit SVM；
- fully-connected feedforward neural network。

硬件是为了提供一个真实的 5-qubit multiplexed-readout testbed，而不是为了给出 resonator geometry design recipe。

因此它对你的价值主要是：

1. 证明 λ/4 + inductive coupling + Purcell-filtered feedline 是实际可工作的；
2. 给出一个很明确的 **7 GHz 多路复用工作点参数窗口**；
3. 给出 \(\chi/\kappa_{\mathrm{eff}}\) 和 resonator spacing 对 crosstalk 的真实实验背景；
4. 但它不适合作为“HFSS 耦合段尺寸模板”。

---

## 1.2 实际器件拓扑

论文 Fig. 1（上传 PDF 第 3 页，论文页 2）给出 optical micrograph 和 circuit schematic。

作者明确写：

> each qubit is capacitively coupled to a quarter-wave readout resonator that couples inductively to a bandpass (Purcell) filtered feedline.

因此这不是根据版图推断，而是**作者明示**：

\[
\text{transmon}
\xleftrightarrow{C}
\lambda/4~\text{readout resonator}
\xleftrightarrow{M}
\text{bandpass/Purcell-filtered feedline}.
\]

这和你想做的架构方向是一致的。

但需要注意：

- 图里画的是“电感耦合拓扑关系”；
- 不是一个标注尺寸的 fabrication drawing；
- 论文没有把 short-end coupling region 单独放大并给参数。

---

## 1.3 制备信息

Appendix B 明确给出：

- substrate：**(001) high-resistivity silicon，>3500 Ω·cm**
- metal：**MBE-grown aluminum**
- patterning：optical lithography + dry etch
- die：**5 × 5 mm²**

这与你的 sapphire + Al 结构并不完全相同。

因此它的具体耦合常数不能直接迁移，但拓扑和系统工作点可以参考。

---

## 1.4 qubit 参数：Table III

| Qubit | idle \(f_q\) GHz | biased \(f_q\) GHz | \(\alpha/2\pi\) MHz | \(T_1\) µs | \(T_{2R}\) µs | \(T_{2E}\) µs |
|---|---:|---:|---:|---:|---:|---:|
| Q1 | 5.249 | 5.092 | -212 | 40.8 | 1.3 | 7.4 |
| Q2 | 4.708 | 4.404 | -216 | 6.4 | 0.6 | 4.1 |
| Q3 | 5.202 | 5.000 | -204 | 21.4 | 1.0 | 7.2 |
| Q4 | 4.560 | 4.309 | -214 | 11.8 | 0.8 | 5.4 |
| Q5 | 5.196 | 5.165 | -200 | 23.4 | 7.6 | 31.8 |

另外：

- nearest-neighbor capacitive coupling 设计值 \(J_{nn}/2\pi\approx14\) MHz；
- next-nearest \(J_{nnn}/2\pi<1\) MHz；
- qubit operating point 通过 detuning 降低 qubit-qubit/control crosstalk。

---

## 1.5 readout 参数：Table IV

这是这篇论文对你最有价值的数据表。

| Resonator | \(f_r\) GHz | IF MHz | \(g/2\pi\) MHz | \(\chi/2\pi\) MHz | \(\kappa_{\mathrm{eff}}/2\pi\) MHz | \(n_{\mathrm{crit}}\) |
|---|---:|---:|---:|---:|---:|---:|
| R1 | 7.06 | -65 | 116.3 | 0.83 | 4.29 | 33.8 |
| R2 | 7.10 | -26 | 143.3 | 0.51 | 4.25 | 55.3 |
| R3 | 7.15 | 24 | 125.7 | 0.77 | 4.41 | 34.9 |
| R4 | 7.20 | 70 | 133.1 | 0.49 | 3.33 | 56.9 |
| R5 | 7.25 | 127 | 125.4 | 0.80 | 6.90 | 33.0 |

如果仅为了获得一个“有效 loaded/coupling-like Q 尺度”的直观量，可以计算：

\[
Q_{\mathrm{eff}}\equiv \frac{f_r}{\kappa_{\mathrm{eff}}/2\pi}.
\]

得到约：

| Resonator | \(Q_{\mathrm{eff}}\) |
|---|---:|
| R1 | 1646 |
| R2 | 1671 |
| R3 | 1621 |
| R4 | 2162 |
| R5 | 1051 |

**但必须特别强调：**

> 论文的 \(\kappa_{\mathrm{eff}}\) 是 **through the Purcell filter 的 effective resonator decay rate**。  
> 它不是“裸 λ/4 resonator 直接对 50 Ω feedline 的 \(f_r/Q_c\)”的简单同义词。

因此不要把上面 \(Q_{\mathrm{eff}}\) 当成你当前无 Purcell-filter 简化结构的目标 \(Q_c\) 直接照搬。

---

## 1.6 \(\chi/\kappa\) 工作点

Fig. 1 给出五个 resonator 的 \(\chi/\kappa_{\mathrm{eff}}\)：

- 0.19
- 0.12
- 0.17
- 0.15
- 0.12

这比“严格 \(|2\chi|=\kappa\)”的 textbook matching 更宽松。

说明实际多路复用系统的读取优化不仅取决于单个 resonator 的最大 distinguishability，还会受到：

- multiplex spacing；
- amplifier bandwidth；
- photon population；
- crosstalk；
- discriminator；
- pulse shape；
- qubit \(T_1\)

共同约束。

论文指出：为了快速读取要增大 linewidth \(\kappa\)，但更宽的 resonator 会增加相邻 resonator 的频谱重叠，从而增加 readout crosstalk。

这点对你未来多比特 readout frequency plan 很重要：

> 不能只把单个 resonator 的 \(Q_c\) 做得越低越好；多路复用时还必须给相邻 cavity 足够频率间隔。

---

## 1.7 Lienhard 到底有没有“具体设计尺寸”？

### 有：

- qubit/readout operating frequencies；
- \(g\)、\(\chi\)、\(\kappa_{\mathrm{eff}}\)、\(n_{\mathrm{crit}}\)；
- substrate / metal / die size；
- λ/4 topology；
- inductive output coupling；
- shared Purcell-filtered feedline；
- neighboring resonator spacing ~50 MHz。

### 没有：

在上传论文中没有找到以下可复刻数据：

- CPW center width；
- CPW slot/gap；
- λ/4 centerline length；
- meander pitch / meander spacing；
- short-end galvanic termination dimensions；
- resonator–feedline separation；
- parallel coupling length；
- finite ground strip width；
- exact mutual-inductance loop dimensions；
- Purcell filter physical line width/gap/length；
- external \(Q_c\) 单独表；
- HFSS/COMSOL readout model；
- GDS；
- lithography mask；
- S-parameter design sweep。

所以如果目标是“把 Lienhard 的 short-end inductive coupler 原样建进 HFSS”，**仅靠这篇论文不够**。

---

## 1.8 Lienhard 的公开资源情况

### 已确认公开

1. APS 正式页面  
   https://journals.aps.org/prapplied/abstract/10.1103/PhysRevApplied.17.014024

2. CHORUS accepted manuscript  
   https://link.aps.org/accepted/10.1103/PhysRevApplied.17.014024

3. arXiv  
   https://arxiv.org/abs/2102.12481

4. Benjamin Lienhard 2021 MIT PhD thesis  
   **Machine Learning Assisted Superconducting Qubit Readout**  
   https://hdl.handle.net/1721.1/140024

### 本次没有找到

在论文正文、APS 页面、arXiv 页面、MIT publication record，以及 GitHub 关键词检索中，没有找到该工作明确链接的：

- GDS；
- HFSS project；
- COMSOL readout project；
- CAD mask；
- raw IQ dataset；
- paper-specific PyTorch repository；
- Zenodo dataset。

“没有找到”不等价于“绝对不存在”，但截至本次检索，**没有一个由论文官方入口指向的硬件设计仓库**。

---

# 2. Luo / TUM-Fraunhofer 工作详细复核

## 2.1 这篇论文为什么与你当前问题高度匹配

Luo 研究的并不是抽象 LC resonator，而是：

- λ/4 CPW resonator；
- two-port feedline；
- 一段有限长度的 coupled CPW section；
- resonator 与 feedline 之间隔着一条**有限宽 ground plane**；
- 可同时处理 planar 与 flip-chip；
- 从几何直接计算 \(S_{21}\)、\(f_r\)、\(Q_c\)；
- 再用 HFSS 3D full-wave 和低温测试验证。

论文 Fig. 1 把 λ/4 resonator 拆成四段：

1. short-end section；
2. coupling section；
3. open-end section；
4. microwave stub / ending pad。

这正好提示一个常见错误：

> **不能把“总长 λ/4”当成唯一参数；coupling section 在驻波上的位置、本身电长度和 open-end pad 都必须进入模型。**

---

## 2.2 Luo 的真实结构参数：Table I

论文使用的基础 CPW/stack-up：

| 参数 | 值 |
|---|---:|
| resonator center width \(w_r\) | 10 µm |
| resonator gap \(g_r\) | 9 µm |
| feedline center width \(w_{fl}\) | 10 µm |
| feedline gap \(g_{fl}\) | 9 µm |
| Nb thickness \(t\) | 150 nm |
| chip separation \(h_s\) | 10 µm |
| top substrate thickness \(h_t\) | 525 µm |
| bottom substrate thickness \(h_b\) | 525 µm |
| substrate \(\epsilon_r\) | 11.45 |
| substrate | bulk silicon |
| superconducting metal | Nb |

与你当前设计对比：

| 项目 | 你的设计 | Luo |
|---|---:|---:|
| center width | ~10 µm | 10 µm |
| CPW gap | ~5 µm | 9 µm |
| substrate \(\epsilon_r\) | sapphire ~11.5 | Si 11.45 |
| topology | 目前更接近单层 planar | top-grounded flip-chip |
| target \(f_r\) | 6.6–6.8 GHz | test chip 约 5.2–8.0 GHz |
| ground strip between line/resonator | 几 µm | \(d=2\)–10 µm tested |

介电常数和中心线宽很接近，但：

> **不要直接复制 Luo 的 \(Q_c(d)\) 数值。**  
> 其 top ground、10 µm inter-chip gap 和 9 µm CPW slots 会显著改变 even/odd-mode fields。

真正值得迁移的是**设计规律和建模框架**。

---

# 3. Luo 的理论：为什么“靠得近”不是一个完整耦合模型

## 3.1 第一步：把平行耦合段当成四端口 coupled transmission line

coupling section 有两根相邻 CPW：

- feedline；
- readout resonator。

因此它不是“一个 lumped \(C_c\)”或“一个 lumped \(M\)”就能完整描述。

对于两条相邻传输线，至少有两个基本准 TEM 模：

- even mode；
- odd mode。

它们分别有：

\[
Z_{0,e},\qquad Z_{0,o}
\]

以及相应 effective dielectric constant。

如果两条线完全不耦合：

\[
Z_{0,e}\approx Z_{0,o}.
\]

耦合增强时，两者差异增大。

因此，coupler 的本质信息之一就是：

\[
\Delta Z_0 = Z_{0,e}-Z_{0,o}.
\]

论文用 conformal mapping 从横截面直接得到 even/odd mode 的 partial capacitance，再得到：

\[
\epsilon_{\mathrm{eff},e/o}
\]

和：

\[
Z_{0,e/o}.
\]

**对你的启示：**

如果短路端只是“视觉上靠近”读取线，但中间地结构使 even/odd fields 几乎一样，则：

\[
Z_{0,e}\approx Z_{0,o}
\]

意味着耦合本身可能非常弱。

这比“间距是多少 µm”更接近真正的物理判断。

---

## 3.2 第二步：有限 ground strip 宽度 \(d\) 直接进入 even/odd mode

Luo 并不是把 resonator 与 feedline 中间的地看成无限大 ground。

他们明确把中间 ground strip 的 lateral width 定义为：

\[
d.
\]

同时还有 top-ground distance：

\[
h_s.
\]

这两个量显著改变 \(Z_{0,e}\)、\(Z_{0,o}\)。

Fig. 6 对 \(d=1\)–20 µm 和不同 \(h_s\) 做了系统 sweep。

论文结论：

- \(d\) 增大；
- 两条 CPW 的场相互作用减弱；
- even/odd impedance 都逐渐接近孤立 CPW 的特征阻抗；
- 因而耦合减弱；
- \(Q_c\) 上升。

因此：

> 你现在读取腔与读取线之间那条“几微米地带”不是一个可忽略的隔离结构，而可能正是决定 \(Q_c\) 的第一优先级几何参数之一。

---

## 3.3 第三步：coupled section 的长度也进入模型

coupling section 的电长度：

\[
\theta=\beta l_c.
\]

因此并不是只有 lateral gap / ground width 决定 coupling。

同样的横截面，如果：

\[
l_c
\]

太短，整体耦合仍然可能非常弱。

Luo 的基准分析使用：

\[
l_c=400~\mu m.
\]

这给你一个量级上的警示：

> 如果你现在短端只与读取线平行相邻几十微米，而不是数百微米尺度，那么即使局部场存在互感，综合耦合也可能远弱于预期。

不能直接把 400 µm 当成你的目标，但需要认真检查你当前有效 parallel current-overlap length 到底是多少。

---

## 3.4 第四步：必须把 λ/4 的 short/open boundary conditions 加回来

coupled-line 本身只是一个四端口网络。

Luo 接下来把：

- short-end section 长度 \(l_s\)；
- open-end section 长度 \(l_o\)

分别作为终端加载到 coupler 的另外两个端口。

在特征阻抗匹配近似下：

短路端反射：

\[
\Gamma_s=-e^{-j2\beta l_s},
\]

开路端反射：

\[
\Gamma_o=+e^{-j2\beta l_o}.
\]

然后把 4-port coupler 缩减成 feedline 上看到的 2-port network，最终得到完整 \(S_{21}\)。

这个步骤非常关键。

它告诉我们：

> **同一段完全相同的几何 coupler，只要放到 λ/4 resonator 的不同电气位置，其 \(Q_c\) 就不同。**

所以不能从 isolated coupler 的 mutual capacitance 或 mutual inductance 单独判断最终的 notch。

---

## 3.5 第五步：短端/开端不是二元开关，而是耦合的连续混合

Luo 用：

\[
l_t=l_s+l_c+l_o
\]

改变 coupled section 在整个 λ/4 resonator 上的位置。

论文 Fig. 7 的结果：

- 当 \(l_s=0\)：coupling section 就在 short end → **predominantly inductive coupling**
- 当 \(l_o=0\)：coupling section 就在 open end → **predominantly capacitive coupling**
- 当 \(l_s\neq0\) 且 \(l_o\neq0\)：**mixed capacitive + inductive coupling**

而在他们研究的具体几何中：

> capacitive coupling generally provides stronger coupling strength than inductive coupling.

这与你当前问题直接对应：

### 你的假设

“短路端是 current antinode，因此只要靠近 readout line 就应有强耦合。”

### Luo 给出的修正

“short end 确实偏向 inductive coupling，但其强度仍由 coupled-line even/odd modes、有效平行长度、有限地宽、return-current path 和边界位置共同决定；在其样品几何中 short-end inductive coupling 甚至明显弱于 open-end capacitive coupling。”

因此你的 S21 几乎没有 notch，**完全可能是一个真实的超高 \(Q_c\) / 过弱耦合结果，而不一定是 HFSS 数值错误。**

---

# 4. Luo 测试芯片：最有价值的几何—\(Q_c\) 对照

## 4.1 Table II：无 qubit coupling pad 的 5 个 λ/4 resonator

统一：

- \(l_c=400\) µm
- \(l_s=578.5\) µm

| Res | \(l_o\) µm | \(l_t\) µm | \(d\) µm | pad | \(f_r\) model / sim GHz | \(Q_c\) model / sim |
|---|---:|---:|---:|---|---:|---:|
| 1 | 3101.5 | 4080 | 2 | No | 8.01 / 8.05 | 17.1k / 16.4k |
| 2 | 3316.5 | 4295 | 4 | No | 7.61 / 7.65 | 30.7k / 27.5k |
| 3 | 3556.5 | 4535 | 6 | No | 7.21 / 7.25 | 50.7k / 45.7k |
| 4 | 3821.5 | 4800 | 8 | No | 6.81 / 6.85 | 79.8k / 72.3k |
| 5 | 4121.5 | 5100 | 10 | No | 6.41 / 6.45 | 122.1k / 105.6k |

注意：

- 这里每一行同时改了 \(d\) 和 \(l_t\) 来得到不同频率，因此不是“纯 \(d\) 单变量实验”；
- 但是趋势极清晰：ground strip 更宽、相互场更弱时，\(Q_c\) 大幅上升。

对于你而言最重要的不是复制数值，而是认识到：

> \(Q_c\) 对几微米级 ground/coupling geometry 可以是高度敏感的。

---

## 4.2 加上 open-end bifurcated qubit coupling pad 后

pad 几何：

- \(l_{\mathrm{stub}}=267\) µm
- \(w_{\mathrm{stub}}=80\) µm
- \(g_{\mathrm{stub}}=5.5\) µm
- \(g_0=55\) µm

对应 Res. 6–10：

| Res | \(l_t\) µm | \(d\) µm | pad | \(f_r\) model / sim GHz | \(Q_c\) model / sim |
|---|---:|---:|---|---:|---:|
| 6 | 4080 | 2 | Yes | 6.16 / 6.21 | 30.7k / 30.2k |
| 7 | 4295 | 4 | Yes | 5.92 / 5.96 | 54.1k / 43.3k |
| 8 | 4535 | 6 | Yes | 5.67 / 5.71 | 86.9k / 82.2k |
| 9 | 4800 | 8 | Yes | 5.42 / 5.45 | 132.9k / 129.3k |
| 10 | 5100 | 10 | Yes | 5.16 / 5.20 | 197.3k / 186.0k |

这张表对你的设计非常重要：

### 例：Res. 1 → Res. 6

物理线长和 coupling section 主体相同，但加 open-end pad 后：

\[
f_r:\quad 8.01 \to 6.16~\text{GHz}
\]

同时：

\[
Q_c:\quad 17.1k \to 30.7k.
\]

所以大 pad 不只是“给 qubit 一个耦合电容”。

它实际上是 resonator 的分布式 microwave loading。

---

# 5. 为什么 open-end pad 会同时改变 \(f_r\) 和 \(Q_c\)

Luo 把 bifurcated ending pad 建模成：

> 两个并联的 open-circuited microwave stubs。

因为 pad 长度通常几百微米，在 6–8 GHz 下仍满足：

\[
l_{\mathrm{stub}}<\lambda/10,
\]

所以可以用 electrically short open-stub approximation。

stub admittance 加载 open boundary，改变：

\[
\Gamma_o.
\]

而完整 \(S_{21}\) 又同时依赖：

\[
\Gamma_s,\Gamma_o,S^{\mathrm{coupler}}.
\]

所以：

> open-end pad 改了反射相位/等效电长度，也会改变 coupler 在整个驻波中的相对电气位置，从而改变 \(Q_c\)。

### 对你当前 HFSS 的直接检查

如果你现在：

1. 先仿真 bare λ/4；
2. 调到 6.7 GHz；
3. 再加 qubit pad / coupling capacitor；
4. 但仍用原来的 short-end coupler geometry；
5. 期待 \(Q_c\) 不变；

那么这个假设并不安全。

应当在**包含最终 open-end qubit geometry 的完整 resonator**中重新提取 \(f_r,Q_c\)。

---

# 6. Luo 揭示的“几微米 ground strip”问题

这是本次调研中对你最重要的一点。

论文明确说：

- side grounds 可以用 indium bumps 来 equalize voltages；
- 这样能抑制 spurious modes；
- 但 resonator 和 feedline 之间那条 finite ground plane 很窄；
- 典型 bump 直径约 10–20 µm，放不下；
- 因此该窄区域 **lack of sufficient grounding**；
- 会产生 parasitic effects；
- 直接影响提取的 \(Q_c\)。

这意味着：

> “它画成 ground metal”并不保证它在微波上就是完美、无条件的 RF ground。

特别是对于你的单层 CPW：

若几微米地带：

- 很长；
- 两侧没有 airbridge / wirebond / ground stitching；
- 被读取腔和 feedline 两个 gap 夹成一条细长金属岛/窄颈；
- return current 需要绕很远才能回到主 ground；

那么它可能支持：

- slotline-like mode；
- common mode；
- 局部浮地电位；
- 非预期 return-current path。

这种情况下，你原本想要的“short-end mutual inductance”可能并没有形成预期的电流回路。

反而可能得到：

- 电场耦合被地带屏蔽；
- 磁场耦合又因为 return path 不对而很弱；
- 再叠加寄生 slotline；
- 最终 S21 中目标 notch 非常浅或形态异常。

---

# 7. Luo 的理论流程，用工程语言重写

完整设计逻辑可简化为：

## Step 1 — 先解决横截面

输入：

- resonator width \(w_r\)
- resonator gap \(g_r\)
- feedline width \(w_{fl}\)
- feedline gap \(g_{fl}\)
- finite ground width \(d\)
- substrate \(\epsilon_r\)
- substrate thickness
- top-ground distance（若有）
- conductor thickness

输出：

\[
Z_{0,e}, Z_{0,o}, \epsilon_{\mathrm{eff},e},\epsilon_{\mathrm{eff},o}.
\]

### 对 HFSS 的对应

这是你可以用 Q2D / 2D electrostatic/eigenmode cross-section 做 sanity check 的层级。

如果这里就发现：

\[
Z_{0,e}\approx Z_{0,o},
\]

说明耦合很弱。

---

## Step 2 — 再加入 coupled length

输入：

\[
l_c
\]

得到 coupled-line 4-port network：

\[
S^{\mathrm{coupler}}.
\]

耦合段太短，即使横截面局部场很强，总 coupling 仍然不足。

---

## Step 3 — 加 λ/4 两边的电长度

输入：

\[
l_s,\quad l_o.
\]

应用：

\[
\Gamma_s,\Gamma_o.
\]

这一步决定同一个 coupler 是：

- 主要 inductive；
- 主要 capacitive；
- mixed。

---

## Step 4 — 加 open-end pad

若实际有 qubit coupling paddle/stub，则修改：

\[
\Gamma_o\to\Gamma_o^{\mathrm{stub}}.
\]

这一步会同时改变：

- \(f_r\)
- \(Q_c\)

不能省。

---

## Step 5 — 形成 feedline 上的完整 \(S_{21}\)

把 four-port network 加入 short/open boundary 后变成 two-port network。

最终从：

\[
S_{21}(f)
\]

拟合：

- \(f_r\)
- \(Q_l\)
- \(Q_c\)
- \(Q_i\)

而不是试图用某个局部场强直接等同于 \(Q_c\)。

---

# 8. Luo 的 HFSS 验证与误差来源

## 8.1 有限金属厚度

conformal mapping 主模型假定：

\[
t=0.
\]

实际/仿真：

\[
t=150~\mathrm{nm}.
\]

有限厚度产生 fringing-field 修正，导致：

\[
Z_{0,e/o}
\]

与理想模型出现系统偏移。

当他们把仿真也改成 \(t=0\) 时，模型与仿真的 \(Z_0\) 差异降到约 0.5 Ω 以内。

### 对你

若你的目标是判断“notch 为什么不存在”，金属厚度通常不是第一嫌疑。

它更像是：

- \(Q_c\) 精度；
- frequency；
- impedance

的二阶修正。

---

## 8.2 spurious modes / grounding

模型与 HFSS 的 \(Q_c\) 误差来源之一就是：

> narrow finite ground plane is insufficiently grounded.

这是非常高优先级。

如果你的几微米 ground strip 也存在类似问题，可能不只是“Q 值偏一点”，而是模态性质本身改变。

---

## 8.3 弯折和 transition 使实际有效 coupling length 增大

Luo 的 analytic model 把 coupling section 与其它段之间的连接简化成 right-angle。

实际版图是 arc-shaped transition。

这个弧形部分会有效增加：

\[
l_{c,\mathrm{eff}}.
\]

结果：

- 实际/simulation coupling 更强；
- \(Q_c\) 更低。

### 对你

如果你只用 nominal “平行段长度”判断 coupling，可能低估或高估真实值。

应该以：

- current overlap；
- field overlap；
- 实际 transition

定义有效 coupler。

---

## 8.4 kinetic inductance

测试器件：

- Nb thickness = 150 nm
- penetration depth 估算 \(\lambda_m\approx80\) nm
- \(L_k\approx12\) nH/m
- \(L_g\approx388\) nH/m

因此：

\[
L_k/L_g\approx3.1\%.
\]

对应 fundamental frequency 约有：

\[
\sim2\%
\]

向低频移动。

### 对你的 Al/SCPW

不能复制 3.1% 这个数。

但必须接受：

> 如果 HFSS 用 PEC / perfect E，而实际超导线有 non-negligible kinetic inductance，低温实测频率会比纯几何模型更低。

对于你现在**仿真本身看不到 notch**的问题，kinetic inductance 不太可能是根因；但如果你只在很窄频率范围扫，它可能让你扫错窗口。

---

# 9. Luo 的实验测量：对 HFSS 后续校准有什么意义

实验采用 flip-chip test chip 和低温 VNA。

典型 Res. 6：

\[
f_r=6.165~\text{GHz}
\]

\[
Q_c\approx19.9\times10^3
\]

由测量拟合得到。

模型/仿真对应 Qc 更高，实验偏差的重要来源之一是实际 chip spacing：

\[
h_s=10.5\pm1~\mu m.
\]

对 Res. 6，\(h_s\) 波动可造成：

- \(f_r\) 最大变化约 150 MHz；
- \(Q_c\) 最大变化约 \(8\times10^3\)。

这说明：

> \(Q_c\) 往往比 \(f_r\) 更敏感于局部耦合几何和制造误差。

你的目标如果是 \(\kappa/2\pi\sim1\) MHz 量级：

\[
Q_c \sim \frac{6.7~\mathrm{GHz}}{1~\mathrm{MHz}}
\sim 6700.
\]

而 Luo 测试器件很多是 \(Q_c\sim10^4\)–\(10^5\)。

这进一步说明：

> 如果你复制一种偏弱的 short-end / finite-ground inductive coupler，很容易落入 \(Q_c\gg10^4\) 的区域；在 S21 幅值中 notch 就可能非常浅。

---

# 10. 由 Luo Table II 换算一个直观 \(\kappa_e\) 尺度

按：

\[
\frac{\kappa_e}{2\pi}=\frac{f_r}{Q_c}
\]

用 model 的 \(f_r,Q_c\) 做数量级换算。

## 无 open-end pad

| Res | model \(f_r\) GHz | model \(Q_c\) | 计算得到 \(\kappa_e/2\pi\) MHz |
|---|---:|---:|---:|
| 1 | 8.01 | 17.1k | ~0.468 |
| 2 | 7.61 | 30.7k | ~0.248 |
| 3 | 7.21 | 50.7k | ~0.142 |
| 4 | 6.81 | 79.8k | ~0.085 |
| 5 | 6.41 | 122.1k | ~0.052 |

## 有 open-end pad

| Res | model \(f_r\) GHz | model \(Q_c\) | 计算得到 \(\kappa_e/2\pi\) MHz |
|---|---:|---:|---:|
| 6 | 6.16 | 30.7k | ~0.201 |
| 7 | 5.92 | 54.1k | ~0.109 |
| 8 | 5.67 | 86.9k | ~0.065 |
| 9 | 5.42 | 132.9k | ~0.041 |
| 10 | 5.16 | 197.3k | ~0.026 |

这些 \(\kappa_e\) 是**本报告根据论文 \(f_r/Q_c\) 计算**，不是论文直接报告。

它们很直观地说明：

- 一个看起来“已经耦合”的几何；
- 其 linewidth 可以只有几十 kHz；
- 如果 HFSS 宽扫分辨率/插值模型不合适；
- 或者背景 S21 ripple 更大；
- notch 很容易被忽略。

这也是你“Driven Modal 里看不到可信 notch”的一个现实可能。

---

# 11. Luo 的 S21 拟合：不要只看 dB 凹口深度

理想 hanger：

\[
S_{21}^{\mathrm{notch}}(f)
=
1-
\frac{Q_l/Q_c}
{1+2jQ_l(f/f_r-1)}.
\]

实验中论文进一步写成带环境项的形式，包括：

- overall attenuation \(a\)；
- phase \(\alpha\)；
- cable/electrical delay \(\tau\)；
- mismatch phase \(\phi\)。

这提醒你：

### 在 HFSS 里也不要只检查 \(|S_{21}|_{\rm dB}\)

尤其 weak coupling 时建议同时看：

- Re/Im \(S_{21}\)
- phase
- complex circle
- group delay

因为非常浅的 amplitude notch 可能仍然对应清楚的 phase rotation。

如果连 complex S21 都完全没有 resonant circle/phase feature，再更有力地说明 target mode 没有被 ports 有效耦合。

---

# 12. 论文中一个容易忽略的“假 dip”案例

Luo 实验的宽带 S21 中存在一个额外约 7.6 GHz dip。

作者把它归因于：

> planar CPW 与 top-grounded CPW 之间过渡的 impedance mismatch。

这对你的 HFSS 很重要：

如果模型有：

- wave/lumped port transition；
- port 截面与实际 CPW 不一致；
- port reference conductor 不完整；
- ground connection 突变；
- airbox/terminal transition；
- package/launch discontinuity；

就可能得到非 resonator 本征的 dip。

所以：

> “有 dip”并不自动说明读到 cavity；  
> “没看到预期 dip”也必须先排除 port / transition 背景。

真正可靠的确认应结合：

- resonance 处的 resonator stored energy；
- surface current；
- E/H field；
- complex S21 pole/notch；
- 对 resonator length 的可预测频移。

---

# 13. 最可能是你当前仿真遗漏的部分：按优先级排序

## 高优先级 1 — 你把“短路端电流波腹”误等同成“自动强电感耦合”

理论上：

- short end = current antinode；

但工程上还需要：

- mutual flux；
- common/current-return proximity；
- sufficient parallel length；
- proper orientation；
- finite-ground-mediated mode coupling。

如果 short end 只是几何上靠近 feedline，但 current loops 不互链：

\[
M\approx0.
\]

这时 S21 几乎不可见是正常结果。

---

## 高优先级 2 — 中间那条几微米 ground strip 的 RF grounding 可能不成立

这是 Luo 论文最直接的警告。

检查：

- 这条地是否为一个细长 island？
- 是否只在很远位置连接大 ground？
- resonator 两侧地与 feedline 两侧地是否同电位？
- 是否有 airbridge / wirebond / crossover 提供短 RF return？
- HFSS 中是否真的连通？
- 是否形成 slotline mode？

如果存在 ground-potential imbalance，耦合类型和 Qc 都可能完全偏离直觉。

---

## 高优先级 3 — 有效 coupling length 太短

Luo 使用：

\[
l_c=400~\mu m.
\]

你的 current-overlap/parallel length 若远短于这个量级，需要警惕 Qc 过高。

不是说“必须 400 µm”，而是必须通过 sweep 或 even/odd impedance check 确认。

---

## 高优先级 4 — 忽略 coupler 在 λ/4 上的“电气位置”

同一横截面：

- 直接 short end → inductive dominant；
- 直接 open end → capacitive dominant；
- 中间 → mixed。

若你所称“短路端耦合”实际还离 galvanic short 有一段明显电长度，它可能已经是 mixed coupling。

甚至电容和电感贡献还可能部分抵消。

---

## 高优先级 5 — 没把最终 qubit/open-end pad 放进 \(Q_c\) 仿真

Luo Table II 证明 pad 可显著改：

- \(f_r\)
- \(Q_c\)

所以必须对“最终完整 readout resonator”提取 Qc。

---

## 高优先级 6 — port/reference return path 不对

Driven Modal 中：

- feedline 两端的 port mode；
- reference ground；
- waveport cross-section；
- port 到 device 的 return-current path

必须与真实 CPW 一致。

若端口激发的是混合/slotline/common mode，目标 readout resonator 可能根本没有被有效驱动。

---

## 中优先级 7 — 你只看 S21 dB，没有检查 complex S21 / phase / group delay

若 Qc 很大：

- amplitude dip 可能极浅；
- phase response仍可能明显。

先确认 complex-plane 是否存在 resonant circle。

---

## 中优先级 8 — sweep 频率窗口受 kinetic inductance / pad loading 偏移

你的 sapphire \(\epsilon_r\approx11.5\) 与 Luo 的 11.45 很接近，但：

- geometry 不同；
- material Al vs Nb；
- SCPW kinetic inductance 不同；
- qubit pad loading 不同。

因此不能只围绕简单 \(\lambda/4\) 计算值窄扫。

---

## 中优先级 9 — 弯折/arc 使实际 coupling length 与名义值不同

Luo 明确发现 actual arc transition 使 coupling 更强。

你的 GDS 中：
- rounded bend
- taper
- ground neck
- corner

都会影响有效 coupler。

---

## 较低优先级 10 — 150 nm / 100 nm 金属厚度本身

会影响 even/odd impedance 和精确 Qc，但如果 notch 完全不可见，通常先查前面 1–9 项。

---

# 14. 对你的 w≈10 µm / gap≈5 µm / sapphire 结构的直接建议

## 14.1 不要照抄 Luo 的 d→Qc 表

Luo：

- 10/9 µm CPW；
- Si \(\epsilon_r=11.45\)；
- 10 µm top-ground distance；
- flip-chip；
- Nb。

你：

- 10/5 µm CPW；
- sapphire \(\epsilon_r\approx11.5\)；
- 很可能无 10 µm top-ground plane；
- Al/SCPW。

因此 exact Qc 不可直接移植。

---

## 14.2 但可照搬其“分层验证思路”

### 层 1：cross-section sanity check

对你的 coupling region 建一个 2D cross-section：

提取：

\[
Z_{0,e},\quad Z_{0,o}.
\]

重点看：

\[
|Z_{0,e}-Z_{0,o}|.
\]

如果差极小，说明“视觉上近，但电磁耦合弱”。

---

### 层 2：coupler-only coupled-line model

固定横截面，扫：

- ground strip width；
- gap；
- parallel length。

确认 coupling 能随这些参数单调变化。

---

### 层 3：完整 λ/4 boundary

再加入：

- real short；
- complete open-end pad；
- final meander。

提取完整 S21。

---

### 层 4：full chip / package

最后再加入：

- qubit body；
- neighboring resonators；
- airbridges；
- control lines；
- package。

这样可以区分：

- coupler 本身弱；
- ground mode 问题；
- full-chip parasitic。

---

# 15. 与 λ/2 双端电容 control 的关系

Luo 论文并不说明你应该放弃 λ/4。

恰恰相反，它说明：

> λ/4 short-end inductive coupling 是一种需要把 coupled-line mode、地回流和边界条件认真设计的结构。

所以你当前 λ/2 open-open control 仍然非常有价值：

## 如果 λ/2 + 明确 capacitive feedline coupler 可以稳定看到 S21 notch

说明：

- ports 基本可用；
- feedline 基本可用；
- S21 extraction 流程基本可用；
- λ/4 的问题更集中在 short-end coupler / ground topology。

## 如果 λ/2 capacitive control 也看不到 notch

则应优先排查：

- port setup；
- terminal/reference；
- ground connectivity；
- driven solution setup；
- mesh；
- sweep。

这比继续盲调 λ/4 gap 更有效。

---

# 16. Lienhard 与 Luo 的互补关系

| 维度 | Lienhard / MIT | Luo / TUM-Fraunhofer |
|---|---|---|
| 主要目标 | multiplexed readout / discriminator | resonator geometry → \(f_r,Q_c,S_{21}\) |
| 实际 qubits | 是，5 transmons | test resonators + architecture model |
| λ/4 readout | 是 | 是 |
| inductive coupling | 作者明确 | 理论上系统分析 |
| Purcell filter | 有 | 主模型不依赖 filter |
| 具体 \(g,\chi,\kappa\) | 很详细 | 重点是 \(f_r,Q_c\) |
| CPW width/gap | 未给 | 给：10/9 µm |
| coupling strip width | 未给 | \(d=2\)–10 µm test |
| coupling length | 未给 | 400 µm |
| open pad dimensions | 未给 | 267/80/5.5/55 µm |
| theory of \(Q_c\) | 非重点 | 核心内容 |
| HFSS validation | 未给 readout design sweep | 有 3D HFSS |
| cryogenic validation | 有完整 qubit readout | 有 resonator test chip |
| 可用于复刻几何 | 低 | 高 |
| 对你 S21 failure 的直接帮助 | 中等 | **很高** |

最合理的使用方式是：

- 用 **Lienhard** 定目标系统尺度：7 GHz、几 MHz \(\kappa_{\rm eff}\)、~50 MHz multiplex spacing、\(g\sim100\) MHz；
- 用 **Luo** 设计/诊断实际 feedline–resonator coupling geometry。

---

# 17. 两项工作的公开文件/代码结论

## 17.1 Lienhard

### 有

- APS / CHORUS paper
- arXiv
- MIT PhD thesis

### 未找到

- GDS
- HFSS
- COMSOL readout model
- raw data
- paper-specific ML code repo

因此：

> “开源论文/博士论文”是有的；  
> “开源器件设计工程文件”目前没有找到。

---

## 17.2 Luo

论文自身：

- CC BY 4.0；
- TUM / Fraunhofer 有公开全文；
- arXiv 版本后续公开。

截至本次检索，没有找到论文作者明确发布的：

- analytical-model source code；
- HFSS project；
- Q2D project；
- GDS；
- measurement raw data。

但是它的**关键理论前身之一**确实有公开代码：

### Besedin & Menushenkov 2018

**Quality factor of a transmission line coupled coplanar waveguide resonator**  
EPJ Quantum Technology 5, 2 (2018)  
https://doi.org/10.1140/epjqt/s40507-018-0066-3

论文明确写出：

- example calculations；
- 3D EM simulation results；
- source code

公开在：

https://github.com/ooovector/cpw_coupling

还有在线 calculator：

https://smm.misis.ru/CPW-resonator-coupling/

这个 repo 是目前找到的最接近 Luo 理论框架、且真正公开 source code 的起点。

注意：

> Besedin 2018 更偏 conventional planar TL-coupled CPW；  
> Luo 2025 进一步扩展到 finite ground、top-grounded flip-chip、open-end microwave stub 和更多 stack-up。

所以不能把 Besedin code 直接当 Luo 的实现，但非常适合做你的 independent analytical sanity check。

---

# 18. 建议你下一轮 HFSS 前先做的“零仿真”版图审核清单

这里不启动仿真，只列检查项。

## A. ground connectivity

- resonator 两侧地是否真的属于同一个 metal net？
- feedline 两侧地是否同一个 net？
- 中间几微米 ground strip 两端是否有短、宽的 ground connection？
- 是否存在细长 ground island？
- 是否需要 airbridge？
- short end 的 return current 到底沿哪里闭合？

## B. coupling location

标出 λ/4 从 real short 到 real open 的中心线坐标。

计算 coupling section 中心距 short 的：

\[
x/l.
\]

不要只用“看起来在短端”。

## C. coupling length

记录真正 parallel 的长度：

\[
l_c.
\]

包括：

- straight parallel section；
- arc/taper 贡献。

## D. finite ground width

在 coupling section 全程记录：

\[
d(x).
\]

若不是常数，也不要只记录最小值。

## E. open-end pad

把：

- qubit pad；
- finger；
- fork；
- terminal flare

作为 resonator 的一部分，而不是“外部小扰动”。

## F. port topology

对每个 Driven Modal port 明确：

- signal conductor；
- reference conductor；
- return current；
- mode count；
- port cross-section 是否完整覆盖 CPW fields。

---

# 19. 如果下一轮只做三个对照模型，优先做什么

## Model A — λ/2 open-open + open-end capacitive feedline coupling

作用：

> 验证 Driven Modal、ports、S21 extraction 本身。

## Model B — λ/4 + open-end capacitive feedline coupling

作用：

> 验证同一 λ/4 resonator 在强电压位置能否产生可见 notch。

如果 B 成功而当前 short-end M 失败：

> 基本锁定是 short-end coupler 的物理强度/回流问题，而不是 λ/4 本身。

## Model C — λ/4 + redesigned short-end inductive coupler

按照 Luo 的思想显式定义：

- finite ground width \(d\)
- coupling length \(l_c\)
- short-end offset \(l_s\)
- airbridge / ground stitching
- real open-end pad

再扫 Qc。

---

# 20. 对“为什么你当前 S21 失败”的最终判断

根据两篇论文，尤其 Luo，当前最值得优先验证的因果链是：

\[
\text{窄地带 + 短端耦合拓扑}
\]

↓

\[
\text{even/odd coupling 很弱或 ground return 不理想}
\]

↓

\[
M_{\rm eff}\text{ 很小 / parasitic mode 出现}
\]

↓

\[
Q_c\text{ 很大}
\]

↓

\[
\kappa_e/2\pi=f_r/Q_c\text{ 很小}
\]

↓

\[
|S_{21}|\text{ notch 很浅、很窄或形态异常}
\]

↓

\[
\text{Interpolating 宽扫中难以识别，无法稳定拟合 }Q_c.
\]

这个解释比“HFSS 不能仿 λ/4”或“短路端电感耦合理论上不成立”更符合现有证据。

---

# 21. 推荐阅读顺序

## 第一优先级

1. Luo 2025：
   - Fig. 1：结构分段；
   - Section II-A/B/C：four-port + boundary + stub；
   - Fig. 6：even/odd impedance vs \(d,h_s\)；
   - Fig. 7：coupling location vs \(Q_c\)；
   - Table I：基础截面；
   - Table II：test designs；
   - experimental discussion：spurious grounding、kinetic inductance、chip-gap sensitivity。

2. Lienhard 2022：
   - Fig. 1：真实 λ/4 inductive + Purcell topology；
   - Appendix B；
   - Table III；
   - Table IV。

## 第二优先级

3. Besedin & Menushenkov 2018：
   - planar coupled CPW analytical theory；
   - open GitHub implementation。

4. Li et al. 2023：
   - flip-chip resonator design；
   - conformal mapping；
   - frequency/Qc vs chip spacing。

论文：
https://arxiv.org/abs/2305.05502  
DOI:
https://doi.org/10.1109/TQE.2023.3302371

---

# 22. 参考链接

## Lienhard

- DOI: https://doi.org/10.1103/PhysRevApplied.17.014024
- APS: https://journals.aps.org/prapplied/abstract/10.1103/PhysRevApplied.17.014024
- Accepted manuscript: https://link.aps.org/accepted/10.1103/PhysRevApplied.17.014024
- arXiv: https://arxiv.org/abs/2102.12481
- MIT PhD thesis: https://hdl.handle.net/1721.1/140024

## Luo

- DOI: https://doi.org/10.1109/TMTT.2025.3578414
- TUM: https://mediatum.ub.tum.de/1795639
- Fraunhofer: https://publica.fraunhofer.de/entities/publication/450a3678-c2ee-4962-9d00-f4a9bb18211e
- arXiv: https://arxiv.org/abs/2607.00490

## Luo 的重要理论前身

- Besedin & Menushenkov 2018:
  https://doi.org/10.1140/epjqt/s40507-018-0066-3
- Open source:
  https://github.com/ooovector/cpw_coupling
- Online calculator:
  https://smm.misis.ru/CPW-resonator-coupling/

## Flip-chip resonator 参考

- Li et al. 2023:
  https://arxiv.org/abs/2305.05502
- DOI:
  https://doi.org/10.1109/TQE.2023.3302371

---

# 23. 证据等级与限制

### Lienhard

**高可信作者明示：**
- λ/4 readout；
- qubit capacitive coupling；
- readout resonator inductive coupling to Purcell-filtered feedline；
- Table III/IV 参数。

**论文未提供：**
- readout coupler fabrication dimensions；
- GDS / EM files。

### Luo

**高可信作者明示/表格：**
- CPW dimensions；
- finite ground width；
- coupling section length；
- resonator section lengths；
- ending pad dimensions；
- model/simulation \(f_r,Q_c\)；
- actual cryogenic validation；
- spurious-grounding explanation；
- kinetic-inductance estimate。

### 针对你结构的推断

“你当前 S21 不可见很可能与 short-end coupling 太弱、ground return 不理想或 parasitic mode 有关”是：

> **基于 Luo 理论与其仿真/实验误差机制，对你的结构作出的工程推断。**

它不是 Luo 对你具体 HFSS 文件的直接结论。

在看到你当前 Q1 的 GDS/HFSS geometry、ports、ground connectivity 和 field/current plots 之前，不能把根因唯一确定为某一项。
