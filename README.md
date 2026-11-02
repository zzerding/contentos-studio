# ContentOS · Agent 工作台

[![License: PolyForm Noncommercial 1.0.0](https://img.shields.io/badge/license-PolyForm%20Noncommercial%201.0.0-blue)](LICENSE)
![Commercial use: prohibited](https://img.shields.io/badge/commercial%20use-prohibited-red)

单文件、零运行依赖的内容运营工作台原型。对话是主入口，右侧为资源画布；手动 UI、`/tool` 指令和模型共用同一套 40 个白名单工具。

在线预览：<https://zzerding.github.io/contentos-studio/>

## 本地运行

直接用浏览器打开 `docs/index.html`，或用任意静态服务器托管 `docs/` 目录：

```sh
python3 -m http.server 8000 --directory docs
```

## 能力范围

- 内容创建/修改/删除/导出、草稿生成、SVG 排版封面、人工审核
- 逐渠道本地发布、排期与取消；计划规则与手动模板运行
- 渠道配置、公司资料、成员授权、工作空间设置
- OpenAI 兼容 Chat Completions 多轮工具调用（完整 endpoint、model、可选内存 API key）

## 边界

- 业务数据仅保存在页面内存，刷新即重置。
- 页面只模拟逐公司权限策略，不是生产安全隔离；真实业务必须在后端鉴权。
- 发布与回执始终标记为 DEMO，没有真实渠道、调度器或外部调研。
- 生成封面是本地 SVG 排版，不是 AI 图像生成。
- 不要输入真实密钥、PII 或生产数据。密钥不写入文件、日志、URL、localStorage 或 sessionStorage。

设计决策与验证记录见 [DECISIONS.md](DECISIONS.md)。

## 测试

```sh
node tests/agent-content-studio.test.cjs
```

测试从 HTML 提取脚本到隔离 VM 运行，不在页面里留测试后门。

## 许可

- 许可：[PolyForm Noncommercial License 1.0.0](LICENSE) —— 允许个人学习、研究、实验及非商业组织内部使用；**禁止任何商业用途**。
- SPDX：`PolyForm-Noncommercial-1.0.0`。GitHub 的许可证徽标不收录该许可，仓库页会显示 “Other”，以 `LICENSE` 文件为准。
- 分发时须随附本许可条款（或官方 URL）以及 `LICENSE` 顶部的 `Required Notice:` 行。