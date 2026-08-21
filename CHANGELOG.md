# 更新日志

本项目基于 [ai4rpg/tavern-cards](https://github.com/ai4rpg/tavern-cards) 的 fork。版本号从 v1.0 开始记录本 fork 的自定义改动，跟随上游更新时在对应版本内注明。

## v1.4 — 上游同步：拆分 tavern-design / agents 迁移到根目录（当前版本）

> 合并上游 `ccc55f6..b73dd8f`，共 2 个结构化重构提交。本 fork 的五种角色整合模版未受影响，融合到上游新版结构，自动合并零冲突。

### 上游变更

- **拆分 tavern-design skill** [`2ab1653`]：将原 tavern-cards 中的「叙事设计阶段」（大方向讨论、剧情设计、材料转化、产出 `design-spec.md`）独立为新的 `tavern-design/` skill；新增 `references/design-guide.md`（七大设计维度）、迁移 `conversion/` 材料转化文档与 `conversion-agent`；确立跨 skill 交接物 `design-spec.md`（tavern-design 写入 → tavern-ui 追加 UI 段 → tavern-cards 消费）
- **子代理集中到仓库根目录** [`b73dd8f`]：把各 skill 目录下的共享子代理迁移到仓库根 `agents/`（check-agent、first-message-agent、conversion-agent），并新增 **schema-agent**（负责 schema.ts 编写/校验）；README 安装指南扩展子代理 invocation 契约，`mvu/schema.md` 简化、`rules-check.md` 补充
- **tavern-cards 精简**：`SKILL.md`、`error-handling.md`、`composition.md`、`project-setup.md` 等去掉已迁出的设计逻辑，聚焦项目创建与创作规划

### 涉及文件

`tavern-cards/`：`SKILL.md`、`composition.md`、`error-handling.md`、`mvu/guide.md`、`mvu/schema.md`、`project-setup.md`、`requirements.md`、`requirements/entries-dynamics-style.md`、`requirements/world-characters.md`、`resume.md`、`revision.md`、`rules-check.md`

`tavern-design/`（新增 skill）：`SKILL.md`、`references/design-guide.md`、`references/conversion*.md`、`scripts/validate-conversion-outline.mjs`

`agents/`（迁移+新增）：`check-agent.md`、`first-message-agent.md`、`conversion-agent.md`、`schema-agent.md`（新增）

`tavern-ui/`：`SKILL.md`、`references/design-thinking.md`、`README.md`

### 融合说明

本 fork 的五种角色整合模版（主角/NPC/功能/家庭/组织）及其在 `composition.md`、`SKILL.md` 中的接入（含"写角色的必问判断"、模版映射表、参考资料索引）完整保留并适配到上游新版结构，合并零冲突。

---

## v1.3 — 上游同步：UI 模板 / 表单式开局 / 打包增强

> 合并上游 `a053e54..ccc55f6`，共 5 个提交，涉及 `tavern-cards` 与 `tavern-ui` 两个 skill。本 fork 的自定义模版未受影响，自动合并零冲突。

### 上游变更（tavern-cards）

- **表单式开局** [`6dcc87c`]：`first-message.md` 新增**交互表单式**开场白（表单式/叙事+表单组合两种形态，自定义占位符挂载 + 替换/隐藏正则）；同步扩展 `entries-dynamics-style.md`
- **打包命令增强** [`c38a226`]：forge CLI 新增 **export / split 命令**、无效头像时 pack JSON 回退、重复 schema key 检测；同步更新 `manual.md`、`error-handling.md`、`modify-existing.md`、`revision.md`、`mvu/schema.md`
- **质量扫描时机重构** [`ccc55f6`]：词质量扫描改为**每个 typeLists 位置完成后运行**（位置级扫描），全局扫描改为可选；同步更新 `composition.md`、`SKILL.md`、`project-setup.md`

### 上游变更（tavern-ui）

- **vue-tsc 静态检查门禁** [`f5f57c5`]：前端开发流程加入 vue-tsc 类型检查关卡，数据丢失类症状路由到该检查
- **Live Server CORS 指引** [`e300ea8`]：新增跨域 iframe 预览的 CORS 配置指南，补充 vue-tsc 与 schema 字段故障排查
- **交互表单与 MVU 变量文档** [`6dcc87c`]：新增 `references/interactive-opening-form.md`（开局表单前端实现，含 `defineMvuDataStore` 读写、`createChatMessages` + `triggerSlash` 提交触发 AI）与 `references/mvu-variables.md`（MVU 变量访问指南）

### 涉及文件

`tavern-cards/`：`SKILL.md`、`composition.md`、`first-message.md`、`error-handling.md`、`manual.md`、`modify-existing.md`、`mvu/schema.md`、`project-setup.md`、`entries-dynamics-style.md`、`revision.md`、`tavern-cards-forge.mjs`

`tavern-ui/`：`SKILL.md`、`tavern-helper-runtime.md`、`tavern-helper-template.md`、`interactive-opening-form.md`（新增）、`mvu-variables.md`（新增）

### 未改动

本 fork 的五种角色整合模版（主角/NPC/功能/家庭/组织）保持不变，合并无冲突。

---

## v1.2 — 上游同步：MVU 修复 / 开场白增强 / 修改流程重构

> 合并上游 `49ebe70..a053e54`，共 3 个提交。本 fork 的自定义模版（v1.0/v1.1）未受影响，自动合并零冲突。

### 上游变更

- **MVU 修复** [`aaf1058`]：`mvu-patch.json` 和 regex 脚本文档中移除无效的 `null` 值 `minDepth`/`maxDepth`，避免生成的 JSON Patch 带空字段
- **开场白增强** [`8bec605`]：纯文本格式的 MVU 问候语拆包时自动追加状态栏占位符；打包时分离问候语后缀；新增说明性问候语支持
- **修改流程重构** [`a053e54`]：将 revision（修改已有卡片/世界书）的流程逻辑从多个文档中抽取为独立文件 `references/revision.md`；统一"先收齐所有编辑请求、再统一修改条目"的工作流

### 涉及文件

`SKILL.md`、`mvu-patch.json`、`composition.md`、`first-message.md`、`conventions.md`、`error-handling.md`、`manual.md`、`modify-existing.md`、`initvar.md`、`revision.md`（新增）、`regex-scripts.md`、`text.md`、`tavern-cards-forge.mjs`

### 未改动

本 fork 的五种角色整合模版（主角/NPC/功能/家庭/组织）和 CHANGELOG.md 保持不变，合并无冲突。

---

## v1.1 — 模版增强

> 基于上游 `49ebe70`。本次新增/修改均在 `tavern-cards/` 目录内，未改动脚本。

### 新增

- **家庭级模版** `references/contents-creation/character/family-character.md`：以家庭/家族为单位，含概述、现状、目标、经济情况、住址和住宅布局、成员、成员住址、成员间关系、其他信息
- **组织级模版** `references/contents-creation/character/organization-character.md`：以组织/势力为单位，含概述、现状、目标、势力范围、经济情况、组织结构、成员、据点、内部关系、对外关系、其他信息

### 修改

- **目标数量要求**：5 个模版（主角/NPC/功能/家庭/组织）的长期、短期目标均要求 **1-3 条、方向各不相同**，字段说明与自查清单同步更新
- **NPC 级 NSFW 器官** `npc-character.md`：拆分为独立字段，覆盖**口、胸、臀、外阴、内阴、肛门谷道**
- **主角级 NSFW 器官** `main-character.md`：保留男性器官，`规格` 拆分为**尺寸、形态、睾丸、特征**，要求用类比或详细数值描述
- **社交关系信息项**：主角级（家人/亲属、朋友/同侪、对手/敌人、与 {{user}}）、NPC 级与功能级（与主角、与其他角色、所属圈层）均新增按方向划分的 `社交关系` 字段
- **多角色同条目** `npc-character.md`：一个条目可写多个同类型角色，先整体简述，再分别按模版填写每个角色（如一群下人仆妇、封建大家庭中的某个小家庭、一个同好会里的成员）

### 接入

- `references/composition.md`：必问判断与整合模版映射表加入家庭/组织级
- `SKILL.md`：场景路由说明与参考资料索引更新
- `references/requirements/entry-types.md`：补充整合模版说明

---

## v1.0 — fork 初始化：新增角色整合模版

> 基于上游 `49ebe70`。首批自定义改动。

### 新增

- **主角级模版** `references/contents-creation/character/main-character.md`：可被 {{user}} 代入的主角色，整合基础信息、性格、NSFW 为一条
- **NPC 级模版** `references/contents-creation/character/npc-character.md`：深度互动 NPC，含"与主角的相遇可能和展开方向"
- **功能级模版** `references/contents-creation/character/function-character.md`：剧情功能型角色（指派任务、牵线搭桥、引入剧情等）

### 修改

- `references/composition.md`：角色部分加入"角色类型 → 写作模板"映射表；整合模版角色不再拆分条目
- `SKILL.md`：场景路由加入"写角色前的必问判断"——写角色前先向用户确认用整合模版还是原本拆分流程，默认原本拆分流程

### 基础设施

- 新增 `.gitattributes`：统一 LF 行尾，避免 Windows CRLF 污染历史
