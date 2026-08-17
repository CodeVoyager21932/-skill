# Benchmark Learning & Isolation｜借成熟语法，隔离作品指纹

benchmark 的职责不只是在最后打分。用户明确要求“参考 / 学习 / 对标这些优秀作品”时，可以让**经过筛选的成熟故事语法**进入创作；但作品的高辨识度内容、标志性组合和具体反转链仍然隔离。

目标不是“有 benchmark 和没 benchmark 想出的东西必须一样”，也不是“照着高赞作品写”。目标是：**让 benchmark 提高 AI 选择故事路径的能力，同时不把新作品变成对标换皮。**

使用前先读取 `creative-provenance.md`，锁定真实用户约束与作品指纹边界。

## 1. 先判断任务模式

### A. `INDEPENDENT_ORIGINALITY_MODE`

在以下情况使用：

- 用户明确要测试 Skill 的独立原创能力；
- 用户要求不参考已有作品；
- benchmark 只用于最后审查 / 比较，没有要求它参与创作。

流程继续使用 Blind Seed Birth：先依据 `USER_EXPLICIT + TASK_REQUIRED` 生成种子，再用 benchmark 校准。

### B. `BENCHMARK_GUIDED_MODE`

用户在当前任务或已明确延续的项目约束中说“参考 / 学习 / 对标 / 按这批优秀作品的水平做”，或当前任务本来就是基于指定作品集做正式创作时使用。

此时 benchmark 可以在种子出生前提供**可迁移的故事语法**，但不能直接提供新题的具体人物、地点、怪物、标志物、标志场面、逐段推进或结局。

不要因为项目上下文曾经读过 benchmark，就自动进入 GUIDED；也不要因为使用 GUIDED，就把所有作品都当模板。

## 2. Benchmark 先做学习价值分流

不要用点赞 / 分享直接决定“该学什么”。对有希望的对标内部判断四件事：

- `CRAFT_VALUE = HIGH / MEDIUM / LOW`：入口、事件惯性、清晰度、节奏、人物继续的理由、payoff 等完成质量；
- `NOVELTY_VALUE = HIGH / MEDIUM / LOW`：它真正新鲜的是题眼、关系、行为、情绪变轨、视觉关系还是只是包装；
- `TRANSFERABILITY = HIGH / MEDIUM / LOW`：其中的经验能否脱离原人物 / 原场景后仍成为通用创作能力；
- `SIGNATURE_RISK = HIGH / MEDIUM / LOW`：一旦借用，是否很容易让熟悉原作的人认出具体来源。

使用原则：

- **Craft 高 + Transferability 高**：优先学习；
- **Craft 高 + Novelty 一般**：学叙事效率、故事惯性、清晰度和兑现，不把普通表面创意当创新模板；
- **Novelty 高 + Craft 一般**：可以当灵感刺激，不把它的完整故事骨架当标准答案；
- **Craft 和 Novelty 都弱**：不主动模仿，即使传播数据高也只作为现象样本。

benchmark 本身不因“是对标”就自动正确。

## 3. 三层学习权限

### 第一层｜`TRANSFERABLE_GRAMMAR`：允许直接学习

这些是成熟故事语法，不属于单篇作品私有零件：

- 入口功能与第一笔观看债；
- “陈年未完成事件被今天的新证据重新启动”这类 story spine；
- “现实任务逐步进入未知”“近身威胁持续缩短距离”“私人线索驱动寻找公共未知”等推进语法；
- 人物为什么自然继续；
- 中段怎样维持 story inertia；
- 何时确认、何时升级、何时兑现；
- payoff function；
- 情绪变轨方式；
- 图片在叙事中承担证据、距离、尺度、行动结果还是身份确认。

这些可以参与第一轮种子和 Strong Seed 修复。

### 第二层｜`TRANSFORM_BEFORE_USE`：只允许经过当前题重新设计

包括：

- 人物关系类型；
- 场景类别；
- 证据介质；
- 异常载体类别；
- 开场情境类型；
- 终局类型。

它们可以提醒“还有这种解法”，不能原样移植。进入新题前必须由用户需求、当前种子和现实语境重新决定。

### 第三层｜`SIGNATURE_QUARANTINED`：继续隔离

包括：

- 原人物、专名、地点、组织、编号；
- 高辨识人物关系 + 场景的具体组合；
- 标志性物件、怪物、生物和场面；
- 具体异常机制的独特实现；
- 可逐段映射的推进链；
- 标志性反转链与原结局；
- 独有标题句式、台词和高度识别性表达。

用户明确点名某个粗粒度元素做变体时，可以解除该元素本身的限制，但仍不能复制整套指纹。

## 4. `BENCHMARK_GUIDED_MODE` 的生成流程

1. **Register**：确认作品范围、重复版本、用户要求与来源边界；
2. **Triage**：判断哪些作品 / 哪些层值得学，不按热度全盘接受；
3. **Extract Grammar**：从不同高学习价值样本中提取少量 `TRANSFERABLE_GRAMMAR`；只写功能和结构，不带原作专名和标志物；
4. **Seed Birth**：使用 `USER_EXPLICIT + TASK_REQUIRED + 适配的 TRANSFERABLE_GRAMMAR` 生成粗种子；grammar 是可选路线，不是必须套模板；
5. **Preserve & Repair**：先保留种子已经成立的入口、行为、关系或图像锚点；只对明显弱层调用 benchmark 经验；
6. **Provenance Check**：按 `creative-provenance.md` 检查作品指纹、组合辨识度与多篇拆件重组；
7. **External Compare**：最后再做 benchmark 中位 / 顶档竞争力判断。

不要让同一篇作品从入口一路决定到结局。一个 benchmark 可以提供某个成熟 spine 或某项修复经验，但新题仍要拥有自己的具体观看身份。

## 5. 熟悉结构合法，创新要集中

benchmark-guided 不等于保守，也不等于模仿。

- 一个题可以使用成熟的失踪调查、返乡、探索、工作任务、封闭空间威胁等结构；
- 不要求入口、关系、异常、推进、证据、payoff 每一层都创新；
- 至少保留一个真正属于当前题的 `INNOVATION_FOCUS`：可以是具体行为、关系、异常、视觉证据、人物处境或终点；
- 如果 baseline 已经强，不因为“和成熟语法相似”继续叠第二机制；
- 如果 baseline 明显陈旧、可替换或走不长，才继续发散。

熟悉结构不是加分项，只是合法工具。平庸仍然要修。

## 6. 创意来源隔离看“作品指纹”，不看槽位数量

最终强候选仍做 `COMPOSITE_LEAKAGE_AUDIT`，但不要机械使用“三个槽位相似就失败”。判断三件事：

- `SPECIFICITY`：相似的是通用语法，还是高度具体的实现；
- `COMBINATION`：这些具体元素组合起来是否指向同一已知作品 / 几篇近期对标；
- `SALIENCE`：相似部分是否恰好承担新题最有价值、最容易被记住的部分。

只有当**具体 + 组合 + 显著**达到足以识别来源的程度，才提高到 `REVIEW / LEAK`。

## 7. 有无 benchmark 的稳定性检查重新定义

如果存在同一 Prompt 的 A/B：

- 在 `INDEPENDENT_ORIGINALITY_MODE`，第一轮分布应相对稳定；
- 在 `BENCHMARK_GUIDED_MODE`，允许 story spine、入口功能、节奏与修复策略明显变好甚至发生变化；
- 两种模式都不应让候选池大面积滑向 benchmark 的专名、地点生态、标志物、怪物、具体反转或同一套可识别场景组合。

当最终池超过一半明显落入 benchmark 的同一批**具体表面生态或签名组合**，标记 `BENCHMARK_SURFACE_DRIFT`，回到可迁移 grammar 层重做。

## 8. 最终原则

> **学会别人为什么好看，不需要忘掉别人怎样讲故事；但要忘掉那篇作品只有它自己才有的指纹。**

benchmark 可以帮 AI 少走弯路，不能替 AI 决定新作品的脸。
