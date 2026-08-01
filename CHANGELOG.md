# 更新日志

本项目基于 [ai4rpg/tavern-cards](https://github.com/ai4rpg/tavern-cards) 的 fork。版本号从 v1.0 开始记录本 fork 的自定义改动，跟随上游更新时在对应版本内注明。

## v1.1 — 模版增强（当前版本）

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
