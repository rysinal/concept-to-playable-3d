# Blender 工具路线调研（2026-09-08）

本次比较公开项目、作者工作流和官方文档，不是社区市场份额统计，也没有对同一人物做 MCP/CLI 质量对照实验。结论：长期交互修整推荐配置 MCP，批量制作保留 CLI，人物生成器独立选择；没有证据支持“MCP 本身让人物更精细”。

| 一手来源 | 核对结果 | 对技能的影响 |
| --- | --- | --- |
| [Dream Loop 作者仓库](https://github.com/achimala/dream-loop) | 作者把 Blender MCP 与 scripting interface 都列为优于纯 UI 操作的路径；并未要求只能 MCP | 同时保留两种执行方式 |
| [ahujasid/blender-mcp](https://github.com/ahujasid/blender-mcp) | 查询时 GitHub API 返回 27,592 stars；提供场景/对象查询、视口截图、Python 执行及外部素材/生成集成 | 采用较广的社区候选；不以 star 数证明人物质量 |
| [社区版核对源码](https://github.com/ahujasid/blender-mcp/blob/5f8ddaf6e987c4aa0c3467fcc548838b28f64477/src/blender_mcp/server.py) | 核对工具签名；README 与 PyPI 发布包核对到 1.9.1，支持 Codex 注册和 install-addon；默认遥测可关闭 | 给出来源明确的安装与调用示例，固定版本便于复现 |
| [Blender 官方 Lab MCP](https://www.blender.org/lab/mcp-server/) | 官方页面要求 Blender 5.1+，单独安装 addon、client、server；是 Python API 的自然语言接口 | 单独列为候选，不能与社区版包名/工具名混用 |
| [Tripo 官方 MCP 仓库](https://github.com/VAST-AI-Research/tripo-mcp) | README 标为 alpha，以 Tripo Blender Addon 集成为主；查询到最后 push 为 2025-04-14，199 stars | 不因“官方 MCP”推断覆盖当前全部生成模型；可复用现有 API skill |
| [Berrio 人物管线 MCP](https://github.com/Berrio/blender-MCP) | 专门面向游戏角色管线；查询时 0 stars | 是具体实现候选，不足以称为主流或默认安装 |
| [Blender 5.2 CLI 文档](https://docs.blender.org/manual/fr/5.2/advanced/command_line/arguments.html)、[Python Quickstart](https://docs.blender.org/api/main/info_quickstart.html) | 支持后台执行 Python；参数顺序与 UI operator 上下文有实际影响 | 明确文件加载顺序、Python 退出码、后台渲染与视口的区别 |

此前在本任务直接阅读的 X 作者案例继续支持“分工管线”：

- [Nano 分件生成与装配](https://x.com/Dstudio_ai/status/2096475126942560677)：Tripo 分别生成头、头发、身体，Astra 在 Blender 装配/绑定；不证明纯 Python 从零造人。
- [Sayaka 面部制作步骤](https://x.com/sayaka_aiart/status/2096813754159878181)：头发与脸分开，眼球/眼睑结构先准备，表情参考采用正面、斜面和侧面再制作形状键。作者展示不等于独立验证模型。
- [Nano 表情切换](https://x.com/Dstudio_ai/status/2096525100518453342)：多表情模型切换有离散与连续混合限制，不能冒充稳定拓扑的表情形状键。

官方 Lab 页面内容及下载/Setup 链接已读到；项目源站被研究工具限制，未核对其完整当前 server 启动参数和工具签名。因此没有编写伪精确的官方版 Codex 命令。社区版源码固定提交 `5f8ddaf6e987c4aa0c3467fcc548838b28f64477`；Tripo MCP 固定提交 `7e8ec9eeb751e38f7bd1955f9df961c2676c5e38`。

本地边界：发现 Blender 5.2.1 LTS、uv/uvx、Codex CLI；本轮工具清单未暴露 Blender MCP。只读 CLI 探针与技能校验结果以本次交付为准。本次未安装或更换 MCP、未调用付费 3D 服务，也未进行人物质量对照测试。以后执行该技能应重新发现环境，不把此快照当成当前状态。
