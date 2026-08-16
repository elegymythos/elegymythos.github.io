---
title: "DSH exam-paper-plugin - 基于 DeepSeek Harness 的自动试卷生成插件"
date: 2026-08-08T12:00:00+08:00
draft: false
tags: ["DeepSeek", "Harness", "Plugin", "Python", "Docx"]
categories: ["Projects"]
---

DeepSeek Harness 插件：自动识别上传的 Word(.doc / .docx) / PDF / 纯文本题目文件，抽取题目与配图，按你的选择生成一张带书写空隙的可打印试卷（`.docx` 可编辑 + `.html` 网页预览）。

项目地址：[elegymythos/exam-paper-plugin](https://github.com/elegymythos/exam-paper-plugin)

## 核心功能

- **自动解析题目**：识别选择题 / 填空题 / 解答题 / 判断题，提取题干、选项和配图（按坐标关联到对应题目）
- **按需抽题**：先预览题目清单（带全局序号），再按序号选择要出的题
- **生成可打印试卷**：标题、卷头（姓名/班级/得分）、选择题可双列、每道题自动预留书写空隙（横线或空白、按题型定行数）
- **双格式输出**：`.docx`（用 Word/WPS 打开打印）+ `.html`（浏览器直接打印成 PDF）

## 依赖

| 组件 | 用途 | 必需性 |
| --- | --- | --- |
| `python3` | 引擎运行 | 必需 |
| `poppler-utils`（`pdftohtml`、`pdfimages`） | 解析 PDF 文本与图片坐标 | 仅解析 .pdf 时需要 |
| `LibreOffice` 或 `antiword`/`catdoc` | 解析旧版 .doc（前者保留配图，后者仅文字） | 仅解析 .doc 时需要 |
| Python `Pillow` | 图片尺寸检测 | 可选 |

> 依赖均可选、不强制：缺失只影响对应文件类型。`.docx` 解析只用 Python 标准库 `zipfile`，不需要 `unzip`。

## 一键安装

### Linux / macOS

```bash
git clone https://github.com/elegymythos/exam-paper-plugin.git exam-paper-plugin
cd exam-paper-plugin
./install.sh
```

`install.sh` 会：安装插件到 `$DSH_HOME/plugins/exam-paper/`、建软链接指向 Harness 内置依赖作用域（`@deepseek-ai/dsh-tools` 不在公开 npm 上）、注册「试卷生成」代理预设，并可选地检查缺失依赖（先询问，绝不强制）。

| 参数 | 作用 |
| --- | --- |
| `--with-doc` | 询问是否安装 .doc 转换后端 |
| `--install-deps` | 非交互自动安装缺失依赖 |
| `--skip-deps` | 跳过依赖检查 |
| `--uninstall` | 卸载 |

### Windows

双击 `install.bat`，或 PowerShell 执行 `install.ps1`（`-WithDoc` / `-InstallDeps` / `-Uninstall`）。脚本用目录联接（junction）替代软链接，无需管理员权限。

### 作为 bundle 安装

本包同时声明了 Harness 的 bundle 清单，可被 `dsh --profile` 启动器的插件系统识别：

```bash
dsh plugin --profile <name> add "github:elegymythos/exam-paper-plugin"
```

## 使用

上传题目文件后，直接对 Agent 说：

```
解析 /path/to/数学试卷.docx 里的题目
```

会得到题目清单（含全局序号、题型、是否有配图）。然后：

```
要第 1、3、5 题，标题「数学单元测验（一）」，解答题留 8 行横线
```

生成的 `.docx` / `.html` 会写到会话工作区根目录。

### 两个工具

| 工具 | 作用 |
| --- | --- |
| `parse_question_file(files)` | 解析题目文件，返回题目清单 |
| `generate_exam_paper(files, select, title, …)` | 抽题并生成试卷 |

`generate_exam_paper` 关键参数：`files`（源文件，必填）、`select`（题目全局序号数组）、`title`（试卷标题）、`headerFields`（卷头，默认 姓名/班级/得分）、`blankLinesByType`（各题型书写行数）、`ruledLines`（横线/空白）、`optionColumns`（选择题列数 1 或 2）。

## 工作原理

- **`src/plugin.mjs`**：ESM Cordis 插件，`inject: ["tools","shell","fs"]`，用 `@deepseek-ai/dsh-tools` 的 `defineTool` 注册两个工具，通过 `shell` 服务调用 Python 引擎，会话工作区取自 `sandboxPolicy.resolve({session}).workspaceRoot`
- **`src/exam_paper.py`**：无第三方依赖。`.doc` 经 LibreOffice 转 `.docx`（缺失时用 antiword/catdoc 仅取文字）；`.docx` 用 `zipfile` + `ElementTree` 解出正文与 `word/media/*` 配图；`.pdf` 用 `pdftohtml -xml` 取文本/图片坐标并按位置把配图关联到题目；输出用 Python 标准库直接构造 OOXML
- 代理预设与 bundle 补丁两条安装路径都只消费宿主能力（tools / shell / fs），不发布任何服务，因此无需 `isolate` realm

## 项目结构

```text
exam-paper-plugin/
├── install.sh            # 一键安装/卸载（Linux/macOS）
├── install.ps1           # 一键安装/卸载（Windows）
├── install.bat           # Windows 双击入口
├── preset.yml            # 预设显示元数据
├── agent.cordis.yml      # 代理预设组合（模板）
├── cordis.patch.yml      # bundle 补丁
├── src/
│   ├── plugin.mjs        # Cordis 主机插件（注册两个工具）
│   └── exam_paper.py     # 文档解析 + 试卷生成引擎（Python）
├── README.md
└── LICENSE
```

## 已知限制

- `.doc` 解析需要额外后端：LibreOffice（保留配图）或 antiword/catdoc（仅文字）
- PDF 解析依赖 poppler-utils
- `@deepseek-ai/dsh-*` 是 Harness 内置包、不在公开 npm 上，bundle 方式依赖 profile 的 `node_modules` 内由 pnpm 维护的符号链接来解析
