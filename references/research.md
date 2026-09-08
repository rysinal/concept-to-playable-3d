# 调研记录与设计依据

检索日期：2026-09-08。输入是一张讲解 Codex、Astra、生图和 Blender MCP 的中文社交平台截图，没有实际游戏画面。因此能分析的是工作流主张，不能从该图判断渲染质量或性能。

## 截图拆解

截图提出：连接 Blender MCP、提供游戏概念、生成目标画风图片、反复使游戏截图接近参考、要求 60fps、推理设为 high，并转述 45 分钟完成。

可提炼的方法是把抽象美术要求转成可见目标，并用反馈循环指导实现。缺失的环节包括游戏引擎、资产导入、相机一致性、玩法闭环、真实性能测量和预算内收敛。截图中的时间、配额和强度设置是案例陈述，不能推导成技能保证。

## X 原帖与其他实践

下面的 X 页面均通过已登录浏览器直接读取了相关正文；部分页面显示 X 自动中文翻译。没有运行作者的游戏或复核其基准数据。

| 来源 | 已读内容与适用边界 | 纳入本技能的做法 |
| --- | --- | --- |
| [Anshu：截图对应的方法原帖，9 月 5 日](https://x.com/anshuc/status/2096008086901113339) | Blender MCP + 生图目标 + 实机截图迭代；作者说还用生图制作网格纹理 | 保存视觉目标、生成纹理与模型结合 |
| [Anshu：同日说明](https://x.com/anshuc/status/2096009016312725876) | 作者明确当时只是图形 demo，之后才探索玩法 | 图形 demo 与可玩游戏分别验收 |
| [Anshu：8 小时后续，9 月 6 日](https://x.com/anshuc/status/2096584624432374151) | 作者报告继续做天气、模型和细节；仍需优化与测试 | 不承诺一键完成；保留动态观察和收敛条件 |
| [Meng To：制作说明，9 月 5 日](https://x.com/MengTo/status/2096213835460084184) | Three.js 程序化环境，生图做透明 UI PNG，另在 Blender 重建场景制作预告片；小于 2MB 不含图像音乐 | 混合资产方式；区分离线宣传渲染与实时画面；统计口径明确 |
| [Bilawal Sidhu：多视角评审，9 月 5 日](https://x.com/bilawalsidhu/status/2096092205140062666) | 用扫描多视角图像评价 Blender 重建结果；两轮各约 45 分钟。这是重建案例，不是游戏帧率测试 | 多视角检查、与上一轮问题对照；不只看单张美图 |
| [Ben Nash：性能修改，9 月 6 日](https://x.com/bennash/status/2096364012707483901) | 作者列出分离物理/渲染、场景批处理、水动画搬到 GPU、降低部分画质成本和 HUD 更新；主观报告更流畅 | 按瓶颈优化并披露画质变化；不照抄 120Hz 物理频率或宣称已验证帧率 |
| [Anshu：发布 skill 的后续说明，9 月 8 日北京时间检索](https://x.com/anshuc/status/2097002966225912100) | 原作者已经把方法整理成 Dream Loop | 阅读上游实际技能，避免只根据转载推测 |

最初通过公开搜索和 [AI Primer 汇总](https://www.ai-primer.com/creative/stories/astra-game-builds) 获得其他作者原帖链接，再回 X 核验。原始 X 方法链接也由 [Reddit 转述](https://www.reddit.com/r/ProAI/comments/1w7nzld/dude_gpt6_astra_is_some_kind_of_turboagi_machine/) 提供。搜索抓取 X 返回过 403，但浏览器能直接读正文，因此未将镜像当成最终唯一证据。

## Dream Loop 上游核验

- 作者仓库：[achimala/dream-loop](https://github.com/achimala/dream-loop)。README 自述作者为 @anshuc。
- 本次读取的固定提交：`d113b78bd8143d6c4e2b46840c1a881139bcc084`。
- [该版本 SKILL.md](https://github.com/achimala/dream-loop/blob/d113b78bd8143d6c4e2b46840c1a881139bcc084/SKILL.md)。通过 GitHub API 解析 HEAD，再读取固定提交原文与 LICENSE；未运行仓库代码或安装命令。
- MIT 许可；本包保留 [许可与归属](../THIRD_PARTY_NOTICES.md)。这是综合改写的独立技能，不是原作者维护的版本。

沿用其有价值的思路：引擎内目标画面、同条件截图对照、先修主要形体与光色、延续上轮反馈、停滞时检查实现方法、后续改进从现有画面出发。

做了以下适配：

| 上游该版本的约定 | 本技能的选择与原因 |
| --- | --- |
| 概念画面强烈偏写实，并排斥部分风格化效果 | 尊重用户画风；上游 README 的体素示例也说明不能把写实当普遍目标 |
| 自生成概念后默认暂停确认 | 明确需求内继续；保留用户明确的确认要求及重大方向变更澄清 |
| 视觉评分达到 8 且帧率可接受即可完成 | 视觉、玩法、性能独立检查；游戏不能仅以美术评分完成 |
| 倾向独立 critic，缺失时考虑递归 CLI | 仅在当前授权允许时委派，否则自身评审，不绕过运行限制 |
| 倾向详细自建模型和生成纹理 | 按画面贡献选择 Blender、程序化和已有资产；增加导出与 PBR 数据检查 |
| 未给预算时持续循环并提示 token 消耗 | 以明确范围和验收收敛；出现无改善则调整方法，真实受阻时报告 |

这些适配是本次综合判断，不是声称社区已经证明的统一最佳实践。

## 技术一手资料

- [OpenAI：Building games with Astra，2026-09-04](https://developers.openai.com/blog/how-to-build-games-with-astra)：读取完整案例，支持保存不同玩法状态的视觉目标、可重复场景、真实动作检查和资产导出评估。案例明确 headless SwiftShader 的帧间隔不代表硬件 GPU 帧率。这里借鉴测量边界，不复制其大型星球架构。
- [Blender MCP 社区项目仓库](https://github.com/ahujasid/blender-mcp)：确认场景查询、对象/材质操作、Python 执行能力；修改前保存场景，复杂操作分步。具体工具名和参数在使用时按实际安装版本读取。
- [Three.js WebGLRenderer 文档](https://threejs.org/docs/pages/WebGLRenderer.html)：确认 `info` 多 pass 统计的重置行为，以及逻辑视口和 drawing buffer 的区别。
- [Blender glTF 5.3 文档](https://docs.blender.org/manual/en/5.3/addons/scene_gltf2.html)：本次只读到搜索索引摘要，直接抓取失败。索引可见法线空间、材质通道和烘焙说明；不据此保证任何安装版本的全部导出行为。

## 验证范围

本次交付是技能文件。结构校验和规则审阅不等于执行过生图、Blender 建模、游戏开发或 GPU 性能验收。案例时长、配额和效果均为作者报告，未独立复现。后续应以一次实际项目的执行结果修正技能，而不是增加未经验证的普遍规则。
