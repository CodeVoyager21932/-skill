# 审查输出格式

默认中文、结论先行、少讲空泛理论。

## 1. TOPIC / STORY / STORYBOARD

使用：

```text
VERDICT: PASS | REPAIR | REDESIGN
P0: x
P1: x
P2: x
```

`TOPIC_AUDIT` 先输出入口层：

```text
TITLE_AUDIT: STRONG | OK | REPAIR | N/A
ENTRY_AUDIT: STRONG | BORDERLINE | WEAK
LATE_HOOK: YES | NO
TITLE_REPAIR_SCOPE: NONE | TITLE_ONLY | ENTRY_AND_TITLE
```

`ENTRY_AUDIT` 在 blind pass 后冻结，后续 Payoff / Deepening 不得调高。标题弱但入口强时用 `TITLE_ONLY`；标题与底层入口都弱时用 `ENTRY_AND_TITLE`.

当用户明确要求高质量 / 独具创意 / 最有上限 / 冠军潜力时，`TOPIC_AUDIT` / `STORY_AUDIT` 先增加一行：

```text
HIGH_BAR_CONTENDER: YES | BORDERLINE | NO | NOT_ASSESSED
```

这条不依赖 benchmark。

`TOPIC_AUDIT` / `STORY_AUDIT` 在存在明确 benchmark 时，再增加一行：

```text
BENCHMARK_COMPETITIVENESS: ABOVE_MEDIAN | AROUND_MEDIAN | BELOW_MEDIAN | NOT_ASSESSED
```

`STORYBOARD_AUDIT` 默认不输出这些创意竞争力判断。

当用户明确要求和 benchmark 最高档比较，而且材料足以识别高水位样本时，再增加：

```text
TOP_TIER_CONTENDER: YES | BORDERLINE | NO | NOT_ASSESSED
```

这条只表示和高水位对标相比的竞争潜力 / 实现度，不进入 P0/P1/P2。`HIGH_BAR_CONTENDER = YES + TOP_TIER_CONTENDER = NO`、`ABOVE_MEDIAN + NO` 都合法；不要把内部高标准或“超过中位数”写成“已经和外部顶档同档”。

如果同时提供同一 Prompt 的有 / 无 benchmark 两轮结果，再增加：

```text
BENCHMARK_DEPENDENCE_AUDIT: PASS | RISK | FAIL
```

当用户要求原创 / 独具创意，或当前项目 / 对话已经暴露具体 benchmark、Library、历史作品时，再增加：

```text
CREATIVE_PROVENANCE_AUDIT: PASS | RISK | FAIL | NOT_ASSESSED
PROMPT_DRIFT: YES | NO | UNKNOWN
CONTEXT_LEAKAGE_RISK: YES | NO | UNKNOWN
COMPOSITE_LEAKAGE_RISK: YES | NO | UNKNOWN
```

`FAIL` 需要过程证据或明确组件依赖；只有最终结果相似、看不到生成过程时优先写 `RISK`。成熟 story grammar 相同本身不判来源失败。

当任务涉及创新平衡 / benchmark-guided 创作，再增加：

```text
CREATIVE_BALANCE_AUDIT: PASS | RISK | FAIL | NOT_ASSESSED
OVER_INNOVATION_RISK: YES | NO | UNKNOWN
IMITATION_RISK: YES | NO | UNKNOWN
```

- `OVER_INNOVATION_RISK`：baseline 已强却无必要叠第二主机制、额外世界、身份 / 时空反转，且观看价值没有同步提高；
- `IMITATION_RISK`：高度具体的 benchmark 人物关系、场景、异常、证据、推进 / payoff 形成可识别映射；成熟 story spine 相同不算；
- `PASS` 不代表必须“创新很多”，只表示当前创新集中且没有明显过度模仿；
- `FAIL` 只在问题已经明显损害作品或形成可识别换皮时使用。

### TOPIC_AUDIT 有多个候选时：先完整披露

在任何“推荐最佳候选”之前，必须先输出：

```text
CANDIDATES_EXPECTED: N
CANDIDATES_REVIEWED: N
```

并覆盖全部候选：

| ID | 候选 | 标题 | 入口 | Late hook | Verdict | 内部高标准* | 来源独立性* | 对标竞争力* | 顶档竞争力* | 最大优点 | 最大风险 | 推荐态度 |
|---|---|---|---|---|---|---|---|---|---|---|---|---|
| T01 | ... | 强/可用/需修 | 强/边缘/弱 | 是/否 | PASS/REPAIR/REDESIGN | 是/边缘/否/未评估 | 通过/风险/失败/未评估 | 高于/附近/低于/未评估 | 是/边缘/否/未评估 | ... | ... | 强推/可做/谨慎/不推荐 |

规则：

- `内部高标准` 在用户明确要求高质量 / 独具创意 / 最有上限 / 冠军潜力时填写，即使没有 benchmark 也要判断；
- `来源独立性` 在用户要求原创，或当前对话 / 项目已经暴露具体 benchmark 时填写；题很好看也可能来源审查失败；
- 创新平衡不强制塞进候选表；若某候选存在明显 `OVER_INNOVATION_RISK / IMITATION_RISK`，写进“最大风险”并在表后汇总 `CREATIVE_BALANCE_AUDIT`；
- `对标竞争力` 只有用户提供 / 指定 benchmark 时填写；没有明确对标就写“未评估”，不要凭空假设市场基线；
- `顶档竞争力` 只有用户明确要求最高档比较、且 benchmark 足以识别高水位样本时填写；否则写“未评估”；
- `入口` 必须来自 blind pass，不读取后续解释；`Late hook = 是` 表示真正有效的观看债务直到第二层才出现；
- 标题弱时明确 `TITLE_ONLY` 或 `ENTRY_AND_TITLE`，不要默认所有标题问题都只改文案；
- 对标竞争力与 P0/P1/P2 分开，不能因为低于中位数就虚构生产错误；
- 一项一行即可，候选多时保持紧凑，但不能省略；
- “强推 / 不推荐”都只是 Auditor 意见，不会把候选从结果中删除；
- 表后可单列 `我的推荐`，解释 Top 1–3；
- 只有用户明确说“只给 Top N”时才允许不展示完整表；
- 如果 Auditor 自己提出了参与比较的替代候选，用 `ALT01...` 一并披露。

多个候选时，如入口发动方式高度聚类，再输出 `POOL_ENTRY_RISK: YES | NO`。

然后根据审查模式继续：

### 最重要的结论
用 2–5 条说清决定作品成败的问题。

### 问题表
`层级 | 严重度 | 问题 | 为什么影响观看/生产 | 最小修复方向`

### 可保留的东西
只列真正值得保护的设计，防止 Repair 把正确能力一起删掉。

### 下一步
明确回到 Topic / Story / Storyboard / Asset / Generation 哪一层。

## 2. FINAL_FRAME_AUDIT

最终图片必须覆盖全部可验证 Frame ID，不能只挑最差几张然后说“其他类似”。

开头：

```text
VERDICT: PASS | REPAIR | REDESIGN
EXPECTED_N: <N or unknown>
ACTUAL_FRAMES: <范围/数量>
P0: x
P1: x
P2: x
```

### 生产合同检查
先报告缺帧、重复、顺序、N、单图/拼贴等。

### 逐帧审查
推荐表格：

| Frame | 判定 | 根因 | 审查结论 |
|---|---|---|---|
| F01 | ✅ | - | 核心任务成立；必要时只写一句 |
| F02 | ⚠️ P1 | GENERATION | ... |
| F03 | ❌ P0 | GENERATION | 要求是...，实际是...，因此... |

通过帧保持简短；问题帧写清**原要求 vs 实际像素**。

无法可靠看清时写 `? UNCERTAIN`，不要硬判。

### 系统性问题
只在确实跨多个 Frame 时总结，例如：

- 核心机制持续漂移；
- 场景/人物资产污染；
- 后半段尺度失控；
- 整组统一电影化；
- 必要过程帧持续缺失。

### Repair Queue
只列需要行动的 Frame/上游设计，按 P0 → P1。

示例：

```text
1. F08 | P0 | GENERATION | 关键对应关系不可辨认 | 同 ID 重做
2. F19 | P0 | STORYBOARD | 原分镜没有把关键状态写清 | 先修分镜再重做
3. A-P01 -> F01-F12 | P1 | ASSET | 人物资产本身概念图化 | 先重做资产
```

### 最终状态

- 有未解决 P0：`STATE: REPAIR`
- P0 来自上游且大量 Frame 受影响：`STATE: REDESIGN`
- 无阻塞问题：`STATE: PASS`

不要因为“整体挺好”跳过 P0，也不要因为几个 P2 拒绝 COMPLETE。
