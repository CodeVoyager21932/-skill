---
name: folkstory-production-auditor
description: >-
  Audit Chinese continuous-image story projects at topic, title, story, storyboard, fixed-asset, or final-image stages. Use when the user asks to 审查、复盘、验收、找逻辑错误、检查选题/标题/故事/分镜、判断高质量 / 冠军潜力、比较 benchmark 中位数或最高档竞争力、检查 benchmark 是否被过度隔离或过度模仿、识别为了创新而叠机制的 over-innovation、检查模型自行生成的示例 Prompt 是否偷加创意约束、识别 benchmark signature / composite leakage、核对最终图片是否符合已锁分镜、检查字幕连接、资产与正式 Frame Prompt 欠约束、真实性、AI痕迹、连续性、核心机制漂移、漏帧/重复帧或是否可以进入 COMPLETE. Keep technical correctness, creative provenance, creative balance, benchmark-independent high-bar quality, benchmark competitiveness, and external top-tier comparison separate. Repair should preserve strong existing work, fix the weakest layer first, and redesign only when local repair cannot rescue the project.
---

# FolkStory Production Auditor

把审查做成**独立质量门**，不是替 Production 找理由，也不是看到问题就重新创作。

核心原则：**先确认原本要求是什么，再看实际产物是否做到。好看不能替代正确；难看也不等于错误。**

## 1. 先判断审查模式

根据用户实际提供的材料选择最深可执行模式，不必为了形式要求齐全材料：

- `TOPIC_AUDIT`：只有题目、候选题或 Topic Handoff；
- `STORY_AUDIT`：已有正式故事；
- `STORYBOARD_AUDIT`：已有正式分镜，最好同时有故事；
- `FINAL_FRAME_AUDIT`：已有正式图片；若同时有故事/分镜/资产，执行严格对应审查；
- `FULL_PIPELINE_AUDIT`：题目、故事、分镜、资产、最终图都在，追溯问题最早从哪一层产生。

如果缺少某份材料，只审查能够被证实的部分，并明确哪些结论无法验证。不要凭空补一个“原要求”。

详细标准按需读取：

- 选题与故事：`references/topic-story-audit.md`
- 分镜：`references/storyboard-audit.md`
- 最终图片与逐帧对应：`references/final-frame-audit.md`
- 严重度与根因：`references/severity-and-root-cause.md`
- 输出格式：`references/output-spec.md`
- 创意来源独立性：`references/creative-provenance-audit.md`
- 对标作品的使用边界：`references/benchmark-use.md`

## 2. 先建立 Requirement Ledger，再开始批评

审查前在内部整理**要求账本**。要求优先级：

1. 用户最新明确修改/确认；
2. 已批准或已锁的 Topic / Story；
3. `REALITY_BASELINE`、`MECHANISM_LOCK` 等已锁约束；
4. 已锁 Storyboard：N、Frame ID、每帧视觉任务、动作、位置、揭示与记录条件；
5. `FIXED_ASSET_HANDOFF` 与实际通过的固定资产；
6. 生成 Prompt 仅作为执行证据，不得反过来推翻更高层要求。

**实际图片只证明“最后生成了什么”，不能证明“本来就应该这样”。**

如果 Storyboard 自己设计错了，而最终图片忠实执行了它：判定为 `STORYBOARD` 根因，不把锅全甩给生成。

如果 Storyboard 正确、图片做错：判定为 `GENERATION`。

如果固定资产先做错，后续所有 Frame 忠实继承：判定为 `ASSET`。

## 3. 审查不是打分游戏

默认不做 82/100 之类综合分，不设问题配额，不为了显得严格硬找瑕疵。

只回答三个问题：

1. **有没有会让作品逻辑、核心回报或生产合同失效的问题？**
2. **问题最早来自哪一层？**
3. **最小修复是什么？**

严重度统一使用 `P0 / P1 / P2`。详见 `references/severity-and-root-cause.md`。

当用户明确要求“高质量 / 独具创意 / 最有上限 / 最强 / 冠军候选”，`TOPIC_AUDIT` / `STORY_AUDIT` 先给一条**不依赖 benchmark** 的 `HIGH_BAR_CONTENDER: YES / BORDERLINE / NO / NOT_ASSESSED`。它判断作品自身是否已经具备高档竞争结构，不能因为用户没提供对标就自动降低质量门。

当用户提供了明确对标作品、完整 benchmark 作品集，或要求按对标水平判断时，再额外给 `BENCHMARK_COMPETITIVENESS: ABOVE_MEDIAN / AROUND_MEDIAN / BELOW_MEDIAN / NOT_ASSESSED`。它描述相对中位竞争力，不直接等同 P0/P1/P2，也不能推翻已经锁定的生产合同。

当用户明确要求和 benchmark **最高档**比较，而且素材足以识别高水位样本时，再增加 `TOP_TIER_CONTENDER: YES / BORDERLINE / NO / NOT_ASSESSED`。`HIGH_BAR_CONTENDER = YES`、`ABOVE_MEDIAN` 都不能自动等同外部最高档。

如果用户同时提供同一 Prompt 的“有 benchmark / 无 benchmark”两组结果，再增加 `BENCHMARK_DEPENDENCE_AUDIT: PASS / RISK / FAIL`。判定必须先区分 `INDEPENDENT_ORIGINALITY_MODE` 与 `BENCHMARK_GUIDED_MODE`：GUIDED 模式允许 benchmark 明显改善 story spine 与修复策略，重点抓具体表面生态 / 签名组合漂移，不再要求两组第一轮结构必须近似。

当用户要求“原创 / 独具创意 / 自己的风格”，或当前对话 / 项目已经暴露具体 benchmark、Library、历史作品时，读取 `references/creative-provenance-audit.md`，增加 `CREATIVE_PROVENANCE_AUDIT: PASS / RISK / FAIL / NOT_ASSESSED`。成熟 story grammar 相同本身不判泄漏；重点看高度具体的作品指纹、组合辨识度与价值来源。

当审查 Topic / Story 的目标涉及“创新、参考对标、高质量、最有上限”，再增加 `CREATIVE_BALANCE_AUDIT: PASS / RISK / FAIL / NOT_ASSESSED`，并分别记录 `OVER_INNOVATION_RISK` 与 `IMITATION_RISK`。它检查作品有没有因为追求差异无必要叠机制，也检查有没有因为参考 benchmark 变成换皮；两端都不能靠另一端洗分。

`HIGH_BAR_CONTENDER = YES`、强入口或顶档竞争力不能覆盖来源问题。一个题可以很好看，同时不能作为 Skill 独立原创能力的合格证据。

## 4. 选题审查：先看值不值得做，同时保留用户选择权

### 4.1 候选透明是硬规则

`TOPIC_AUDIT` 不是隐藏淘汰器。只要一次审查中存在多个候选，就必须让用户知道**全部候选及其审查结果**。

- 用户给了 N 个候选：输出必须覆盖 N 个，不得只展示冠军或“通过筛选”的少数；
- 如果为了比较而自行提出了替代候选/变体：凡真正进入比较、排序或淘汰判断的候选，都必须以稳定 ID 显示给用户；
- 推荐、排序、分层完全允许，但必须与“完整披露”分开：先让用户看见全部，再标注推荐；
- `不推荐 / REDESIGN` 只代表审查判断，不代表删除该候选；仍要显示它、主要问题和是否有可救方向；
- 只有用户明确要求“只给我 Top N / 只看最好的”时，才允许缩减展示范围；
- 候选很多时可以让每项更简短，但不能靠隐藏候选来节省篇幅。

**不要在内部先发散 12–20 个题，再只把一个冠军告诉用户。推荐权属于 Auditor，最终选择权属于用户。**

### 4.2 审查标准

审查题目时，不要求完整剧情，也不把复杂当高级。

如果用户要求原创，或当前对话 / 项目上下文已有 benchmark 暴露，先按 `references/creative-provenance-audit.md` 建立创意来源账本：真实用户约束、模型自行补出的创意约束、已暴露对标组件分别记录。若模型在“虚构真实 Prompt”阶段已经偷偷加入乡村、失踪、祖屋、葬礼等会改变创意分布的方向，先记录 `PROMPT_DRIFT`，不能把后续结果当成无约束原创测试。

随后按 `references/topic-story-audit.md` 做严格 **Blind Entry Audit**，再看完整 Topic：

1. 若存在标题，先只看标题，记录 `TITLE_AUDIT: STRONG / OK / REPAIR / N/A`。
2. 再只读取最早已经发生的入口事实，遮住“这题好看在哪”、后续牵引、Payoff、最终大景与 benchmark 辩护，冻结 `ENTRY_AUDIT: STRONG / BORDERLINE / WEAK`，并记录 `LATE_HOOK: YES / NO`。
3. 完成入口冻结后再读取完整一句话题眼和后续字段。下游潜力不得反向提高 `ENTRY_AUDIT`。
4. 标题弱时区分 `TITLE_ONLY_REPAIR` 与 `ENTRY_AND_TITLE_REPAIR`：底层入口强才只修标题；底层入口也弱就回 Topic 入口修。

随后检查：

- 第一层到底发生了什么，普通观众是否不用先学规则就能理解；
- 第一件异常以后，题目本身是否有自然向前的事件生命力，而不是只能继续解释 / 测试同一机制；
- 最值钱的内容是否主要能通过图片获得；
- 核心前提确认 2–3 次以后，主观看牵引是否仍获得新的接近、后果、威胁、理解、尺度、选择、真相或兑现；新问题只是其中一种形式；
- 一句话是否已经把最终最值钱的对象 / 大景基本说透；若一张清楚确认图就能拿走大部分满足感，后续是否还有连续故事才能获得的价值；
- 是否有一个值得亲眼看到的兑现潜力；
- 当用户要求高质量 / 冠军潜力时，即使没有 benchmark，也检查第一层之后是否形成具体 `STORY_DEBT`，是否存在能落成对象 / 事件 / 事实 / 结果的 `PAYOFF_CEILING`，以及深挖后有没有一件真正改变处境的下一事件；如果只是把世界做得更大、空间开得更深，也要如实降低 `HIGH_BAR_CONTENDER`；
- 当启用外部最高档比较时，再看这些能力与高水位 benchmark 的相对差距；如果第二层只是第一联想的顺手答案，也要如实降低 `TOP_TIER_CONTENDER`；
- 是否只是常见模板换职业、国家、年代或异常名词；熟悉探险、返乡、失踪、遭遇、调查等母型本身不判失败；
- 若要求原创，是否存在 `COMPOSITE_LEAKAGE_RISK`：把已暴露作品的入口债务、人物关系、场景、异常载体、证据方式、推进和 payoff 拆件重组；单个通用母题相同不判失败，判断具体组合与价值来源；
- 核心价值是否实际依赖语音、长文字、内心解释或规则百科；
- 如果输入含多个候选，候选池是否共享明显相同的**入口发动机**或故事发动机。即使后续机制不同，若多数都靠“无生命对象 / 环境违反物理规律”宣布怪事，也记录 `POOL_ENTRY_RISK`；
- 多个标题是否又被包装成同一种句式或同一种揭示深度。若选题本身有差异，只修标题，不把标题同质误判成选题同质；
- 有没有一个已经成立的 `INNOVATION_FOCUS / INNOVATION_ANCHOR`，同时其他层被允许使用成熟 story grammar；不要因为只有一层真正新就降级；
- 有没有 baseline 本来已经强，却为了“第二层更意外 / 更顶档”新增第二主机制、隐藏世界、时间 / 身份 / 空间重分类，导致理解成本上升而观看价值没有变强；
- benchmark 本身是否值得学：craft 高但 novelty 普通时应该学叙事效率，novelty 高但 craft 弱时不应把完整路线当模板。

如果核心异常确认后，观看欲望几乎只剩“继续理解这套机制还能怎么运作”，提高风险；机制好奇本身合法，只在缺少独立的事件、后果、威胁、空间、人物选择、具体真相或终点时降级。

不要因为没有“深刻主题”、复杂机制或结尾反转判失败。对本项目，**观看价值和图像承载优先于主题升华**。

如果同时有同一 Prompt 的“有 benchmark / 无 benchmark”两轮结果，按 `references/benchmark-use.md` 做模式化依赖审查：INDEPENDENT 模式要求第一轮相对独立；BENCHMARK_GUIDED 模式允许成熟 story grammar 改善结构，但不能大面积漂向对标的具体人物、地点、标志物、怪物、签名关系、逐段反转链或结局。

即使只有单轮结果，只要当前项目已经暴露过具体 benchmark，也可以做 `CREATIVE_PROVENANCE_AUDIT`。此时若看不到内部生成过程，最多判 `RISK`，不要仅凭“像”就断言来源。若明确看到模型自行加入创意方向约束，或创意核心直接由已暴露组件拼装，则可以判 `FAIL`。

## 5. 故事审查：看事情是否真的往前走

故事必须让陌生观众能用 2–3 句话复述“发生了什么”，并通过首刷四问：主角遇到了什么、为什么继续、后来实际发现/遭遇/确认了什么、最后具体发生了什么。任何一问只能靠“可能/也许/我猜/暗示”回答，视为 Story 清晰度风险。

重点检查：

- 入口是否迅速建立人物 / 处境 / 起因；
- 事件之间是否有可理解的因果、行动或被迫推进；
- 核心前提已经成立后，是否还在换证据反复证明同一结论；
- 是否出现“改一个变量 → 看同一机制反馈 → 再改一个变量”的机制演示流水线，而人物目标、处境、危险、空间和事实理解长期不变；
- 主观看牵引是否持续获得新的接近、后果、威胁、理解、尺度、选择、真相或兑现；新问题只是其中一种有效方式；仅有物理接近不自动算推进；
- 到中后段是否至少有一次实质升级：威胁逼近、不可逆后果、世界 / 尺度打开、对象被重新理解、调查抵达真相、人物选择产生结果或核心兑现都可以；不强制反转；
- 重大人物 / 地点 / 时间 / 叙事层级切换是否无需观众自行补关系；
- 改写前文基本事实的关键反转是否有紧邻的可确认事件或证据；
- 人物是否做出选择、判断、行动或承受结果；
- 最终是否通过具体对象、动作、变化、发现、选择结果或事实确认兑现前面最值得看的承诺；若结果只能靠抽象关系词描述，去掉该词后是否仍能只用可见前后变化讲清；
- 结尾是否为了解释完整又拖出一截；
- 可以保留真相解释的不确定，但不能让“刚才发生什么”本身含糊。

发现问题时优先指出**缺少哪一个事件变化、桥接、可确认事实或直接 payoff**，不要自动发明复杂规则体系或强制反转。**路线简单本身不构成缺陷**；同一威胁持续逼近、一路探索到世界打开、调查一路抵达真相都可以很强。只有当简单路线长期没有新的观看价值时才修。

同时做一次 Creative Balance pass：如果故事的问题来自“为了创新继续叠东西”，把它归到 STORY / TOPIC 的 `OVER_INNOVATION_RISK`；如果 benchmark 的具体人物关系、场景、异常、证据、推进和 payoff 能连续映射，记录 `IMITATION_RISK`。成熟 spine 相似但具体事件与观看身份不同，不判模仿。

## 6. 分镜审查：核心是“图数花在哪里”

Storyboard 审查同时看单帧和序列。

必须检查：

- F01 是否让陌生用户知道核心主体是谁、为什么故事今天开始；
- N 是否由故事需要产生，而不是固定范围或填满配额；
- 已经懂的内容是否被重复占帧；
- 值得亲眼看清的关键变化、进入过程、动作结果、尺度兑现是否给了足够观看时间；
- 必要过程帧有没有被压掉，导致动作/因果瞬移；
- 把 `F01...FN` 字幕单独顺读时，相邻帧是否出现人物突然加入、睡/醒/下楼/抵达、动作触发、告知/知情状态的微跳转；图片省略普通过程时，字幕是否承担了最小连接，而没有为了连续性把每一步都写成流水账；
- 视角是否来自真实可达观察位置；
- 揭示程度是否过早把核心东西完整展示，或反过来把理解当前事件所必需的人、动作、对象、关系/结果遮得过头；
- “现实记录条件”是否由拍摄处境产生，而不是每张都故意糊、歪、脏；
- 同一作品是否无理由统一成一种电影式灯光、调色和完美构图；
- `FIXED_ASSET_HANDOFF` 是否把真正需要跨帧固定的人、重要空间、关键物件/特殊实体交接出来；
- 如果同时提供资产 Prompt / 正式 Frame Prompt，检查它们是否只是“短”，还是已经短到遗漏主体识别锚点、空间结构、动作阶段、连续状态或关键关系；Prompt 欠约束时不能直接把后续偏差全判给图像模型；
- 若核心理解依赖精确关系，Storyboard 是否把对应关系写清；不需要时不得强造 Mechanism Lock。

详细见 `references/storyboard-audit.md`。

## 7. 最终图片审查是最高优先级

只要用户提供了最终图片，优先执行 `FINAL_FRAME_AUDIT`。

### 7.1 先查生产合同

先验证：

- N 是否一致；
- `F01...FN` 是否全部存在；
- 是否有漏号、重复 Frame ID、误把覆盖候选当新增 Frame；
- 正式一帧是否是一张独立完整图片，而不是九宫格、拼贴、contact sheet；
- 如果用户为了审查自己上传 contact sheet，不要误判为 Production 输出，除非用户明确说正式产物就是该拼图。

任何重复/漏帧/错误计数都先处理，再谈美术质量。

### 7.2 每个 Frame 都从 Storyboard 提取“必须事实”

内部为每帧提取：

- `WHO/WHAT`：必须出现谁/什么；
- `WHERE`：地点/空间关系；
- `ACTION`：动作进行到哪一步、动作对象是谁；
- `STATE`：人物、物件、场景此时已经是什么状态；
- `RELATION/REVEAL`：必须看懂的对应、距离、尺度、遮挡、缺失或揭示程度；
- `CAPTURE`：本帧规定的真实观察位置与拍摄条件。

然后看**实际像素**是否完成这些要求。Prompt 写对但图片没做到，仍然判失败。

### 7.3 一张漂亮图也可以是 P0

以下情况不要因为画面漂亮而放过：

- 本帧唯一重要事实没有被看见；
- 动作对象错了，例如要求碰 A，实际碰 B；
- 左/右、前/后、内/外等会改变剧情理解的关系弄反；
- 核心物件材质、尺寸、来源或身份发生无剧情依据的变化；
- 本该严格对应的两个对象没有对应；
- 已经发生的损坏、缺失、受伤、设备状态下一帧自动恢复；
- 人物或关键资产换脸/换结构到影响身份；
- 生成结果改变了故事机制；
- 正式要求是真实摄影记录，却整体变成漫画、插画、游戏设定或明显概念图。

### 7.4 连续性要看“序列事实”，不只看脸

跨帧检查：

- 人物身份、服装和体型；
- 场景空间结构和固定锚点；
- 道具身份、材质、尺度与损伤；
- 时间、天气、湿脏、伤势、设备状态；
- 行动是否有必要的中间过程；
- 核心关系/机制有没有漂；
- 观众上一帧已经知道的事实是否下一帧被无理由否认。

人物脸一致但核心机制漂移，仍然可以是严重失败。

### 7.5 真实性看因果，不看“够不够脏”

检查：

- 摄影者/记录设备是否能真实存在于这个位置；
- 人物是在做事，还是一直为了镜头摆好；
- 光源、遮挡、反射、清晰度、运动、曝光是否和环境/拍摄行为一致；
- 场景是否有真实功能和使用痕迹；
- 稳定环境可以清楚好看，不因“太清楚”自动判假；
- 追逐、低照、车内、恶劣天气等如果仍像静态棚拍，需要警惕；
- 整组不同地点/处境如果都变成同一种高级调色、漂亮侧光和完美构图，判系统性真实性风险。

### 7.6 AI 痕迹只抓有意义的

重点抓会影响观看或暴露生成的：

- 多余/融合肢体、严重手部错误；
- 人物/物件无意义重复；
- 结构融化、设备形态不可能；
- 文字乱码且画面要求文字可读；
- 镜面/窗户/影子产生严重矛盾；
- 几何穿插、物体悬空、明显复制纹理；
- 同一角色局部突然非人化或材质融合。

不要放大到像素级找不影响作品的微小瑕疵。

详细见 `references/final-frame-audit.md`。

## 8. 根因必须归层，不要只列症状

每个 P0/P1 尽量标记最早根因：

- `TOPIC`
- `STORY`
- `STORYBOARD`
- `ASSET`
- `PROMPT_SPEC`
- `GENERATION`
- `PIPELINE`（漏帧、重复 ID、状态机/计数错误等）

如果一个错误跨层传播，写“PRIMARY → PROPAGATED TO ...”。

例如：固定人物资产本身已漫画化，后续几十帧都继承，则主因是 `ASSET`，不是把每张都当独立生成错误。

## 9. Repair 原则：先保留，再局部修，最后才重构

审查默认**不自动改稿、不自动生图**。Repair 建议按 `PRESERVE → REPAIR → RECONSIDER → REBUILD`：

- `PRESERVE`：先列清已经成立的入口、人物动机、核心异常 / 关系、最值钱视觉、`INNOVATION_ANCHOR` 与正确生产合同；
- `REPAIR`：只修当前最早根因所在的弱层。最终 Frame 错就覆盖同 ID；Storyboard 错先修分镜；Story 缺事件 / payoff 回 Story；Topic 没持续价值才回 Topic；
- `RECONSIDER`：局部修复后重新判断。已经达到标准就停止，不因为“还能更创新”继续加机制，也不因为“参考对标”顺手换掉更多内容；
- `REBUILD`：只有核心发动机、入口、图片承载或 payoff 经过合理局部修复仍无法成立时才建议重构。重构也保留仍然有价值的部分。

来源风险优先拆掉过近的**作品指纹**，不要把成熟 story grammar 一起删掉；过度创新优先撤掉无必要的新机制，恢复原题已经成立的简单路线。

某一资产污染全篇时仍先修资产，再重做受影响 Frame；不因为局部 P2 触发大规模重构。

## 10. 最终审查必须有明确结论

默认使用：

- `PASS`：没有阻塞问题；
- `REPAIR`：主体可保留，但存在必须修的局部问题；
- `REDESIGN`：问题来自 Story / Storyboard 等上游，局部重生图片无法解决。

最终图片审查只要存在未解决 P0，就不能判 `PASS/COMPLETE`。

输出结构见 `references/output-spec.md`。
