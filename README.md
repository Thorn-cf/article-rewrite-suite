# Article Rewrite Suite

一个面向长文内容生产的 Codex Skill，用于翻译、洗稿、改写、仿写、多版本生成和长稿分批处理。它会尽量保留原文结构、观点、事实和论证顺序，同时重建表达，让输出更像一篇可直接发布的新稿，而不是机械同义词替换。

## 适合谁

- 需要把已有长文改写成可发布新稿的内容创作者。
- 需要把英文/外文稿翻译成本土化中文稿的人。
- 需要根据一篇样稿仿写同主题或新主题文章的人。
- 需要一篇原文生成多个不同风格版本的运营、编辑或自媒体团队。
- 使用基础模型能力一般、处理长文容易卡住的智能体用户。

## 核心能力

- **翻译**：中译英、英译中、跨语言本土化翻译。
- **洗稿**：保留结构和核心观点，重写表达，输出可发布正文。
- **改写**：润色、降重、增强逻辑、调整语气。
- **仿写**：提取文章结构和风格，用于同主题或新主题创作。
- **多版本生成**：同一篇原文生成多个表达策略不同的版本。
- **长文分批**：维护事实、术语、结构和观点一致性。
- **低模型辅助**：把长文拆成小分块和短提示词，让弱模型也能逐段完成。

## 工作方式

这个 skill 默认采用三段式流程：

```text
结构蒸馏 -> 表达重建 -> 质量与相似风险回检
```

它不会把原文逐句替换，而是先抽取：

- 标题意图
- 章节结构
- 每段功能
- 核心观点
- 事实锁和术语锁
- 风格指纹
- 情绪推进
- 结尾落点

然后按段落功能重新写作。

## 安装

推荐直接克隆到 Codex skills 目录：

```bash
git clone git@github.com:Thorn-cf/article-rewrite-suite.git ~/.codex/skills/article-rewrite-suite
```

或者下载仓库后，把整个目录放到：

```text
~/.codex/skills/article-rewrite-suite
```

安装后重启 Codex，或让 Codex 重新加载 skills。

## 如何触发

显式触发：

```text
使用 $article-rewrite-suite 帮我洗一下这篇稿子，保留结构和观点，输出一篇可发布的新稿。
```

也可以用这些说法触发：

- 洗稿
- 改写
- 仿写
- 降重
- 润色
- 翻译成本土化稿件
- 保留结构但换一种表达
- 生成多篇结构一致的新稿
- 质量不低于原文

## 示例 Prompt

深度洗稿：

```text
使用 $article-rewrite-suite 深度改写这篇文章。要求保留原文结构和核心观点，但重新组织表达，输出可直接发布的正文，不要显示改写说明和质量检查。
```

多版本生成：

```text
使用 $article-rewrite-suite 基于这篇原文生成 3 个版本：一个忠实改写版，一个深度重构版，一个公众号风格版。三版都要保留原文结构。
```

仿写新主题：

```text
使用 $article-rewrite-suite 分析这篇文章的结构和风格，然后用同样的写法仿写一篇关于【主题】的新稿。
```

翻译和本土化：

```text
使用 $article-rewrite-suite 把这篇英文稿翻译并本土化成中文公众号文章，保留结构，语言自然，适合中文读者。
```

长文分批：

```text
使用 $article-rewrite-suite 处理一篇长稿。我会分多次发送，请你维护结构、术语和观点一致性，每次只输出当前部分的可发布改写稿。
```

## 默认输出规则

生成文章时，默认只输出可发布正文：

- 不显示“改写说明”
- 不显示“质量检查”
- 不显示“结构分析”
- 不解释内部工作流
- 直接从标题或正文开始

如果你需要过程信息，可以明确要求：

```text
请同时给我结构分析和质量检查。
```

## 长文和低性能模型工作流

当稿件很长，或某些智能体基础模型能力较弱时，可以先用脚本把任务拆小。

生成任务包：

```bash
python3 scripts/prepare_rewrite_job.py input.md rewrite-job
```

它会生成：

- `rewrite-job/chunks/`：原文分块
- `rewrite-job/prompts/`：每个分块对应的短提示词
- `rewrite-job/rewritten/`：放置改写后分块
- `rewrite-job/manifest.json`：分块顺序和元数据
- `rewrite-job/structure-map-template.md`：结构图模板
- `rewrite-job/stability-locks-template.md`：事实锁/术语锁模板
- `rewrite-job/final-assembly-checklist.md`：合并检查清单

逐块完成改写后，把结果保存到 `rewrite-job/rewritten/`，再合并：

```bash
python3 scripts/assemble_rewrite.py rewrite-job/rewritten final.md
```

如果模型仍然卡住，可以减小分块：

```bash
python3 scripts/prepare_rewrite_job.py input.md rewrite-job --chunk-chars 1000
```

## 文件结构

```text
.
├── SKILL.md
├── agents/
│   └── openai.yaml
├── references/
│   ├── long-form-workflow.md
│   ├── low-capacity-model-workflow.md
│   ├── modes.md
│   ├── quality-rubric.md
│   ├── rewriting-methods.md
│   └── usage-guide.md
└── scripts/
    ├── assemble_rewrite.py
    └── prepare_rewrite_job.py
```

## 维护建议

- 修改核心触发和总体流程：编辑 `SKILL.md`。
- 修改洗稿模式、仿写模式：编辑 `references/modes.md`。
- 修改低模型分块流程：编辑 `references/low-capacity-model-workflow.md` 和 `scripts/`。
- 修改安装后引导：编辑 `references/usage-guide.md`。

## 注意

这个 skill 会帮助提高原创表达和结构化改写质量，但不承诺“法律意义上的绝对安全”或“必然规避所有相似性风险”。涉及商业发布、版权敏感内容或高风险传播场景时，建议人工复核。
