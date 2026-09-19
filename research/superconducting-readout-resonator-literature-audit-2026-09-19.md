# 超导量子芯片读取谐振腔文献复核记录

- 复核日期：2026-09-19
- 复核对象：此前关于 `λ/2` / `λ/4` CPW/SCPW 读取谐振腔报告中提到的论文、学位论文和拟合方法文献
- 复核标准：优先核对出版社/DOI/高校论文库/作者机构全文；不仅确认“文献存在”，还重新核对文献是否真的支持此前赋予它的 **读取腔拓扑、耦合方式和实验结果**。
- 状态定义：
  - **A：独立公开来源确认，且此前关键引用基本准确**
  - **B：文献真实，但此前对其用途/拓扑的表述需要补充或修正**
  - **C：附件原文可核验，但本次公开互联网检索未找到稳定的官方题录/全文入口**

## 结论先行

本次逐篇复核后，**没有发现此前列出的英文文献是“虚构论文”或 DOI 对不上题目的情况**。Majer 2007、Krantz 2016、Jeffrey 2014、Heinsoo 2018、Lienhard 2022、Opremcak 2021、Hertzberg 2021、Sunada 2022、Teixeira 2024、Spring 2025、Luo 2025、Bhattacharjee 2025、Kreikebaum 2020、Wallraff 2004、Khalil 2012、Probst 2015 均能找到真实对应文献或学位论文。

但是，复核发现此前报告里有几处需要 **纠正证据强度或重新分类**：

1. **Jeffrey et al., PRL 2014**：不仅 Purcell/bandpass filter 是 `λ/4`，论文 Fig. 1 正文也明确把 **measurement resonator r** 写成 `λ/4`。此前把它列为“readout+filter、简单 λ 分类证据不足”过于保守，应改为 **实际制备的 λ/4 measurement/readout resonator + λ/4 bandpass filter**。
2. **Heinsoo et al., PRApplied 2018**：正文明确写出 **readout resonators 和 Purcell filters 都是 λ/4**，而 qubit-qubit coupling resonators 才是 λ/2。此前没有把其读取腔明确归为 λ/4，属于漏标。
3. **Sunada et al., PRApplied 2022**：正文明确说器件由 transmon 与 **half-wavelength resonator** 构成，并且谐振器 **两端开路**。此前说“不宜硬分简单 λ/2/λ/4”不够准确。应改成：**开路—开路 λ/2 readout/reset resonator，但其输出耦合点不是简单放在端点，而是利用分布式多模特性形成 intrinsic Purcell filter**。
4. **Majer et al., Nature 2007**：文献确实是 λ/2 CPW cavity，且可进行 multiplexed readout；但其核心角色是 **cavity bus/common cavity**，不是现代“每个比特一只专用读取腔”的直接样板。因此它能证明 λ/2 的物理和读出可行性，但对“专用 readout resonator 版图选型”的权重应降低。
5. **Krantz et al., Nat. Commun. 2016**：是真实 λ/4 结构，且 transmission-line coupling capacitor、`Q_ext` 等数据明确；但它是 **Josephson parametric oscillator (JPO)**，不是普通线性 hanger readout resonator。可以用于验证 λ/4 开路侧电容耦合几何，但不能直接等价成普通被动读取腔。
6. **Kreikebaum 2020 博士论文**：λ/2 readout resonator 确认无误，但它是 **4 个 transmon 共用一个 λ/2 readout resonator**，不是每比特独立 λ/2 读取腔。
7. **Teixeira et al., Sci. Rep. 2024**：λ/4 readout resonator 和 7.437 GHz 数据真实；但论文主线是 QCR reset，不是读取腔耦合/Qc 设计论文，作为几何案例的权重应中等。
8. “**λ/4 在现代多比特专用读取腔中更常见**”仍有较强文献支持，尤其 Luo 2025 摘要直接写出 superconducting quantum chips commonly utilize λ/4 transmission-line resonators as readout circuits；但这不是一项系统统计调查。因此更严谨的表述应是：**在本次核对的现代专用/复用读取案例中 λ/4 明显更常见，并且有近期设计论文明确称其为常用方案；不应把它写成经过全领域统计证明的比例结论。**

---

## 1. 两篇中文学位论文

### 1.1 王晋哲，《超导量子比特的制备和表征》，2024

**状态：C — 附件原文可核验；独立公开题录未找到。**

附件本身是一篇完整硕士论文，扉页信息为：

- 王晋哲
- 《超导量子比特的制备和表征》
- 中国科学院物理研究所
- 工程硕士，材料与化工
- 2024 年 6 月
- 指导教师：孙亮研究员

附件中可以直接核对：

- PDF 第 31–32 页（论文页码 20–21），Fig. 3-1：作者明确说以本人制备的 **10 比特 Xmon 超导量子芯片** 为例，图中有十个读取腔。
- PDF 第 32 页，Fig. 3-2：HFSS 读取腔—传输线电场分布。
- PDF 第 32–33 页，§3.1.2 / Eq. (3-4)：作者明确写 **读取腔与量子比特通过电容耦合**。
- PDF 第 48 页（论文页码 37），Fig. 4-7 / Table 4-1：实际 S21 读取腔测量，10 个读取频率为 7.2913–7.5108 GHz。

**需要保留的限制：** 全文中检索到的明确“λ/2”只出现在理论章节的 Fig. 2-1 介绍中；没有找到作者对实际 10 比特芯片写出“本芯片读取腔为 λ/2”的句子。因此此前对实际版图“开路—开路 λ/2”的判断只能保留为 **版图推断**，不能升级成作者明确陈述。

本次公开网页检索未找到该论文的稳定 UCAS/CNKI 官方题录页面，所以不能声称已获得第二个独立公开来源。这里应把“附件原文已核验”和“互联网独立题录验证”分开。

---

### 1.2 梁珪涵，《超导量子计算多比特芯片》，2025

**状态：C（论文附件本身证据很强；公开题录未直接找到，但作者/团队身份获得外部佐证）。**

附件扉页：

- 梁珪涵
- 《超导量子计算多比特芯片》
- 中国科学院物理研究所
- 凝聚态物理博士
- 2025 年 6 月
- 指导教师：范桁研究员

附件中的关键内容是 **作者明示**，不是版图猜测：

- PDF 第 33–34 页，Fig. 2-3：作者写“我们在实验中制备了 λ/4 SCPW 腔”，明确标注开路端/短路端；仿真基频 7.08 GHz，10 mK 实测 6.77 GHz。
- PDF 第 35 页：读取腔为 **λ/4 SCPW**；开路端与比特电容耦合，短路端与读取线电感耦合。
- 论文 §2.2.5：等效电路明确写开路端通过 `C_QR` 耦合 qubit、短路端通过互感 `M_R` 耦合 readout line。
- PDF 第 115–116 页，Fig. 4-5：HFSS modal S21、读取线/读取腔/附近地网格、±1 GHz Interpolating 1001 points、拟合 `Q_c` 再求 `κ_R`。
- PDF 第 157 页：第二版芯片加入 Purcell filter，`|2χ|/2π≈1.5 MHz`，基本满足 `|2χ|=κ_R`，单个读取保真度可超过 95%。

公开网页没有检索到同名学位论文题录，但北京量子信息科学研究院“智能量子计算与模拟团队”页面明确把 **梁珪涵** 列在“新构型多比特超导量子芯片设计与制备”团队成员中，中国科学院物理所人员页面也列有梁珪涵参与的多比特量子芯片相关专利。这是作者/研究方向的外部一致性证据，但 **不是论文题录的替代品**。

团队页面：
- https://cqcp.baqis.ac.cn/Research.html
- https://iop.cas.cn/rcjy/yjdwfgj/?id=3448

---

## 2. 英文实验论文与学位论文逐篇复核

| # | 文献 | 真实对应文献 | 读取腔/相关结构复核 | 对此前报告的处理 |
|---|---|---|---|---|
| 1 | Majer et al., 2007 | **A** | λ/2 CPW cavity，5.19 GHz，长度 12.3 mm；可 multiplexed readout | 文献和 λ/2 数据正确，但核心是 cavity bus/common cavity，不应当作现代每比特专用 readout 的最高权重证据 |
| 2 | Krantz et al., 2016 | **A/B** | tunable λ/4 JPO；transmission line 端 `C_c=11.9 fF`，`Q_ext=2555`；qubit 也在该端附近 | λ/4/电容耦合数据正确；必须注明它是 JPO，不是普通线性 hanger |
| 3 | Jeffrey et al., 2014 | **A/B** | **measurement resonators 明确为 λ/4**；bandpass filter 也是 λ/4 | 此前“readout λ 类型不足”应修正为明确 λ/4 |
| 4 | Heinsoo et al., 2018 | **A/B** | **readout resonators 与 Purcell filters 均 λ/4**；coupling resonators λ/2 | 此前未明确归类读取腔，应修正为 λ/4 |
| 5 | Lienhard et al., 2022 | **A** | 每个 transmon 电容耦合 **quarter-wave readout resonator**，再 **inductively** 耦合 bandpass/Purcell-filtered feedline；约 7 GHz | 此前结论正确，是“λ/4 + 短端/电感型读出链路”强证据 |
| 6 | Opremcak et al., 2021 | **A** | half-wave CPW measurement resonator；裸频 5.693/5.825 GHz；实验 readout | 此前 λ/2 分类正确 |
| 7 | Hertzberg et al., 2021 | **A** | 36-qubit test vehicle；通过 **half-wave microwave resonators** 色散读出，低温测得 31 qubits | 此前 λ/2 读取案例正确 |
| 8 | Sunada et al., 2022 | **A/B** | **half-wavelength、两端开路** resonator；通过改变 output coupler 位置形成 intrinsic Purcell filtering | 此前“不宜简单分类”应修正为明确 λ/2；特殊之处是耦合点位置/多模干涉 |
| 9 | Teixeira et al., 2024 | **A** | quarter-wave readout resonator，`f_RO=7.437 GHz`；另有 4.671 GHz λ/4 auxiliary resonator | 数据正确，但论文主线是 QCR reset，readout 几何证据权重中等 |
| 10 | Spring et al., 2025 | **A** | λ/4 readout resonator + λ/4 filter resonator；MTL 同时产生分布式电容和电感耦合 | 此前“C+M 并存、42 MHz、56 ns、99.77%”正确 |
| 11 | Luo et al., 2025 | **A** | IEEE TMTT 73(10), 8059–8070；test chip 制备并低温测量多种 λ/4 resonator，比较 `f_r,Q_c` | 文献真实；注意 arXiv 版本 2026 才上传，不影响 2025 journal publication |
| 12 | Bhattacharjee et al., 2025 | **A** | 实际制备 sapphire/Ta 6-transmon 芯片；其中 3 qubits 用各自 **λ/2 resonator** 同时做 control/readout；约 40 mK | 此前 λ/2 案例正确 |
| 13 | Kreikebaum, 2020 PhD | **A/B** | 4 transmons **共享** λ/2 CPW readout，6.391 GHz，`κ_ext/2π=816 kHz`；配 λ/4 Purcell filter | 数值正确；需强调是 shared λ/2 readout，不是每比特独立 |
| 14 | Wallraff et al., 2004 | **A** | circuit QED 早期真实 CPW cavity 实验 | 文献真实；此前没有强行给 λ/2/λ/4 分类是正确做法 |
| 15 | Khalil et al., 2012 | **A** | 非理想端口/阻抗不匹配下的 asymmetric resonator transmission 分析 | 用于提醒简单 Lorentzian/Q 拟合会偏置，引用正确 |
| 16 | Probst et al., 2015 | **A** | complex scattering circle fit、diameter correction、noise-robust `Q_i/Q_c` 提取 | 用于 S 参数复拟合，引用正确 |

---

## 3. 可直接检查的真实文献入口

### 3.1 λ/2 / half-wave 实验案例

1. **Majer, J. et al. “Coupling superconducting qubits via a cavity bus.” Nature 449, 443–447 (2007).**  
   DOI: https://doi.org/10.1038/nature06184  
   出版社: https://www.nature.com/articles/nature06184  
   **复核注：** λ/2 CPW cavity 真实，但主要是 common cavity bus。

2. **Opremcak, A. et al. “High-Fidelity Measurement of a Superconducting Qubit Using an On-Chip Microwave Photon Counter.” Phys. Rev. X 11, 011027 (2021).**  
   DOI: https://doi.org/10.1103/PhysRevX.11.011027  
   APS: https://journals.aps.org/prx/abstract/10.1103/PhysRevX.11.011027  
   **复核注：** 论文/作者全文明确写 half-wave CPW measurement resonator。

3. **Hertzberg, J. B. et al. “Laser-annealing Josephson junctions for yielding scaled-up superconducting quantum processors.” npj Quantum Information 7, 129 (2021).**  
   DOI: https://doi.org/10.1038/s41534-021-00464-5  
   全文: https://www.nature.com/articles/s41534-021-00464-5  
   **复核注：** Results 明确说用 half-wave microwave resonators 做 dispersive readout，低温测量 31 qubits。

4. **Sunada, Y. et al. “Fast Readout and Reset of a Superconducting Qubit Coupled to a Resonator with an Intrinsic Purcell Filter.” Phys. Rev. Applied 17, 044016 (2022).**  
   DOI: https://doi.org/10.1103/PhysRevApplied.17.044016  
   APS: https://journals.aps.org/prapplied/abstract/10.1103/PhysRevApplied.17.044016  
   Accepted manuscript: https://link.aps.org/accepted/10.1103/PhysRevApplied.17.044016  
   **复核注：** 论文明确：half-wavelength transmission-line resonator, open at both ends。40 ns readout、99.1% fidelity。

5. **Bhattacharjee, A. et al. “Demonstration of two qubit entangling gates in a 2D ring resonator based coupler architecture.” Scientific Reports 15, 4426 (2025).**  
   DOI: https://doi.org/10.1038/s41598-025-87410-z  
   全文: https://www.nature.com/articles/s41598-025-87410-z  
   **复核注：** Device design 正文明确：三个 qubits 分别耦合到 individual λ/2 resonators used for both qubit control and readout。

6. **Kreikebaum, J. M. “Superconducting Qubit Enabled Single Microwave Photon Detection.” PhD dissertation, UC Berkeley (2020).**  
   学位论文页: https://escholarship.org/uc/item/0834k8bw  
   **复核注：** Chapter 5 明确 `λ/2 CPW resonator at 6.391 GHz, κ_ext/2π=816 kHz serves as the readout resonator for all qubits`；它是四比特共享读取腔。

### 3.2 λ/4 / quarter-wave 实验案例

7. **Jeffrey, E. et al. “Fast Accurate State Measurement with Superconducting Qubits.” Phys. Rev. Lett. 112, 190504 (2014).**  
   DOI: https://doi.org/10.1103/PhysRevLett.112.190504  
   APS: https://journals.aps.org/prl/abstract/10.1103/PhysRevLett.112.190504  
   Full text: https://harvest.aps.org/v2/journals/articles/10.1103/PhysRevLett.112.190504/fulltext  
   **关键修正：** Fig. 1 caption 直接写 “voltage antinode of the λ/4 measurement resonator r”；filter 也为 λ/4。99.8% / 140 ns 数据真实。

8. **Heinsoo, J. et al. “Rapid High-fidelity Multiplexed Readout of Superconducting Qubits.” Phys. Rev. Applied 10, 034040 (2018).**  
   DOI: https://doi.org/10.1103/PhysRevApplied.10.034040  
   Accepted manuscript: https://link.aps.org/accepted/10.1103/PhysRevApplied.10.034040  
   **关键修正：** 正文明确 “readout resonators and Purcell filters are implemented as λ/4 resonators”; 五比特 1.2 GHz channel、80 ns pulse、97% average assignment 均可核。

9. **Lienhard, B. et al. “Deep-Neural-Network Discrimination of Multiplexed Superconducting-Qubit States.” Phys. Rev. Applied 17, 014024 (2022).**  
   DOI: https://doi.org/10.1103/PhysRevApplied.17.014024  
   Accepted manuscript: https://link.aps.org/accepted/10.1103/PhysRevApplied.17.014024  
   **强证据：** Fig. 1 caption 明确：each qubit capacitively coupled to a quarter-wave readout resonator that couples inductively to a bandpass (Purcell) filtered feedline。

10. **Teixeira, W. et al. “Many-excitation removal of a transmon qubit using a single-junction quantum-circuit refrigerator and a two-tone microwave drive.” Scientific Reports 14, 13755 (2024).**  
    DOI: https://doi.org/10.1038/s41598-024-64496-5  
    全文: https://www.nature.com/articles/s41598-024-64496-5  
    **复核注：** quarter-wave readout resonator `f_RO=7.437 GHz` 明确。

11. **Luo, Z. et al. “A Versatile Analytical Model for Fast and Accurate Determination of Feedline-Coupled Resonators for Superconducting Qubit Readout.” IEEE Transactions on Microwave Theory and Techniques 73(10), 8059–8070 (2025).**  
    DOI: https://doi.org/10.1109/TMTT.2025.3578414  
    TUM: https://mediatum.ub.tum.de/1795639  
    Fraunhofer: https://publica.fraunhofer.de/entities/publication/450a3678-c2ee-4962-9d00-f4a9bb18211e  
    **复核注：** 真实 journal paper；fabricated λ/4 test chip + cryogenic measurement + `f_r,Q_c` 对比 FEM。arXiv 预印本在 2026 年才出现，不是“2025 论文不存在”。

12. **Spring, P. A. et al. “Fast Multiplexed Superconducting-Qubit Readout with Intrinsic Purcell Filtering Using a Multiconductor Transmission Line.” PRX Quantum 6, 020345 (2025).**  
    DOI: https://doi.org/10.1103/PRXQuantum.6.020345  
    APS: https://journals.aps.org/prxquantum/abstract/10.1103/PRXQuantum.6.020345  
    **复核注：** λ/4 readout + λ/4 filter；MTL 同时具有 distributed capacitive/inductive coupling；linewidth up to 42 MHz；56 ns integration；99.77% average assignment。

13. **Krantz, P. et al. “Single-shot read-out of a superconducting qubit using a Josephson parametric oscillator.” Nature Communications 7, 11417 (2016).**  
    DOI: https://doi.org/10.1038/ncomms11417  
    全文: https://www.nature.com/articles/ncomms11417  
    **复核注：** λ/4 JPO，`C_c=11.9 fF`，`Q_ext=2555`；这是 nonlinear JPO，不应与普通被动读取腔完全等同。

### 3.3 基础 cQED / S 参数拟合参考

14. **Wallraff, A. et al. “Strong coupling of a single photon to a superconducting qubit using circuit quantum electrodynamics.” Nature 431, 162–167 (2004).**  
    DOI: https://doi.org/10.1038/nature02851  
    出版社: https://www.nature.com/articles/nature02851

15. **Khalil, M. S., Stoutimore, M. J. A., Wellstood, F. C., Osborn, K. D. “An analysis method for asymmetric resonator transmission applied to superconducting devices.” Journal of Applied Physics 111, 054510 (2012).**  
    DOI: https://doi.org/10.1063/1.3692073  
    arXiv: https://arxiv.org/abs/1108.3117  
    **复核注：** 文献真实；讨论 impedance mismatch / nonideal transmission 造成 asymmetric line shape，适合说明为什么不能只凭简单 Lorentzian 深度提取 Q。

16. **Probst, S., Song, F. B., Bushev, P. A., Ustinov, A. V., Weides, M. “Efficient and robust analysis of complex scattering data under noise in microwave resonators.” Review of Scientific Instruments 86, 024706 (2015).**  
    DOI: https://doi.org/10.1063/1.4907935  
    PubMed: https://pubmed.ncbi.nlm.nih.gov/25725869/  
    arXiv: https://arxiv.org/abs/1410.3365  
    **复核注：** 文献真实；complex-circle fit 与 diameter correction 的引用正确。

---

## 4. 对此前 λ/2 / λ/4 结论的修订

### 4.1 实验样本重新计数

只按上面“明确写出 readout/measurement resonator 波长”的实验案例来算，而不把 bus、Purcell filter 当作 readout：

**明确 λ/4 readout/measurement：**
- Jeffrey 2014
- Krantz 2016（JPO，特殊）
- Heinsoo 2018
- Lienhard 2022
- Teixeira 2024
- Spring 2025
- Luo 2025 test resonators
- 梁珪涵 2025 学位论文

**明确 λ/2 readout/measurement：**
- Opremcak 2021
- Hertzberg 2021
- Sunada 2022
- Bhattacharjee 2025
- Kreikebaum 2020（shared readout）
- Majer 2007（common cavity/bus，兼 readout；不属于现代专用每比特 readout）
- 王晋哲 2024：**不能计入“明确 λ/2”**，因为实际芯片拓扑目前只有版图推断，没有作者明示。

因此更严谨的结论是：

> 本次经重新核对的样本中，现代 dedicated/multiplexed readout 的 λ/4 案例确实更多；但 λ/2 并不罕见，且 2021–2025 仍有多个实际低温实验案例。不能把“λ/4 更常见”解释成“λ/2 已经过时”或“λ/4 总是性能更好”。

### 4.2 对你当前设计最有直接参考价值的证据层级

如果目标是判断“短路端靠近读取线为何 S21 notch 很弱”，优先级建议改为：

1. **梁珪涵 2025 学位论文**：与你的 λ/4 open→qubit C / short→feedline M 拓扑最直接，而且给了 HFSS 模态流程。
2. **Lienhard 2022**：真实 5-qubit chip，明确 quarter-wave readout resonator + inductively coupled Purcell-filtered feedline。
3. **Heinsoo 2018**：真实多比特 λ/4 readout + λ/4 Purcell filter，包含实测 `κ_R`、`χ`、频率等。
4. **Jeffrey 2014**：真实 λ/4 measurement resonator + λ/4 bandpass filter，实际 S21 和快速 readout。
5. **Luo 2025**：针对 feedline-coupled λ/4 resonator 的 `Q_c` 几何设计/低温实测验证。
6. **Sunada 2022**：明确 open-open λ/2，并说明输出耦合位置可利用分布式多模效应；适合做 λ/2 路线的高质量对照。
7. **Hertzberg 2021 / Bhattacharjee 2025 / Opremcak 2021**：证明现代实际芯片中 λ/2 readout 仍然真实存在。

---

## 5. 本次复核后不再采用的表述

为避免后续设计判断被文献标签误导，建议删掉/改写以下说法：

- ~~“Jeffrey 2014 的 readout resonator λ 类型无法确认”~~  
  → **可确认：measurement resonator 是 λ/4。**

- ~~“Heinsoo 2018 只确认了 Purcell filter，readout resonator λ 类型不明确”~~  
  → **可确认：readout resonator 和 Purcell filter 都是 λ/4。**

- ~~“Sunada 2022 的 resonator 不宜分 λ/2/λ/4”~~  
  → **可确认：它是 open-open λ/2；特殊之处是 output coupling position 形成 intrinsic Purcell filtering。**

- ~~“Majer 2007 可直接代表现代每比特 λ/2 读取腔”~~  
  → **应标注：common λ/2 cavity/bus，可作 readout，但不是现代每比特独立 readout 架构的直接等价物。**

- ~~“Krantz 2016 是普通 λ/4 hanger 读取腔”~~  
  → **应标注：λ/4 Josephson parametric oscillator；几何可参考，动态/读取机制不同。**

---

## 6. 复核结果对最终工程判断的影响

此次复核没有推翻“λ/4 与 λ/2 都有真实制备和低温实验基础”这一结论，但会改变证据权重：

- **λ/4：** 实际多比特 dedicated readout 的公开案例更密集，Jeffrey/Heinsoo/Lienhard/Spring/Luo 都是强证据。
- **λ/2：** 现代案例也很充分，尤其 Sunada/Hertzberg/Bhattacharjee/Opremcak；因此把 λ/2 作为你当前 HFSS 的低风险电容耦合 baseline 是有实验文献基础的。
- **你的当前 λ/4 问题：** 梁珪涵和 Lienhard 的资料都支持“short-end/current-antinode 侧必须形成真正的 inductive/current-return coupling network”；仅仅几何靠近不能由文献推出“必然有足够 M”。
- **后续文献表格：** 应把“论文是否真实”“是否真的制备测量”“readout λ 类型是否作者明示”“Purcell filter λ 类型”分成独立列，避免再次把 filter/bus/readout 混在一起。

---

## 7. 可追踪性说明

这份文件是对之前回答的 **文献真实性和证据归属复核**，不是新的仿真结果。没有运行 HFSS，也没有把任何 Eigenmode 损耗当成 `κ_R`。

两篇中文学位论文的具体页码/图号来自当前会话中的原始 PDF 附件；由于公开网页暂时未找到其正式学位论文题录入口，所以它们被明确标成 **“附件原文已核验、独立公开题录未完成”**，而不是假装已经由 CNKI/UCAS 二次确认。
