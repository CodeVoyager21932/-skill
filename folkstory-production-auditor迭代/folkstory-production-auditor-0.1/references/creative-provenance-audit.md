# Creative Provenance Audit｜审作品指纹，不把成熟 story grammar 当抄袭

这份文件在用户要求原创 / 独具创意，或当前项目已经暴露 benchmark / Library 时使用。

目标是区分：

- Prompt 有没有偷偷改变用户创意要求；
- 新作有没有高度依赖已知作品指纹；
- 合法学习成熟 story grammar 是否被错误判成来源污染。

## 1. Prompt 来源

建立：

- `USER_EXPLICIT`
- `TASK_REQUIRED`
- `ASSISTANT_INVENTED`

模型把宽泛创作请求扩写成乡村、失踪、祖屋、葬礼、某职业 / 怪物等方向并据此生成，记录 `PROMPT_DRIFT = YES`。

中性流程补充不算 drift。

## 2. Context Split

已暴露 benchmark 要拆成：

### `TRANSFERABLE_STORY_GRAMMAR`

入口功能、Story Debt、story spine、人物为什么继续、中段惯性、节奏、payoff function、情绪变轨、图片叙事职责。

用户明确要求参考 benchmark 时，这些可以合法学习。

### `SIGNATURE_QUARANTINED`

原人物 / 具体角色组合、地点生态、标志物 / 怪物 / 场面、独特机制实现、高识别证据方式、可映射的推进 / 反转链、原结局、独有标题 / 表达。

本轮没重新打开文件，也不能忘记这些指纹已经暴露。

## 3. Composite Leakage 看三件事

仍可拆：入口债务、关系、场景、异常载体、证据、推进、payoff。

但不要机械用“相似三个槽位”判失败。判断：

- `SPECIFICITY`：相似的是通用语法还是具体实现；
- `COMBINATION`：具体元素组合后是否指向某篇 / 几篇已知作品；
- `SALIENCE`：相似部分是否恰好承担新作最有辨识度、最值钱的内容。

提高风险：

- 入口到 payoff 可逐段映射；
- 多个签名组件高度组合；
- 新作最好的部分都来自对标指纹，新增只剩换名词；
- 候选池整体滑向同一批具体表面生态。

不提高风险：

- 都使用失踪、返乡、调查、探索、封闭空间、近身威胁；
- 都使用“旧事件被新证据重启”“一路进入未知”“威胁越来越近”等成熟 spine；
- payoff 功能相似，但具体对象 / 事件 / 关系完全不同。

## 4. 输出

`CREATIVE_PROVENANCE_AUDIT: PASS / RISK / FAIL / NOT_ASSESSED`

可补：

- `PROMPT_DRIFT: YES / NO / UNKNOWN`
- `CONTEXT_LEAKAGE_RISK: YES / NO / UNKNOWN`
- `COMPOSITE_LEAKAGE_RISK: YES / NO / UNKNOWN`

判定：

- `PASS`：没有实质 Prompt 注入，没有高辨识指纹依赖；
- `RISK`：存在明显具体相似组合，但过程证据不足，或表面生态开始漂移；
- `FAIL`：有明确过程证据显示模型直接借签名组件，或新作核心能被清楚映射到已知作品指纹；
- `NOT_ASSESSED`：材料不足。

一个题可以同时高质量 + provenance FAIL；也可以大量使用成熟 story grammar + provenance PASS。

## 5. 修复原则

- Prompt drift：回真实用户原话；
- Signature leakage：保留新作真正独立的入口 / 核心锚点，只换掉过近的签名组合；
- Composite leakage：优先局部拆开高度识别组件，不因为来源风险推翻所有成熟 story grammar；
- 不为了证明原创强迫作品增加第二机制；
- 不因为本轮强调“可借成熟结构”就放过真正换皮。
