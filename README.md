# concept-to-playable-3d

面向 Codex 的 3D 制作 skill：用 imagegen 建立视觉目标，通过 Blender 或现有资产实现，再用真实模型渲染和游戏截图对照修正。支持从一张人物参考图展开整体多视角、五官、头发、服饰和饰品细节，逐步制作精细人物。

这是一套执行与协作流程。它不自带 Blender、图像或 3D 生成模型，也不保证单张图能恢复被遮挡的真实结构。

## 安装

将本仓库完整放入 Codex 的技能目录，保留 `SKILL.md`、`agents/`、`references/` 和第三方许可文件。例如，目标目录尚不存在时：

```bash
git clone https://github.com/rysinal/concept-to-playable-3d.git \
  ~/.codex/skills/concept-to-playable-3d
```

使用自定义 `CODEX_HOME` 时，放到该目录下的 `skills/concept-to-playable-3d`。已有安装先检查本地改动，不覆盖用户修改。安装后让 Codex 重新发现技能；未显示时重新加载客户端。

## 使用

提供真实人物参考图，或清楚的设计描述，然后调用：

```text
使用 $concept-to-playable-3d，根据这张人物图制作角色。
先检查工具并提出拆分方案，再生成统一母版和必要的局部细节。
以真实模型同视角渲染检查偏差，先完成头肩小样，再推进全身。
```

也可只做参考图包、修已有角色，或制作完整的可玩 3D 切片。用途、风格、引擎、预算及已确认设计沿用用户要求。

## 制作与确认流程

**查看参考/检查工具 → 拆分方案 → 统一母版 → 局部细节 → 头肩小样 → 完整制作与交付**。

默认在拆分方案、母版和真实头肩小样三个关键节点等待用户确认，节点间自动检查并修正，每阶段展示产物。支持用户指定逐阶段确认或自动推进。局部修复、仅参考图等任务跳过不适用的阶段；已确认的版本不重复询问。

imagegen 目标图与真实模型渲染分开保存和对照。生图编辑后的截图只能表达修正意图，不能当作模型已经修复的证据。用户认可外观与技术检查通过分别记录。

## 工具分工

| 能力 | 路线 |
| --- | --- |
| 母版与细节参考图 | 可用的 imagegen 工具，实际传入原图和母版 |
| 当前 Blender 场景交互修整 | 优先已有可用 MCP；新安装可选社区 `ahujasid/blender-mcp` |
| 批量导入、渲染、烘焙、导出 | Blender Python / CLI |
| 基础人物网格 | 已有底模、Blender 制作或获准使用的专门 3D 生成服务 |
| 游戏验收 | 真实引擎中的截图、交互、动作与性能测量 |

安装 skill 不会自动安装上述工具或开通外部服务。工具缺失时先检查：等质量的执行通道可告知后切换；新增安装、收费服务及实质质量降级按用户已有授权和具体选择处理。

## 详细说明

- [技能入口](SKILL.md)
- [执行阶段与用户确认](references/production-checkpoints.md)
- [人物细节参考与制作](references/character-detail-workflow.md)
- [人物生图提示词](references/character-detail-prompts.md)
- [多视角建模对照](references/character-visual-comparison.md)
- [工具选择、安装与调用](references/tool-routing.md)
- [社区与官方工具调研](references/tool-research.md)

## 验证范围

已检查技能格式、内部引用和分发文件一致性，并对核心工作流进行了独立审查。安装实测使用社区 MCP 包 1.9.1 与 Blender 5.2.1 LTS，完成 MCP 工具发现、场景读取、只读 Python 和可辨认视口截图；这些是指定环境的记录，不代表所有版本均兼容。

尚未完成从参考图到精细人物、表情绑定和游戏运行的完整质量验收。文档不承诺固定制作时长、自动获得合格拓扑或跨设备帧率。

## 来源与归属

工作流借鉴 [Anshu Chimala 的 Dream Loop](https://github.com/achimala/dream-loop)，结合人物案例调研及本项目的细节拆分、质量对照和协作规则。这是独立维护的技能，与 OpenAI、Blender、Tripo 或上游作者没有官方隶属关系。

Dream Loop 的 MIT 许可及归属保留在 [THIRD_PARTY_NOTICES.md](THIRD_PARTY_NOTICES.md)；[调研记录](references/research.md) 列出来源和证据边界。
