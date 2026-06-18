# guided-dream-interpretation

一个用于梦境探索的Skill。

该 Skill 将“解梦”处理为结构化、以用户为解释主体的探索流程。它借鉴现象学访谈、以人为中心的对话原则和叙事心理学中的意义建构视角，不使用固定的梦境符号词典，也不输出心理或医学诊断，而是通过多轮追问，帮助用户基于梦中的情绪、画面、现实处境和个人联想，形成更贴近自身经验的叙事性解读。

## **方法论基础**

- **现象学取向**：悬置预设的符号解释，优先回到用户描述的具体体验。
- **以人为中心的对话原则**：解释权归用户，AI 作为非指导性的探索促进者，而不是替用户下结论的专家。
- **叙事心理学视角**：将梦境理解为个人意义建构的一部分，而非待解码的符号系统。
- **证据导向**：强结论需要至少两条相互独立的支持性线索；证据不足时保留不确定性。

## 适用场景

当用户分享梦境，或请求解释、探索、理解梦境时使用，例如：

- “帮我解梦”
- “我昨晚梦到……”
- “这个梦是什么意思”

## 工作流

1. **定位说明**  
   首次进入梦境探索时，用一句话自然说明：这是引导式自我探索，不是心理咨询或诊断。

2. **完整听取**  
   保留用户原话，不急于解释。先让用户完整讲述梦境。

3. **安全判断**  
   按 `references/safety-routing.md` 判断是否需要先确认继续意愿，或在必要时核对现实安全状态。

4. **结构化追问**  
   按 `references/questioning-method.md` 执行。至少进行三轮追问，每轮只推进一个理解目标，帮助用户从具体画面、情绪和现实联想中探索梦境意义。

5. **生成解梦报告**  
   按 `references/report-method.md` 在聊天界面生成叙事性报告。报告完成后，必须以固定归档询问结尾：“要不要收藏进梦境档案馆？”

6. **写入梦境档案**  
   用户明确同意后，按 `references/archive-method.md` 将本次梦境记录写入 `assets/dreams-data.js`，并提供 `assets/dream-archive.html` 作为档案查看入口。用户未明确同意则不写入。

## 目录结构

```text
guided-dream-interpretation/
├── SKILL.md                                    # 主流程与硬边界
├── references/
│   ├── questioning-method.md                   # 追问方法
│   ├── report-method.md                        # 报告规范
│   ├── safety-routing.md                       # 安全分流
│   ├── archive-method.md                       # 存档规范
│   └── golden-cases.md                         # 参考案例
└── assets/
    ├── dreams-data.js                          # 档案数据源
    └── dream-archive.html                      # 档案展示页
```

## 核心文件

### `SKILL.md`

主入口，定义 Skill 的触发条件、核心姿态、主工作流和硬边界。

### `references/questioning-method.md`

追问方法论，包括先承接再提问、每轮一个问题、五个回答入口、用户否定后不再推进，以及从抽象解释退回具体画面或身体感受。

### `references/report-method.md`

报告生成规范。报告应基于用户确认过的材料展开，包含备选理解，并在结尾落到用户已经表现出的能力、界限、需要或正在保护的东西上。

### `references/safety-routing.md`

敏感内容处理规则，包括何时继续探索、何时确认继续意愿、何时核对现实安全状态。

梦中出现死亡、暴力、恐怖画面，并不自动等同于现实风险。

### `references/archive-method.md`

档案写入规则。仅在报告完成且用户明确同意后写入；用户拒绝、忽略或回复含义不明确时不写入。

### `references/golden-cases.md`

质感参考案例，用于校准语气、节奏、追问方式和报告展开程度。

## 梦境档案

档案功能由以下文件实现：

```
assets/dreams-data.js
assets/dream-archive.html
```

### `assets/dreams-data.js`

档案数据源。该文件通过 `window.DREAMS` 暴露梦境记录数组，供档案页面读取。

单条记录结构如下：

```js
{
  date: "YYYY-MM-DD",
  title: "...",
  mood: "A",
  core: "...",
  tags: ["...", "...", "..."],
  dream: "...",
  report: "..."
}
```

新记录应插入数组最前面，使档案默认按时间倒序展示。

### `assets/dream-archive.html`

梦境档案的静态展示页面。

该页面直接读取同目录下的 `dreams-data.js`，并在浏览器中渲染梦境列表、情绪分类、标签、原始梦境文本和完整报告。它不依赖后端服务，也不需要构建步骤，适合随 Skill 一起作为本地可视化入口使用。

当用户在一次梦境报告完成后同意收藏，本 Skill 会更新 `dreams-data.js`；随后用户可以打开 `dream-archive.html` 查看已保存的梦境记录。

### 数据与视图分离

`dreams-data.js` 是数据层，`dream-archive.html` 是展示层。

新增梦境记录时，应只更新 `dreams-data.js`。不要将梦境记录硬编码进 `dream-archive.html`，以保持展示页面结构稳定。

## 行为边界

本 Skill 不应：

- 使用固定象征解释，例如“水代表情绪”“死亡代表重生”
- 做心理、医学或创伤诊断
- 将恐怖梦境自动判断为现实危险
- 在用户否定后继续推进同一解释方向
- 向用户暴露内部轮次、证据数量或安全分流判断
