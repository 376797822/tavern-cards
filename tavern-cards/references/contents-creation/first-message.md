# 开场白创作

仅角色卡需要此步骤。开场白设定初始场景、体现角色性格、引导用户互动。

## 工作流

1. 读取 `创作规划.yaml` 的 `first_messages` 数组
2. 逐项处理：
   - **叙事式**：调用 `first-message-agent`（传入创作规划路径 + 索引；启用 MVU 时传入 initvar 文档路径——有 `initvar_override` 则传 override 路径，否则传默认 `世界书/变量/initvar.yaml`；未启用 MVU 则不传）
   - **大纲式/说明式**：主代理直接整理
3. 检查与保存：
   - 确认文件已写入 `output_path`
   - 调用 `check-agent` 检查正文
4. 所有项完成后，按顺序注册到 state 的 `first_messages`

## 开场白格式

### 叙事式

传统的角色扮演开场白，AI 直接叙述场景，以角色身份说话。适合大多数情况。

编写流程：
- 遍历 `first_messages` 数组，逐项调用 `first-message-agent`
- 向子代理传入规划路径 + 索引（启用 MVU 时传入 initvar 路径，有 override 则传 override、否则传默认 initvar.yaml）
- 确认子代理已写入当前项的 `output_path`
- 调用 `check-agent` 检查

### 大纲式

只整理关键信息，不写完整描写。适合用户有清晰构想、希望精确控制内容时。

编写要点：
- 只根据用户提供的信息整理，不自己编造
- 结构清晰，关键要素一目了然

格式参考：

```
场景: [时间/地点/情境一句话]
背景设定:
  - [背景信息1]
  - [背景信息2]
当前状态:
  [角色A]状态:
    - [状态描述]
  [角色B]状态:
    - [状态描述]
关键细节:
  - [具体细节1]
  - [具体细节2]
开场点:
  1. [互动切入点1]
  2. [互动切入点2]
```

### 说明式

玩法规则、前置插件、作者信息、界面提示等元信息开场白。不扮演角色、不展示状态栏，用于在游戏开始前告知玩家必要信息。

使用条件：
- 仅当角色卡玩法复杂、玩家需要前置说明，或用户明确要求时，才作为第一个开场白使用
- 其后必须有一个真实的叙事开场白——说明式不能是唯一开场白，也禁止放到第一个开场白以外的位置

编写要点：
- 结构清晰，按标题分层组织信息
- 结尾明确引导玩家翻页开始游戏（如「翻到第二页以开始游戏」）
- 不展示变量，无需 `<StatusPlaceHolderImpl/>` 占位符；保存为 `0.md`

示例：

```markdown
# 角色卡名称

作者: XXX

测试模型: XXX
允许二创: 是/否
下载链接: [XXX](https://example.com)

### 前置插件
1. 酒馆助手
  [Github](https://github.com/N0VI028/JS-Slash-Runner)
2. 提示词模板
  [Github](https://github.com/zonde306/ST-Prompt-Template)

### 玩法说明
……

### 界面小贴士
在电脑端游玩时，如果发现前端的选项框太宽，可以在酒馆的用户设置里适当增大`页面宽度`、减小`字体比例`。

**翻到第二页以开始游戏**
```

示例中的插件按需取舍：用到 MVU（变量脚本/状态栏）时需酒馆助手，用到 EJS 时需提示词模板。

## 开场白后缀约定

MVU 项目的开场白后缀决定是否自动追加状态栏占位符：

- `.txt`：打包时自动追加 `<StatusPlaceHolderImpl/>` 占位符（状态栏锚点，见 `references/ui/text.md`）
- `.md`：打包时不追加

需要状态栏的叙事式/大纲式开场白存 `.txt`；不需要的说明式开场白存 `.md`。

解包外部卡时按内容决定后缀：含占位符的开场白存 `.txt`、不含的存 `.md`。

## MVU 变量确认

- 默认开场白（`开场白/0.txt`）对应默认 `世界书/变量/initvar.yaml`
- 额外开场白需不同初始变量时，使用 `initvar_override`：创建 `开场白/initvar/{index}.yaml`、注册到 state、pack 自动嵌入 `<UpdateVariable><initvar>` 块——完整流程见 `references/mvu/initvar.md#initvar_override`

## 注册

按 `output_path` 保存（通常 `开场白/{index}.txt`），然后注册到 state：

```bash
node scripts/tavern-cards-forge.mjs patch {project} '[{"op": "add", "path": "/first_messages/-", "value": "开场白/0.txt"}]'
# 每项一行，顺序与数组一致
```

## 自查清单

叙事式：
- [ ] 每一项已调用 first-message-agent
- [ ] 子代理已交付自查摘要，正文已写入 output_path
- [ ] MVU 项目：叙述状态与对应 initvar 一致

大纲式：
- [ ] 只根据用户提供的信息整理，未编造内容
- [ ] 结构清晰，要素完整

通用：
- [ ] 已调用 check-agent 检查
- [ ] 注册到 `first_messages`
- [ ] state 中 `first_messages` 顺序与创作规划一致
- [ ] 说明式开场白：仅玩法复杂或用户要求时使用，其后必有真实的叙事或大纲开场白，已保存为 `.md` 后缀
- [ ] 有 `initvar_override` 的开场白：已按 `references/mvu/initvar.md#initvar_override` 处理
