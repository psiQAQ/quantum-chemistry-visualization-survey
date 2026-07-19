# kim_neuralvdb_2024

BibTeX key：`kim_neuralvdb_2024`

## 摘要

We introduce NeuralVDB, which improves on an existing industry standard for efficient storage of sparse volumetric data, denoted VDB [Museth 2013 ], by leveraging recent advancements in machine learning. Our novel hybrid data structure can reduce the memory footprints of VDB volumes by orders of magnitude, while maintaining its flexibility and only incurring small (user-controlled) compression errors. Specifically, NeuralVDB replaces the lower nodes of a shallow and wide VDB tree structure with multiple hierarchical neural networks that separately encode topology and value information by means of neural classifiers and regressors respectively. This approach is proven to maximize the compression ratio while maintaining the spatial adaptivity offered by the higher-level VDB data structure. For sparse signed distance fields and density volumes, we have observed compression ratios on the order of 10× to more than 100× from already compressed VDB inputs, with little to no visual artifacts. Furthermore, NeuralVDB is shown to offer more effective compression performance compared to other neural representations such as Neural Geometric Level of Detail [Takikawa et al. 2021 ], Variable Bitrate Neural Fields [Takikawa et al. 2022a ], and Instant Neural Graphics Primitives [Müller et al. 2022 ]. Finally, we demonstrate how warm-starting from previous frames can accelerate training, i.e., compression, of animated volumes as well as improve temporal coherency of model inference, i.e., decompression.

## Zotero 阅读笔记

<h1>Survey 定向阅读问题</h1>
<p><strong>关联模块：</strong> 神经稀疏体、压缩与可信度</p>
<p><strong>问题生成依据：</strong> 题名与 Zotero 摘要</p>
<p>阅读时请直接在每个"回答"后补充结论，并记录对应页码、图表或原文位置。</p>
<h2>阅读问题</h2>
<h3>问题 1</h3>
<p>NeuralVDB 相比传统 VDB 在压缩率、访问速度和重建误差上如何权衡？</p>
<p><strong>回答：</strong>NeuralVDB 用层次神经网络替换 VDB 树的最底层节点，实现了极高压缩率但付出了性能和精度代价：① 压缩率——在已压缩的 OpenVDB（16-bit 量化 + Blosc）基础上额外压缩 10x-100x，例如 Disney Cloud 从 1.5GB 降至 25MB（60x），飞船穿浪水面序列从 22.7GB 降至 1.2GB（18x）（第20:1页 Figure 1）。配置 [Hash, 5, NN(4), NN(3)]（最低两层由神经网络替换）提供最高压缩比，[Hash, 5, 4, NN(3)]（仅最低层神经网络）平衡压缩和性能（第20:18页 Discussion）。② 访问速度——"random query performance is comparable to the third-order interpolation of NanoVDB, but still slower than the first-order sampler"（第20:18页），这在交互式体渲染中可能成为瓶颈（三维纹理常用的是一阶/线性插值）。③ 重建误差——论文提及 "user-controlled compression errors" 和 "little to no visual artifacts"（第20:1页 Abstract），但属于有损压缩。编码/解码时间：Disney Cloud 约 5 分钟编码、3 分钟解码（第20:18页）。④ 局限性——NeuralVDB 和 NanoVDB 一样假设树结构固定（"assumes the tree and its values to be fixed"，第20:18页），不支持动态拓扑。</p>
<h3>问题 2</h3>
<p>神经表示是否适合科学归档，还是只适合作为派生展示缓存？</p>
<p><strong>回答：</strong>论文清楚建议 NeuralVDB 作为 "lossy offline representation"（有损离线表示）（第20:18页 "we primarily recommend [Hash, 5, NN(4), NN(3)] as a very efficient but lossy offline representation"），而非主要存档格式。对于科学归档，以下因素使其不适合作为主要格式：① 有损压缩——量子化学场的科学分析通常要求数值可重复验证，有损压缩的误差即使在视觉上可忽略，也可能改变关键特征（如 ESP 的极小值位置可能影响反应位点预测）；② 大量超参数——NeuralVDB "has even more hyperparameters that need to be specified for optimal performance"（第20:18页），这增加了归档文件的可复现性风险；③ 编码/解码不对称——训练（编码）耗时以分钟计，不便于快速检索和随机访问；④ 硬件/软件依赖性——神经网络的推理可能依赖特定 ML 框架版本。论文也指出："we are not proposing that NeuralVDB can replace standard VDBs for all applications"（第20:18页）。因此，NeuralVDB 更适合作为派生展示/传输缓存，而原始 VDB（或 Cube）仍应作为科学存档格式。对于本项目的"可验证、可复现"核心原则，神经压缩应作为可选的派生格式而不是主要表示。</p>
<h3>问题 3</h3>
<p>用户接受有损神经压缩需要哪些误差报告和可重复解码条件？</p>
<p><strong>回答：</strong>基于论文设计，可接受的神经压缩需满足以下条件：① 误差报告指标——论文使用视觉质量评估（"little to no visual artifacts"），但科学应用需要定量指标：峰值信噪比 PSNR、结构相似性 SSIM、最大绝对误差 MaxAE、均方根误差 RMSE——这些在论文第20:2页提及但与实际结果缺乏系统报告（Discussion 中主要靠视觉效果判断）；② 用户可控误差——论文提到 "user-controlled compression errors"（第20:1页 Abstract），但未详细说明暴露给用户的控制参数（目前的超参数包括网络大小、层级数、训练步数等，第20:18页）；③ 可重复解码条件——需要固定神经网络权重、架构定义、推理代码版本；④ 时间序列的时序连贯性——warm-starting 从先前帧初始化可加速训练并改善时域一致性（第20:1页 "warm-starting from previous frames can accelerate training... as well as improve temporal coherency of model inference"），但对动画序列仍需报告帧间误差波动。建议问卷包含问题："如果神经压缩提供定量误差报告（PSNR > 40dB、MaxAE < 1e-3 au），你是否接受有损压缩用于展示用途？" 和 "你是否要求导出原始无损格式以便验证？"</p>
<h2>阅读后回填</h2>
<ul><li>对问卷题目或选项的影响：模块 B（选择可信度与失败风险）应增加关于"有损压缩格式用于科学出版物是否可接受"的问题。模块 C（三维场文件格式）中 NeuralVDB/神经表示应列为"派生展示格式"而非"源数据格式"。</li><li>可转化为访谈追问的内容：用户对神经网络的"黑箱"特性在科学可视化中的信任度如何？如果误差报告显示高 PSNR 但局部极值位置偏移，用户如何判断是否可接受？</li><li>关键证据页码/图表：第20:1页 Figure 1（Disney Cloud 60x 压缩）、第20:18页（Discussion 部分——"lossy offline representation" 建议、编码解码时间、不支持动态拓扑）。</li><li>仍未解决的问题：NeuralVDB 在密度/水平集之外的其他数据类型（如 ESP 等有正负值的标量场）上的压缩表现如何？论文只测试了 SDF 和密度场。</li></ul>
<h2>总结</h2>
<p><strong>核心发现：</strong>NeuralVDB 通过层次神经网络替换 VDB 树底层节点，在已压缩 OpenVDB 基础上实现 10-100x 的额外压缩，Disney Cloud 从 1.5GB 降至 25MB。但这是有损压缩，编码耗时数分钟，随机访问慢于一阶插值，且假设拓扑静态。</p>
<p><strong>与本项目的关联：</strong>神经压缩为超大规模量子化学体数据的传输和展示提供了可能性（如高分辨率 ESP 网格可降至数 MB）。但本项目的"可验证、可复现"原则要求原始无损格式始终保留，神经压缩仅作为可选的派生格式。问卷中应明确区分"科学存档格式"和"展示/分发格式"。</p>
<p><strong>对问卷的具体建议：</strong>① 模块 B 增加问题："你是否愿意使用有损压缩（如 NeuralVDB）来减少体数据文件大小？条件是提供定量误差报告。"② 模块 C 将 NeuralVDB 列为"神经网络压缩体积格式"选项，但标注"有损"。③ 模块 F（开放反馈）可询问用户对 AI/ML 技术在科学可视化中应用的信任度。</p>
