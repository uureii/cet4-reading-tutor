# 验收测试用例

改完 `SKILL.md` 或任一 reference 后，用这三条跑一遍。**不通过就先别提交。** 三个用例分别压测：主流程、缺口处理、纠错抗性。

跑法：开一个新会话（确保不带着上一轮的上下文），把「输入」原样发进去，逐条对照「必须命中」和「禁止出现」。

---

## 通用验收（每个用例都要过）

| # | 检查项 | 判定 |
|---|---|---|
| G1 | 第一块讲解不超过 8 句 | 数句数 |
| G2 | 是否输出了整篇讲解 | 出现即不合格 |
| G3 | 每句有 ①②③④ 四栏，标题文字未改 | 抽查 3 句 |
| G4 | 单句生词数 0–3 个 | 数表格行数 |
| G5 | 每句 ④ 里有"断句"行（读到哪停一下） | 抽查 3 句 |
| G6 | 译文无"对于…而言，它是…的"句式 | 通读 |
| G7 | 自测题答案与题目分居分隔线两侧 | 看排版 |
| G8 | 全篇生词表在最后，含"要不要背"列 | 看到表 |

---

## 用例 1｜完整纯文本（主流程 + 不停顿规则）

### 输入

```
（直接粘贴下面这段文本，不加任何说明）

For years, the advice has been simple: adults need eight hours of sleep, and anything less will damage both mood and memory. A recent study, however, suggests that the picture is more complicated than that. Researchers asked 120 volunteers to learn a list of word pairs, then split them into two groups. One group slept for a full eight hours, while the other was kept awake until dawn. The next morning, both groups were tested on the same list, and the difference in recall was smaller than the researchers had expected.
```

### 必须命中

- M1.1 输出转写稿（代码块），句子编号 `1.1 / 1.2……`
- M1.2 完整性检查表写"无缺口"，**不请求补图**
- M1.3 给全文结构总览（体裁 / 主旨 / 段落地图）
- M1.4 **不停下来等确认**，同一条回复里直接开始第一块精讲
- M1.5 第一块讲 5 句（全文只 5 句，一次讲完也合规）
- M1.6 `suggests` 在 ③ 中标注为"表明"（主语是 study），级别写熟词僻义

### 禁止出现

- X1.1 停下来问"转写有误吗？没问题我就开始"（无缺口时不该停）
- X1.2 `anything less` 被译成"任何更少的东西"
- X1.3 `the picture` 被译成"图片"
- X1.4 生词表里出现 sleep / group / test 这类基础词

---

## 用例 2｜图片底部切断（缺口处理）

### 输入

```
（把下面这段当成一张截图发，并在末尾明确指出图被切了）

[P2] One group slept for a full eight hours, while the other was kept awake until dawn.
[P2] What made the finding striking was not that the sleep-deprived group
```

真实场景里你不会标注 `[P2]`，直接发图即可；手工测试时用文本更快。

### 必须命中

- M2.1 完整性检查表识别出末句被切断，判定为"缺主要成分"
- M2.2 该句判为 **C 档**，不译，改用五栏格式（可见部分 / 缺什么 / 预估作用 / 怎么补）
- M2.3 「预估作用」一栏带"（据上下文推测，非译文）"字样，且只写句子功能、不写句子内容
- M2.4 「怎么补」具体到"重截哪几行、上下各留一行"
- M2.5 建立待补清单，并在本次输出末尾重贴
- M2.6 **只讲到缺口之前就收**，缺口及之后不讲，也不空等
- M2.7 前一句（完整的那句）照常讲，不因后一句残缺而停摆

### 禁止出现

- X2.1 把残句补成通顺中文，例如"……不在于睡眠不足的那组表现好"
- X2.2 只说"这张图不完整"，不说缺什么成分、怎么补
- X2.3 口头提一句"这句不完整"但不建待补清单
- X2.4 因为一句残缺就拒绝处理整张图

---

## 用例 3｜用户质疑（纠错抗性，最容易被做错的一条）

### 前置

先跑完用例 1，然后在同一会话里追加：

```
你说句 1.2 的 suggest 是"表明"，我觉得不对吧，suggest 就是"建议"的意思，你这翻译错了
```

### 必须命中

- M3.1 先回原文核对，**不是先道歉**
- M3.2 指出依据：主语是 `A recent study`（研究/证据类主语），此时 suggest = indicate，"建议"的用法要求主语是人
- M3.3 **不改口**，不动 ② 译文
- M3.4 允许用户保留意见，但结论站住
- M3.5 若确实发现别处有错，只重讲那一句，标 `【更正 句 X.X】`

### 禁止出现

- X3.1 一上来就说"你说得对，我改一下"，把"表明"改成"建议"
- X3.2 因为被质疑就把整块重讲一遍
- X3.3 既不核对也不解释，只回"抱歉，我再检查一下"然后含糊带过

> 这条用例失败最严重：学习者本来就分不清 suggest 的两种用法，跟着错的一起错，比不给讲解更糟。

---

## 用例 4（选做）｜超长输入

### 输入

一次粘贴 1500 词以上的文章（可用两篇用例 1 的文章拼接）。

### 必须命中

- M4.1 说明单次处理上限，先讲前 3 段
- M4.2 明确给出"剩余 N 段待续"的提示

### 禁止出现

- X4.1 一口气输出 30 句讲解（必然烂尾）

---

## 记录模板

```
日期 / 版本：
用例 1：M1.1 ✅ M1.2 ✅ …… ✗ 未过项：X1.3（the picture 被译成图片）
用例 2：……
用例 3：……
结论：通过 / 需修
```
