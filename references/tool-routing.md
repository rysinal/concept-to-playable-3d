# 工具选择与调用

选择依据和调研边界见 [工具调研](tool-research.md)。以下名称是核对过的实现示例，实际执行仍以本轮工具 schema、安装版本和用户工程为准。不要猜测统一的 `blender.*` 命名空间。

安装与替代路线的用户决策遵循 [执行阶段与用户确认](production-checkpoints.md)：先自动探测，复用已有授权；等质量通道切换可告知后继续，新增安装和实质质量/费用变化先给出具体选择。

## 1. 按工作类型选择

| 工作 | 首选路径 | 无该能力时 |
| --- | --- | --- |
| 人物母版、五官/头发/服装细节图 | 内置 imagegen，传入真实参考图 | 使用已有参考；缺图时说明待补，不假装生成 |
| 查看和修整正在打开的人物场景 | 已连通的 Blender MCP，查询 → 小段 Python → 截图 | 本地 Blender 脚本处理已保存的副本 |
| 批量导入、烘焙、导出、固定相机渲染 | Blender Python + CLI | 已连接 MCP 执行相同脚本；长任务拆分并查询状态 |
| 从图片生成基础 3D 网格 | 用户已有底模，或获准使用的专门 3D 生成工具 | 在 Blender 制作可验证的小样；不以基础几何冒充精细人物 |
| 表情、骨架、权重 | 在真实网格上用 Blender 工具/Python；需要时使用已验证的自动绑定服务 | 保留模型，明确绑定未完成 |
| 插件启用、必须使用界面的设置 | 当前环境可用的 UI 工具 | 提供准确的界面步骤；不用坐标盲点代替建模 API |

MCP 和 Python 可以同时使用：MCP 常把 Python 送入当前 Blender 会话，CLI 把脚本送入另一个进程。差别是会话与反馈方式，不是两套建模算法。已存在可用通道时不为了“必须装 MCP”重建环境。

## 2. MCP 选择与安装建议

**建议为长期交互建模配一个 Blender MCP，但只选一套作为当前场景的控制通道。** 安装建议不自动执行安装、升级或替换用户已有配置；用户已授权搭建环境时按范围完成并验证，不重复确认。

- 已有 MCP 且场景查询、代码执行和图像反馈正常：继续使用。
- 新安装：`ahujasid/blender-mcp` 是社区采用较广、且本次已核对 Codex 安装方法和工具签名的可复现选项；按下节示例配置。这个选择不表示人物质量优于其他实现。
- Blender 5.1+ 也可选择 [Blender 官方 Lab MCP](https://www.blender.org/lab/mcp-server/)。它是独立项目，必须从官方页安装对应插件和 server，按 [官方 Setup](https://projects.blender.org/lab/blender_mcp/wiki/Setup) 获取当前启动方式。本次未完成该实现的 Codex 连通测试，不臆造工具签名或把社区版 `uvx blender-mcp` 当成官方版。
- 只有批量资产任务，或不方便维持 Blender 界面会话：直接走 CLI。仅生成参考图：不需要安装 Blender MCP。
- Tripo MCP 是另一种服务连接；不因用了 Tripo 就再装一套 MCP。已有可用 API skill/CLI 时复用，先核对其多图、生成、绑定版本支持。

| 差异 | 官方 Lab MCP | 社区 ahujasid 版 |
| --- | --- | --- |
| 定位 | Blender Python API 的自然语言接口，场景分析与复杂设置理解 | 场景控制与图像反馈，并集成素材和外部生成服务 |
| 安装 | Blender 5.1+，官方 addon 与独立 server | 对应版本 addon 与 uvx server，已有 Codex 配置示例 |
| 当前选择依据 | 保留独立路线；不能因“官方”推断所有调用已验证 | 已有核心 MCP 调用的安装实测记录；新任务仍需核对能力和实际授权，不代表人物质量更高 |

两个项目都能让代码作用于 Blender，官方名称不构成生成质量或安全隔离保证。优先选择实际连通且具备所需反馈的实现，而不是工具数量最多的实现。

### 社区版安装示例（核对日期：2026-09-08）

来源是 `ahujasid/blender-mcp`，已核对发布包 `1.9.1`。先检查 Blender、`uvx`、已有同名配置；版本固定用于复现，不表示永远使用该版本。更新时同时核对 server 与 addon。

```bash
command -v uvx
codex mcp add --help
uvx blender-mcp@1.9.1 install-addon
codex mcp add blender --env DISABLE_TELEMETRY=true --env BLENDER_HOST=127.0.0.1 --env BLENDER_PORT=9876 -- uvx blender-mcp@1.9.1
```

没有 uv 时按 [uv 官方安装说明](https://docs.astral.sh/uv/getting-started/installation/) 安装。桌面客户端找不到 `uvx` 时，注册命令使用 `command -v uvx` 得到的绝对路径。遇到已有 `blender` 配置先检查其来源，不能直接覆盖成另一项目。

在 Blender Preferences → Add-ons 启用 **MCP for Blender**，在 3D View 的 N 面板启动 MCP Server。Codex 启动 stdio server，不再另开第二个相同 server。插件显示名称按安装版本核对。

上游默认开启遥测；此示例关闭遥测，避免参考图、截图和代码进入该额外采集流程。保持本地连接；将其他资产服务的凭据与开关按实际需要配置。安装 Blender 控制通道本身不要求 Tripo/Rodin 等 API key。

同时关闭插件 Preferences 中的 `Allow Telemetry` 并保存偏好，查询实际值确认。指定 IPv4 地址可避免 `localhost` 解析到另一个 IPv6 服务；若该地址端口已占用，先识别进程并选用空闲端口，客户端与插件保持一致，不终止无关服务。

可通过 Blender 自身 Python 启用已安装模块：刷新脚本路径后 `addon_utils.enable("blender_mcp", default_set=True, persistent=True)`，设置插件 `telemetry_consent=False`，执行 `bpy.ops.wm.save_userpref()`，最后调用 `bpy.ops.blendermcp.start_server()`。必须在有 GUI 事件循环的 Blender 中运行；此版本明确不支持用 `blender --background` 启动 MCP 监听。当前会话不便操作时，可打开独立 GUI 窗口运行启用脚本，保留用户原工程。

### 连接验收

1. 重新发现本轮真实 MCP 工具，确认插件已启动，工具可见。
2. 查询场景和文件名，识别是否连到了用户目标 `.blend`。不要改动或自动保存未保存的其他工程。
3. 运行只读 Python 查询 Blender 版本，再取一张可辨认的视口图。检查图像内容；成功返回空白图片不算图像反馈正常。
4. 记录实际 server/addon 版本、选定实现和验收结果。配置存在、进程启动、工具可见、场景可读、图像可读是不同状态。

若 Codex 本轮工具列表尚未加载新增配置，可用标准 MCP 客户端启动同一配置的 stdio server，执行 initialize、tools/list 和上述只读 tools/call 做独立协议检查；完成后关闭该测试客户端。不把独立客户端通过称为当前 Codex 任务已热加载工具。后续先重新发现工具；仍不可见时刷新 MCP 连接或重新加载客户端配置，不重复安装插件。

本次安装实测：2026-09-08，社区包 1.9.1 与 Blender 5.2.1 LTS，协议检查发现 28 个工具，场景查询、只读 Python 和可辨认截图均成功；server 报告 addon protocol 5，addon 自身版本标记为 1.6，不与 Python 包版本强行比较相等。此记录仅证明当次控制通道可用，不证明人物制作质量；新任务仍应查询当前连接状态。

## 3. MCP 中的具体调用顺序

下表来自社区版已核对源码；`ctx` 由服务器注入，不是客户端参数。官方版或其他实现必须重新读取 schema，并将相同操作映射到真实工具。

`user_prompt` 按当前 schema 传入用户授权本次任务的原话，不改写成助手计划或子目标。下表该字段是替换说明，执行时填真实原文；具体操作目的写在调用说明或脚本中。

| 操作 | 社区版工具与客户端参数示例 |
| --- | --- |
| 读取场景 | `get_scene_info(user_prompt="当前用户授权任务的原文")` |
| 检查目标对象 | `get_object_info(object_name="实际对象名", user_prompt="当前用户授权任务的原文")` |
| 只读版本/路径检查 | `execute_blender_code(code="import bpy; print(bpy.app.version_string, bpy.data.filepath)", user_prompt="当前用户授权任务的原文")` |
| 修改对象/导入/材质/保存/导出 | `execute_blender_code(code="经过核对的短段 bpy 脚本", user_prompt="当前用户授权任务的原文")` |
| 获取反馈图 | `get_viewport_screenshot(max_size=1000, user_prompt="当前用户授权任务的原文")` |

开始修改前保存命名副本。每次只解决一个可检查的问题，例如鼻翼体积或耳饰连接，明确对象、前置条件和输出。优先通过 `bpy.data` 访问真实命名对象；必须用 `bpy.ops` 时确认活动对象、模式和上下文。不得把整个人物的生成、绑定、烘焙和导出塞进一次无法恢复的调用。

每次变更后先检查返回结果和实际对象，再检查截图。脸部质量用固定相机近景渲染，不只依赖当前远距离视口。脚本必须显式保存 `.blend` 与导出资产，不能把工具返回“成功”当作文件已落盘。

超时或断连：先读对象/文件/任务状态再决定重试。CLI 不包含 UI 中未保存的改动，不能静默切换并覆盖原文件；能取得最新副本后再切换。仅视口截图失败而代码仍可用时，可渲染到文件再查看。不要在活动 MCP 会话执行 factory reset，以免卸载连接插件。

## 4. 不用 MCP 的 Blender Python / CLI 路线

先定位 Blender 可执行文件并运行 `--version`。macOS 常见路径为 `/Applications/Blender.app/Contents/MacOS/Blender`，仍需检查是否存在。`bpy` 脚本由 Blender 自带 Python 执行，不假定普通 `python3` 可以导入 `bpy`。

将任务脚本保存为项目内真实文件，例如 `scripts/character_pass.py`。脚本读取副本、定位对象、完成本轮操作、将结果另存到指定路径；渲染和导出按真实任务需要执行。下面为命令模板，调用前替换路径：

```bash
"/absolute/path/to/blender" --background "/absolute/path/to/character-working.blend" \
  --python-exit-code 1 --python "/absolute/path/to/character_pass.py"
```

输入 `.blend` 必须在脚本前加载，`--python-exit-code` 必须在脚本前设置。第一次验证 CLI 可在独立进程中使用 factory startup，只读检查，不接触用户当前 UI 会话：

```bash
"/absolute/path/to/blender" --background --factory-startup --python-exit-code 1 \
  --python-expr 'import bpy; print(bpy.app.version_string, len(bpy.data.objects))'
```

后台模式没有普通 3D View 上下文；用相机渲染生成图片，不调用依赖前台窗口的视口截图操作。检查退出码、日志和实际产物；用图像查看工具打开渲染结果，必要时重新打开 `.blend` 检查。CLI 渲染不是游戏实机验收。

## 5. imagegen 与 3D 生成工具如何衔接

**参考图生成**：读取当前环境的 `imagegen` 技能和工具说明。当前内置 `image_gen.imagegen` 接受 `prompt`；本地图编辑用 `referenced_image_paths`，没有完整本地路径时才按规则使用 `num_last_images_to_include`，二者不同时传。首次新建无参考图时省略二者。本地参考先查看，原图/母版/部位图都实际传入，并注明角色。工具名和签名将来改变时以实际 schema 为准。

按人物模块逐张生成与检查，将返回图片保存到工程，记录真实路径再供后续使用。MCP 里能执行 Python，不意味着 Blender 能凭空调用内置 imagegen；本地路径也不是外部 3D 服务可访问的 URL。

**3D 基础资产**：本环境有 `threejs-3d-generator` 时，先加载其 SKILL.md，再按其中要求读 API 说明并运行自带脚本。以下 `GENERATOR_SCRIPT` 指向发现的真实 `scripts/threejs_3d_asset.py`，不是固定用户机器路径：

```bash
python3 "$GENERATOR_SCRIPT" --help
python3 "$GENERATOR_SCRIPT" probe
python3 "$GENERATOR_SCRIPT" image --help
python3 "$GENERATOR_SCRIPT" status TASK_ID
python3 "$GENERATOR_SCRIPT" download TASK_ID --out-dir assets/models/character
```

先确认服务使用和上传符合用户范围，再按当前参数提交生成请求；记录 task ID 后查询直至完成，成功后下载再导入 Blender。状态未知先查原任务，不重复付费提交。只检查凭据是否存在，不输出 key。版本、价格、绑定限制以当前官方合同和实际反馈核对，不能将旧 skill 的某版本经验扩大为永久规律。

多视图生成要确认接口接受的视角顺序与数量，并传入各自独立且一致的图片；不要把排版三视图海报直接当单视图输入，也不把某版本接口假定为覆盖全部新模型。拆分头/发/身体适合分件生成后在 Blender 装配；自动绑定若要求完整身体网格，应另行准备合适输入，不能让自动绑定服务处理孤立头发。

没有这项 skill 时，使用已连接生成工具的真实 schema 或官方 API 文档；不要为了兼容而杜撰脚本或工具。已有模型足够时直接修模型，不额外启动生成服务。
