# 常用 Prompt 合集

> 说明  
> 1. 这是适合直接放到 GitHub Markdown 文档里的版本。  
> 2. GitHub 会给代码块自动提供复制按钮，所以每个 Prompt 都单独放在代码块中。  
> 3. 每个 Prompt 下面都单独列出该 Prompt 的变量，方便替换。  
> 4. 涉及账号、密码、Token 的内容，建议始终使用占位符，不要把真实密钥直接提交到仓库。  

---

## 1. w-代码提交前检查 + 自动提交 + 发起 PR

### Prompt
```text
请先使用可用的 skills / tools 对我当前工作区与变更代码做一次完整检查，再决定是否执行提交与 PR。

任务目标：
1. 检查我当前分支 `<CURRENT_BRANCH>` 上所有待提交代码，确认是否符合仓库规范、项目要求、已有 cards / stories 以及主流程约束。
2. 重点检查：
   - 功能是否完整
   - 是否存在明显 bug / edge case / 回归风险
   - 命名、目录结构、注释、可维护性是否合理
   - 是否有权限、数据隔离、接口调用、状态处理、异常处理方面的问题
   - 是否存在 lint、type、test、build、依赖或安全风险
3. 输出一份简明检查结论：
   - `PASS`：可以提交
   - `BLOCKED`：暂不建议提交
4. 如果是 `BLOCKED`，请列出必须先修复的问题，并按优先级排序。
5. 如果是 `PASS`，请直接继续执行：
   - 将当前变更提交到分支 `<CURRENT_BRANCH>`
   - 生成规范的 commit message
   - 发起从 `<CURRENT_BRANCH>` 到 `<TARGET_BRANCH>` 的 PR
6. 如果你所在环境无法直接执行 git push / 创建 PR，请不要假装已完成；请给出：
   - 建议的 commit message
   - PR title
   - PR description
   - 我只需要复制执行的命令

输出格式：
- 检查范围
- 风险结论（PASS / BLOCKED）
- 发现的问题（如有）
- 建议的 commit message
- 建议的 PR 标题与描述
- 如可执行则直接执行；如不可执行则给出下一步最短操作命令
```

### 变量
- `<CURRENT_BRANCH>`：当前工作分支，例如 `feat-uploadpage`
- `<TARGET_BRANCH>`：目标分支，例如 `init-ai`

---

## 2. w-基于已有 Cards / Stories 生成新的 User Story 文档

### Prompt
```text
请先使用可用的 skills / templates，根据已有 stories 的风格与结构，帮我写一份新的 User Story 文档。

背景：
- Policy：`D11 Data isolation`，要求用户只能看到自己的 secards。
- 已有关联 Cards / Stories：
  - `CARD-054`：Upload page implementation
  - `CARD-052`：file_uploads collection
  - `CARD-056`：Navigation updates

我需要新增的 User Story：
当用户点击“上传文件”时，不能直接上传；应先弹出一个对话框，让用户选择报表上传类型：
- `day`
- `week`
- `month`

只有在用户完成类型选择后，才允许继续上传。

请你完成以下内容：
1. 先理解并继承已有 Cards / Stories 的上下文与命名风格。
2. 输出一份结构完整的新 User Story 文档。
3. 文档中至少包含：
   - Title
   - Background / Context
   - User Story
   - Scope
   - Acceptance Criteria
   - Validation Rules
   - Error / Edge Cases
   - Dependencies / Related Cards
   - Out of Scope
4. Acceptance Criteria 要写得可测试、可验收。
5. 请特别覆盖这些规则：
   - 未选择 `day / week / month` 时，不能上传
   - 选择后才允许继续
   - 用户能明确知道当前所选类型
   - 取消对话框后的行为要定义清楚
   - 需要考虑错误提示与空状态
6. 请使用偏产品 / 交付文档的语言，不要写成纯代码说明。
7. 最终请输出为一份可直接保存为 `.md` 的文档内容。
```

### 变量
- `CARD-054`：已有相关卡片，Upload page implementation
- `CARD-052`：已有相关卡片，file_uploads collection
- `CARD-056`：已有相关卡片，Navigation updates
- `D11 Data isolation`：数据隔离策略名称

---

## 3. w-将指定 Card 文档同步到 Synque / 目标系统（安全占位版）

### Prompt
```text
请帮我把 `<CARD_FILE>` 同步到 Synque ，并在执行前先做一次完整校验。

执行参数：
- email: `<EMAIL>`
- password: `<PASSWORD>`
- DIRECTUS_PRD_ENDPOINT: `<DIRECTUS_ENDPOINT>`
- DIRECTUS_PRD_TOKEN: `<DIRECTUS_TOKEN>`
- Author: `<AUTHOR_NAME>`

执行要求：
1. 先读取并理解 `<CARD_FILE>` 的内容。
2. 检查文档格式、字段完整性、标题、描述、验收标准、关联信息是否满足同步要求。
3. 如果发现缺失项或格式问题，先列出问题并修正后再同步。
4. 同步时请保留作者信息为 `<AUTHOR_NAME>`。
5. 同步完成后，返回：
   - 是否成功
   - 同步到的对象名称 / ID / 链接（如果可获得）
   - 最终写入的关键字段摘要
6. 如果你所在环境无法直接执行同步，请不要假装已完成；请输出：
   - 需要我手动执行的命令或步骤
   - 每一步的预期结果
   - 最容易出错的检查点

输出风格：
- 简洁
- 可执行
- 先校验，后同步
- 不要跳过失败检查
```

### 变量
- `<CARD_FILE>`：需要同步的卡片文档文件名，例如 `CARD-056.md`
- `<EMAIL>`：登录邮箱
- `<PASSWORD>`：登录密码
- `<DIRECTUS_ENDPOINT>`：环境地址
- `<DIRECTUS_TOKEN>`：API Token
- `<AUTHOR_NAME>`：作者名，例如 `Allen`

---

## 4. w-对齐工作区项目功能、Cards、Stories、Synque 的一致性

### Prompt
```text
use our skill, 请帮我做一次一致性对齐检查，范围包括：
- 工作区项目当前功能实现
- Cards
- Stories
- Synque / 第 3 个 prompt 中对应的同步对象

目标：确认这几部分是否表达一致、实现一致、验收一致。

请按以下步骤完成：
1. 先梳理当前项目实际实现了哪些功能，以及每个功能的边界。
2. 对照 Cards 与 Stories，检查是否存在：
   - 文档有但实现没有
   - 实现有但文档没写
   - 命名不一致
   - 流程不一致
   - 验收条件不一致
   - 依赖关系不一致
3. 再把上述结果与 Synque 中的内容做一次对齐。
4. 对每个不一致项给出：
   - 类型（缺失 / 冲突 / 命名不一致 / 验收不一致 / 状态不一致）
   - 影响范围
   - 建议修复方式
   - 优先级
5. 最后输出一个总结：
   - 已对齐项
   - 未对齐项
   - 建议先修什么
   - 修完后还需要复查什么

输出格式请使用表格 + 简明结论，尽量让我一眼看出问题在哪里。
```

### 变量
- `第 3 个 prompt`：指向同步 Synque 的那个 Prompt，用于和同步对象做一致性核对
- `工作区项目`：当前项目代码与功能实现范围
- `Cards / Stories / Synque`：需要共同核对的一组对象

---

## 5. 一步一步指导模式（可追加到其他 Prompt 后面）

### Prompt
```text
交互要求：
1. 请一步一步地指导我完成，不要一次输出很多步骤。
2. 每一轮只告诉我“当前最应该做的下一步”。
3. 等我执行完并把结果返回给你后，你再继续给我下一步。
4. 如果我返回的结果有问题，请先帮我判断问题原因，再给我新的下一步。
5. 每次回复尽量简短、明确、可直接执行。
6. 除非我主动要求，否则不要一次给出完整方案。
```

### 变量
- 无固定变量  
- 建议作为附加模板，直接拼接到其他 Prompt 末尾使用

---

## 6. 苏格拉底反问

### Prompt
```text
在回答或给出方案之前，请先通过提问充分理解我的真实需求。

请严格遵守以下规则：
1. 先问问题，不要直接给答案或方案。
2. 每次只问一个问题。
3. 等我回答后，再根据我的回答继续追问下一个最关键的问题。
4. 你的问题要尽量简短、明确、聚焦，优先确认目标、约束、场景、预期结果和成功标准。
5. 如果我的描述含糊、存在歧义、信息不足或前后矛盾，请继续追问，不要自行脑补。
6. 在你对我的真实需求、目标、边界条件和期望输出形式达到至少 95% 的把握之前，不要进入方案阶段。
7. 当你认为信息已经足够时，先用简短的话总结你对我需求的理解，并请我确认。
8. 只有在我确认你的理解基本正确后，你再开始给出方案。
9. 给出方案时，请确保方案与我们前面的问答内容严格一致，不要偏题，也不要遗漏我明确提出的要求。

你的整体沟通方式要求：
- 一轮只做一件事：要么提一个问题，要么在确认后给方案。
- 不要一次抛出多个问题。
- 不要提前输出大段分析。
- 不要用模板化、敷衍式追问。
- 以帮助你准确理解我的真实目标为最高优先级。
```

### 变量
- ` 苏格拉底问法

---

## 7. 从零开始编写油猴脚本：自动续期 + 页面右下角 Debug 面板

### Prompt
```text
我要从零开始创建一个 Tampermonkey（油猴）脚本，请你担任我的脚本设计与开发助手。

在你给出方案、代码或实现步骤之前，请严格遵守以下沟通规则：
1. 先向我提问。
2. 一次只问一个问题。
3. 根据我的回答继续追问。
4. 只有当你对我的真实需求、页面环境、风险点、执行目标有至少 95% 把握时，才开始输出方案。
5. 在此之前，不要直接给完整代码。

脚本目标：
实现一个“自动续期”脚本，并在页面右下角提供 debug 面板，方便排查流程卡在哪一步。

核心续期逻辑如下：

① 进入页面：
- URL：`https://web.freecloud.ltd/index.php?rp=/service-renewals`
- 页面开始会有 Cloudflare 人机验证。
- 脚本必须先等待我手动通过验证，再继续后续流程。
- 只有检测到以下元素出现时，才表示验证已通过并允许进入下一步：
  - 元素文本：`新加坡DirectAdmin - 新加坡Webfreecloud`
  - XPath：`/html/body/div[2]/div[2]/div/div[2]/div[3]/div[2]/div/div/div/div[1]/h3/span`

② 点击“添加到购物车”按钮：
- XPath：`/html/body/div[2]/div[2]/div/div[2]/div[3]/div[2]/div/div/div/div[2]/div/button`

③ 等待跳转到购物车页面：
- 目标 URL：`https://web.freecloud.ltd/cart.php?a=view`
- 只有检测到以下元素后，才算成功进入下一步：
  - XPath：`/html/body/div[2]/div[2]/div/div[2]/div[1]/form/div[2]/div[2]/div[2]/div[1]/div/div[1]/div/div/label/span/svg`

④ 编辑页面元素：
- XPath：`/html/body/div[2]/div[2]/div/div[2]/div[2]/div/div[2]/label/div`
- 将：`<div class="checkbox-styled">`
- 改为：`<div class="checkbox-styled checked">`

⑤ 点击“结账”按钮：
- XPath：`/html/body/div[2]/div[2]/div/div[2]/div[2]/div/div[1]/div[4]/div[2]/button`

请在真正开始写脚本前，围绕以下方向逐步向我确认：
- 这个脚本是完全自动还是半自动
- 是否允许使用轮询、MutationObserver、定时器重试
- 页面是否可能改版，是否需要更稳健的选择器策略
- debug 面板需要显示哪些状态 / 日志 / 错误
- 失败后是停止、重试还是回滚
- 是否需要开始/暂停按钮
- 是否需要仅在特定 URL 生效
- 是否有账号安全、频率限制、风控限制方面的顾虑

当你确认完问题后，再按以下结构输出：
1. 需求确认总结
2. 风险点与假设
3. 脚本架构设计
4. 完整油猴脚本
5. 关键函数说明
6. 调试方式
7. 常见故障排查

代码要求：
- 可直接保存为 `.user.js`
- 注释清晰
- 带右下角 debug 面板
- 每一步都能显示当前状态
- 出错时要有明确日志
- 尽量避免脆弱实现
```

### 变量
- `https://web.freecloud.ltd/index.php?rp=/service-renewals`：续期入口页
- `https://web.freecloud.ltd/cart.php?a=view`：购物车页
- `/html/body/div[2]/div[2]/div/div[2]/div[3]/div[2]/div/div/div/div[1]/h3/span`：验证通过后的目标元素 XPath
- `/html/body/div[2]/div[2]/div/div[2]/div[3]/div[2]/div/div/div/div[2]/div/button`：添加到购物车按钮 XPath
- `/html/body/div[2]/div[2]/div/div[2]/div[1]/form/div[2]/div[2]/div[2]/div[1]/div/div[1]/div/div/label/span/svg`：购物车页成功进入的目标元素 XPath
- `/html/body/div[2]/div[2]/div/div[2]/div[2]/div/div[2]/label/div`：需要修改 class 的元素 XPath
- `/html/body/div[2]/div[2]/div/div[2]/div[2]/div/div[1]/div[4]/div[2]/button`：结账按钮 XPath

---

## 8. 总结《鸟哥的 Linux 私房菜》某一部分内容（精简易懂版）

### Prompt
```text
<task>
总结《鸟哥的 Linux 私房菜》指定部分内容
</task>

<context>
我会把《鸟哥的 Linux 私房菜》中的部分内容发给你。请你帮我做一份“够用、好懂、容易吸收”的总结。

我的要求是：
- 只讲核心概念，不要讲太深
- 命令只举典型例子，不要展开太多细节
- 内容尽量精简，我不需要一次记住太多
- 表达尽量通俗，不要太书面化
- 结构清晰，方便我快速浏览
- 如果适合，请加一个简洁的图表 / 表格，帮助我区分知识点

后续如果我需要更深入，我会再问你。
</context>

<instructions>
1. 先阅读并理解我发给你的内容。
2. 提取最核心的概念、作用和常见使用场景。
3. 用通俗、简洁的语言重写，避免堆砌术语。
4. 每个知识点尽量控制在“看一眼就能懂”的粒度。
5. 如需举例，只给最典型、最常见、最容易理解的命令示例。
6. 不需要覆盖所有细枝末节，优先保证我能快速建立整体认知。
7. 如果内容适合做对比或分类，请补一个简洁图表（ASCII / 文本表格均可）。
8. 总结时优先突出：
   - 这部分在讲什么
   - 为什么重要
   - 我实际会在什么场景碰到它
   - 我至少应该记住什么

<output_format>
请按以下结构输出：
1. 一句话概述
2. 核心知识点（列表）
3. 典型命令示例（只保留最必要的）
4. 一个简洁图表 / 表格，帮助理解或区分概念
5. 最后补一个“你现在至少记住这几件事”小结

要求整体风格：
- 精简
- 通俗
- 结构清楚
- 方便快速吸收
</output_format>
```

### 变量
- `《鸟哥的 Linux 私房菜》指定部分内容`：你后续会提供的原文内容
- `图表 / 表格`：可选输出形式，用于帮助快速理解知识点

---





## 使用建议

1. 涉及真实凭据时，优先使用占位符。  
2. 发送给 AI 前，再临时替换成真实值。  
3. 需要交互时，把 Prompt 5 追加到目标 Prompt 末尾。  
4. 需要沉淀文档时，要求对方直接输出 `.md` 内容。  
