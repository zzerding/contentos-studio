# ContentOS Agent-first UI

## Decision

采用单文件、无运行依赖的 `agent-content-studio.html`：对话作为主入口，右侧为资源工作画布；40 个白名单工具由手动 UI、`/tool` 指令和模型共用。未采用“原仪表盘 + 附加聊天侧栏”，因为它无法让意图和工具执行成为主要工作方式。

原始版本已独立提交为 `15a3c3a`（`feat(studio): add content management prototype`）。本次重构和回归测试尚未另行提交。

## Assumptions

- 延续独立 HTML 原型交付，使用现代浏览器，不新增框架、后端、SDK 或构建步骤。
- 公司、内容、计划、渠道、成员和对话保存在页面内存，刷新重置；与用户确认的“全站本地闭环”一致。
- 风险最高的边界：本页只模拟逐公司权限策略，不是生产安全隔离。真实业务必须在后端鉴权并保存凭证，不能依靠浏览器状态。
- 本地插件配置只包含无凭证元数据，发布和回执始终标记为 DEMO，具体排期没有自动执行计时器。

## Goal check

- [Shipped] 对话 + 工作画布、公司独立会话、精确工具指令、工具箱和执行轨迹；内容创建/修改/删除/导出、草稿生成、SVG 排版封面、人工审核、逐渠道本地发布/排期与取消、计划规则与手动模板运行、渠道配置、公司资料、成员授权、工作空间设置。
- [Shipped] 内容/计划/渠道/权限等敏感修改需用户真实点击确认；确认前不修改，资源变化使旧建议失效，跨公司 ID 被拒绝，旧内容审核不沿用到新版本，未知回执与已成功/已排期渠道不能重复发布。
- [Shipped] OpenAI 兼容 Chat Completions 多轮工具调用。使用完整 endpoint、model 和可选内存 API key；支持无密钥代理。每家公司发送资料前需明确授权；失败不回退为演示。密钥不写文件、日志、URL、localStorage 或 sessionStorage。
- [Omitted: 已确认的本地原型边界] 真实发布渠道、调度器、租户后端、邀请、支付、外部调研、图像生成模型和独立多 Agent 进程。生成封面是本地 SVG 排版，不宣称 AI 图像生成。
- [Unverified] 未调用实际模型服务；已验证模拟接口的请求/响应及多轮工具闭环，但真实提供商、模型工具能力、CORS 和认证仍需使用所选接口验收。

## 使用

直接在浏览器打开 `agent-content-studio.html`，默认是明确标记的本地演示。右上角可配置模型；文件模式需要完整 HTTPS endpoint 和 CORS，通过 HTTP 提供页面时可使用 `/api/chat/completions` 同源代理。默认 `credentials: omit`，不会发送浏览器 Cookie；生产认证应在代理端明确设计。

工具箱可填入 `/tool` 示例；自由意图理解和完整正文写作需要真实模型。手动入口和演示指令无需模型密钥。不要在消息、公司资料或渠道字段中输入真实密钥、PII 或生产数据。

## 验证

- 静态：内联 JavaScript 语法、77 个静态 HTML ID 唯一、`git diff --check` 通过。无需构建。
- 回归：`node tests/agent-content-studio.test.cjs`，33 项定向检查通过。测试直接从 HTML 提取脚本到隔离 VM，仅测试环境替换渲染与下载；不在页面导出测试后门。
- 浏览器：Chrome headless 下验证手动表单、HTML/SVG 转义、真实点击确认、只读公司、三轮模拟模型生成、接口变更不沿用密钥、逐公司发送许可、HTTP 401 和取消请求。
- 布局：320 / 390 / 768 / 1024 / 1280 / 1440 px 下八个资源视图无横向溢出或重复 ID；桌面与手机截图已检查。不是正式无障碍审计。
- 协议依据：[OpenAI Chat Completions 官方 types](https://github.com/openai/openai-node/blob/master/src/resources/chat/completions/completions.ts) 和 [FunctionDefinition 官方 types](https://github.com/openai/openai-node/blob/master/src/resources/shared.ts) 的 `function`、`tool_calls`、`tool_call_id`、`parallel_tool_calls` 与 `strict` 字段；查阅时为 2026-10-30 的仓库源码，无运行 SDK 依赖。文档网页访问失败，使用官方源码交叉核对。

## Next

A. 使用选定的真实模型或代理做一次实际接口验收，再用于真实 AI 写作；这是唯一未验证的模型接入环节。

B. 检查当前重构 diff 后决定是否另行 commit；目前只提交了原始 HTML。

C. 若只需本地交互原型，可在此停止；不必添加真实业务基础设施。
