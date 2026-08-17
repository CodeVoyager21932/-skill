# 严格生产流水线

## 1. 状态机必须控制“下一次工具动作”

只能按一条边前进：

```text
INTAKE
→ STORY_DRAFT
→ WAIT_STORY_APPROVAL
→ STORY_LOCK
→ STORYBOARD_DRAFT
→ WAIT_STORYBOARD_APPROVAL
→ STORYBOARD_LOCK
→ ASSET_BUILD
→ ASSET_LOCK
→ GENERATE
→ REVIEW
→ REPAIR
→ COMPLETE
```

每次准备调用工具前，先判断当前状态，并确定一个具体 `NEXT_TOOL_ACTION`。

**状态不是说明文字。当前状态不允许的工具动作，禁止执行。**

例如分镜刚确认后：

```text
STATE: ASSET_BUILD
NEXT_TOOL_ACTION: GENERATE A-P01
FORMAL_FRAME: FORBIDDEN
```

此时即使平台提示“一轮最多生成 10 张”，也与 F01–FN 无关；当前只能继续 A-P01。

## 2. 两个阻塞式用户门

### WAIT_STORY_APPROVAL

只允许：展示/解释/修改当前正式故事。

禁止：正式分镜、资产、生图。

有效批准例如：“故事确认”“按这版进入分镜”。

### WAIT_STORYBOARD_APPROVAL

只允许：展示/解释/修改当前 `REALITY_BASELINE`、`MECHANISM_LOCK`（如有）、正式 N 张分镜，以及随这版分镜一起交接的 `FIXED_ASSET_HANDOFF` 文本清单。

禁止：真正生成资产图、正式 Frame。

`FIXED_ASSET_HANDOFF` 必须在等待确认前就可见，格式保持极简：`资产 ID｜名称｜锁什么`。它只是后续 ASSET_BUILD 的计划队列，不构成第三个审批门。

有效批准例如：“分镜确认”“按这 N 张开始”“28张分镜确认，开始生成”。

一旦用户确认，视为同时确认当前分镜及其固定资产交接。**不要再解释 N、不要重新询问要固定哪些资产、不要询问先生成哪几张，也不要因为工具上限重新规划。** 立即进入 `STORYBOARD_LOCK → ASSET_BUILD`，并把已展示的 `FIXED_ASSET_HANDOFF` 直接转成资产队列。

## 3. 修改导致的回退

- STORY_LOCK 后修改主角、起因、核心事件、主要结果或核心观看欲望：故事锁失效，下游失效。若分镜阶段才发现上述内容本身导致首刷看不懂、关键跳转无桥接或结尾需要猜，提出最小 Story 修订并重新走故事确认，不靠加 Frame / 字幕硬救。
- STORYBOARD_LOCK 后修改 N、顺序、核心可见事实，或实质改变“谁先看到 / 第一第三视角体验 / 揭示意图”：分镜锁失效，受影响下游失效。
- 同一 Frame 内只微调现实机位高度/距离、裁切、动作时点、现场光、Prompt，不改变核心事实与观看意图：不解锁。

## 4. Frame Manifest：N 的唯一真相

分镜确认时冻结：

```text
N = 28
F01 = ...
F02 = ...
...
F28 = ...
```

之后：

- 正式图只允许 `F01...FN`；
- `next_frame` 永远是最小未完成 Frame ID；
- 返修覆盖同一 Frame ID；
- 资产统一 `A-*`，不占 Frame ID；
- 测试图统一 `T-*`，不占 Frame ID；
- 不创建 `F(N+1)`。

如果执行中发现确实缺一张，先尝试在现有 Frame 内解决；仍不行再停止并提出最小 storyboard 变更，等用户重新确认。

## 4.5 INTAKE 只做一次上游保险，不变成第二个 Topic Miner

进入 `STORY_DRAFT` 前只确认三件事：第一层异常以后有没有自然下一步；下一步有没有机会产生新的事件价值；题目是否需要 Production 临时发明无关的大机制才能撑长。

若题目只是一个漂亮对象 / 完整机制说明，或标题已经等于最终大景且后续只剩物理接近，保持 `INTAKE`，指出最小缺口并等用户确认。不要在这里重新发散一整批候选，也不要替用户自动换题。

这个检查不增加新的状态、审批门或 Handoff 字段。通过后立刻按原流程进入 `STORY_DRAFT`。

## 5. 主流程

```text
Topic / 用户输入
→ INTAKE 内部题目生命力检查：题目余量 / 第一层之后是否自然长出下一步 / 是否必须靠外挂第二套大机制续命
→ 若明显失败：保持 INTAKE，提出最小 Topic 修正并等待用户确认，不进入 Story Draft
→ 通过后内部写一句 MAIN_VIEWING_PULL（不新增状态/输出）
→ 故事初稿
→ 一次故事预检：入口 / 首刷四问 / 机制演示风险 / 中后段实质升级 / 重大切换桥接与关键反转落地 / 主观看牵引 / Payoff Payment / 表层故事 / 视觉可行性
→ 唯一正式故事
→ STOP: WAIT_STORY_APPROVAL
→ STORY_LOCK
→ 内部建立 REALITY_BASELINE_CANDIDATE + MECHANISM_LOCK_CANDIDATE
→ 判断哪些段落只需“知道”，哪些值得“亲眼看清”
→ 展开 / 压缩
→ 为每张确定视觉任务、真实观察位置、动作瞬间、揭示程度、现实记录条件/成像结果
→ 一次性决定 N
→ 节奏 / 连续性 / 现实底盘 / F01 检查
→ 唯一正式 N 张分镜
→ 同轮提取并展示 FIXED_ASSET_HANDOFF（资产 ID｜名称｜锁什么）
→ STOP: WAIT_STORYBOARD_APPROVAL
→ 用户确认
→ STORYBOARD_LOCK(N)，同时沿用已展示的 FIXED_ASSET_HANDOFF
→ ASSET_BUILD
→ 逐个实际生成并检查 A-*；失败只重做当前 A-*
→ 队列清空
→ ASSET_LOCK
→ 从 F01 开始正式图，每轮连续 9 张
→ REVIEW
→ REPAIR
→ COMPLETE
```

## 6. ASSET_BUILD：交接清单必须真正接上执行

分镜确认后的第一项生图必须是资产，不是正式 Frame。

资产队列默认**直接来自用户刚刚看到并确认的 `FIXED_ASSET_HANDOFF`**，不要在确认后重新从分镜零散推断一份新清单。Asset ID 在交接与实际生成之间保持一致。

如果执行前发现确有一个明显漏掉、且会导致跨帧漂移的对象，只允许做极小的 `ASSET_HANDOFF_PATCH` 文本补充并说明原因；不得趁机重构分镜或扩成资产项目管理表。

资产队列只是一行行文本顺序，例如：

```text
A-P01 → A-P02 → A-S01 → A-S02 → A-R01
```

执行规则：

1. 当前队列头部就是 `NEXT_TOOL_ACTION`；
2. 每次只生成这一个 Asset ID；
3. 看实际图；
4. 合格才移到下一个；失败仍停在同一 ID；
5. 队列清空才 `ASSET_LOCK`。

禁止：

- 分镜提交时只说“确认后会生成资产”，却不告诉用户具体会生成哪些固定资产；
- 用户确认后把之前的资产交接丢掉，再从零猜一份不同资产队列；
- 只写人物/场景设定就宣布资产完成；
- A01–A20 总资产板；
- 从总板裁切某格当资产；
- 失败/漫画化资产继续做 reference；
- 资产队列未清空就进入 F01。

如果平台在资产阶段因为单轮限制停止，状态保持在当前 `ASSET_BUILD`，下一轮从当前 `NEXT_TOOL_ACTION` 继续，不让用户重新选资产。

## 7. 不存在对象的资料处理

如果资产只有画像、壁画、插画、木刻、漫画、游戏设定、概念图：

1. 在资料理解阶段读取图像，提取少量文字识别锚点；
2. 不把原图艺术媒介继续当作身份；
3. 默认不把风格化原图直接送进现实资产生成；
4. 用文字锚点重新建立现实中可存在、可受光、可遮挡、可被相机记录的对象；
5. 非实体异常则建立稳定的“相机记录规律”；
6. 现实 A-* 通过后，正式 Frame 不再重新引入风格化原图。

详见 `asset-stage.md` 与 `authenticity.md`。

## 8. ASSET_LOCK 后：正式 Frame 每轮固定 9 张

用户已经确认 N，不再询问“先生成哪几张”。

取最小未完成 Frame 开始，当前批次固定为最多 9 个连续 ID：

```text
N=28
BATCH 1: F01-F09
BATCH 2: F10-F18
BATCH 3: F19-F27
BATCH 4: F28
```

每一个 Frame 仍是独立完整图片。一个 9 张批次可以在同一轮连续执行 9 个单帧生成动作，但禁止把它们合成一张九宫格/故事板。

### 工具数量限制的处理

如果平台宣告单轮最多 10 张：

- 仍按本 Skill 固定的 **9 张批次**执行；
- 不改 N；
- 不改 Frame 顺序；
- 不提供“任选 9 张”；
- 不要求用户决定先做哪一组。

如果某轮实际只能完成少于 9 张，记录最小未完成 Frame，下一轮从那里继续，直到当前连续 9 张批次补齐或进入下一批。

## 9. 批次内仍然“一帧一图”

对当前批次的每个 Frame：

- 单独准备当前 Frame 的现实拍摄 Prompt；
- 单独得到一张完整 3:4 图；
- 禁止拼贴、多格、前后对比、嵌入标题/字幕/UI；
- 若某一 Frame 返回多格/严重漫画化/连续性错误，该 Frame 失败，只覆盖这个 ID。

为了满足“一次生成 9 张”，不要把 9 个不同场景写进一个 Prompt；它们是同一批次里的 9 个独立生成动作。

## 10. 状态摘要必须写 NEXT_TOOL_ACTION，而不是只写 NEXT 阶段

阶段切换时保持极短：

### 分镜确认后

```text
STATE: ASSET_BUILD
NEXT_TOOL_ACTION: GENERATE A-P01
FORMAL_FRAME: FORBIDDEN
```

### 资产队列清空后

```text
STATE: ASSET_LOCK
NEXT_TOOL_ACTION: GENERATE BATCH F01-F09
```

### 正式生成中

```text
STATE: GENERATE
N: 28
DONE: F01-F09
NEXT_TOOL_ACTION: GENERATE BATCH F10-F18
```

如果因为平台限制本轮停下：

```text
STATE: GENERATE
N: 28
DONE: F01-F06
NEXT_TOOL_ACTION: GENERATE F07-F09, THEN CONTINUE F10-F18
```

状态用于控制下一次真实动作，不作为额外审批层。

## 11. REALITY / MECHANISM 预锁：不新增审批门

`STORY_LOCK` 后、正式切 Frame 前，内部先建立：

```text
REALITY_BASELINE_CANDIDATE
MECHANISM_LOCK_CANDIDATE   # 只有普通连续性不足以保护的关键视觉关系存在时才有
```

Reality Baseline 锁现实世界如何成立和拍摄条件如何随处境变化，**不锁统一色调、统一天气、统一漂亮灯光或统一“纪实风格”**。

Mechanism Lock 只写 1–5 条“不稳定就会让核心理解/回报失效”的硬关系，优先覆盖材质身份、物理同源、结构映射、尺度身份和状态连续。能由普通人物/场景/道具资产与连续性解决的，不升级成 Mechanism Lock；没有这类关键关系的故事明确 `MECHANISM_LOCK: NONE`，不要为了形式创造机制。

如果一条关系跨多个对象，预锁时同时判断执行模式：
- `SOURCE_DERIVATION`：对象确实物理同源，后续资产必须母版 → 直接派生；
- `RELATION_REFERENCE`：对象只是共享几何/拓扑/比例/排列/空间关系，后续各自建立现实资产，只共同遵守关系规格，不能从一张图片母版换材质派生。

两者与正式 Storyboard、`FIXED_ASSET_HANDOFF` 在同一轮展示，由一次 `WAIT_STORYBOARD_APPROVAL` 一起确认。用户确认后同时冻结：

```text
STORYBOARD_LOCK(N)
REALITY_BASELINE_LOCK
MECHANISM_LOCK   # 如存在
FIXED_ASSET_HANDOFF
```

不增加 `WAIT_REALITY_APPROVAL` 或 `WAIT_MECHANISM_APPROVAL`。

## 12. 每个 9 张批次完成后做一次内部组检，再进入下一批

每批正式图完成后，不向用户新增审批门，内部快速检查四件事：

1. **统一美术化回归**：不同地点、时间和拍摄处境是否无理由共享同一套漂亮灯光、调色、构图或摄影品质；
2. **拍摄条件兑现**：Storyboard 已经设计的现实记录条件是否真的来自当前处境；稳定场景不必故意做坏，困难场景也不能被拍得异常完美；
3. **Mechanism Lock（如有）**：材质、尺度、状态是否漂移；物理同源是否仍可验证；结构映射是否仍可辨认且没有退化成复制贴图 / 统一材质；
4. **Frame ID**：已完成 ID 不得在下一批再次生成，除非明确进入 `REPAIR` 覆盖同一 ID。

发现系统性问题时，先在当前批次内对受影响 Frame ID 做局部 REPAIR，再进入下一批；不增加 N，不把修复图算新 Frame。
