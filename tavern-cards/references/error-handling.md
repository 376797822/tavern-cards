# 错误处理

统一管理转化、技术阶段及运行时的错误处理流程。

---

## 转化阶段

### 材料矛盾处理

**识别矛盾**：
- 同一角色在不同章节的描述冲突
- 世界观设定在不同章节不一致
- 时间线、事件顺序矛盾

**处理流程**：
1. **记录矛盾**：标注矛盾位置、内容、严重程度
2. **询问用户**：提供原文引用，说明矛盾内容，提供建议解决方案
3. **按用户确认修改**：记录用户选择，修改相关条目
4. **验证修改**：检查修改是否解决矛盾，是否引入新矛盾

### 信息缺失处理

**边界说明**：此流程处理「内容完整性」问题，不处理「命名」问题（命名问题归「关键信息确认」，见 `references/conversion/key-info.md`）。

**识别缺失**：
- 信息存在但不够详细，无法构成完整条目
- 某类信息完全缺失（如角色无家庭背景描述）
- 信息过于模糊，需要具体化

**处理流程**：
1. **记录缺失**：标注缺失位置、内容、现有信息
2. **询问用户**：提供上下文，说明重要性，提供建议
3. **处理方式**：
   - 用户补充：用户提供完整信息
   - 用户确认推导：基于现有信息推导，用户确认
   - 标记未知：在条目中标记为未知，后续揭示
   - 跳过：信息非关键，可跳过

### 转化失败恢复

**失败场景**：
- 源材料质量太差，无法提取有效信息
- 材料类型判断错误，导致流程混乱
- 用户需求变更，需要重新转化

**恢复流程**：
1. **评估失败原因**：记录失败原因，评估已完成工作的价值，确定恢复起点
2. **恢复策略**：
   - 从头开始：材料问题严重，需要重新转化
   - 部分重做：部分章节有问题，重做相关部分
   - 继续完成：问题不大，可继续完成
3. **恢复步骤**：制定恢复计划，记录恢复进度

---

## 技术阶段

### patch 命令失败

schema 校验不通过、路径冲突、JSON 格式错误等：

1. 解读错误信息，定位具体问题（字段缺失、类型不匹配、路径已存在等）
2. 修正后重新执行 patch
3. 路径冲突时：检查是否为重复注册，确认后用 `replace` 操作覆盖或删除旧条目后重新 `add`

> **null 值迁移**：`recursion.delay_until`、`effect.sticky/cooldown/delay`、`regex_scripts.minDepth/maxDepth` 已收紧为 `.optional()`（禁止 `null`，仅允许缺省）。若旧 state.json 报 `expected number, received null`，把对应字段的 `null` 删除即可（留空即可省略）。

### MVU 校验失败

> MVU 校验失败 forge 会详细列出违法语句。
> 关键提示原文含「do NOT run `npm install`」「Cannot find module 'zod'」——遇到此类提示直接删除对应 import，**不要**给项目 `npm install zod`/`lodash`。

`state.zod` 缺失时 validate-mvu 会报错，需确保 `mvu-patch.json` 已正确应用（包含 `/zod` 的 add 操作）。Zod 脚本内容校验现在通过 `state.zod` 驱动，schema.ts 路径由 `state.zod.schemaPath` 定位。

## SillyTavern 运行时

以下错误发生在 SillyTavern 浏览器中，卡片已部署后运行时报错。

### EJS 条件抛 `xxx is not defined`

现象：打开聊天或生成时，酒馆 EJS 扩展自检报 `ReferenceError: xxx is not defined`；生成阶段可能不报，只在打开/preparation 阶段报。

根因：EJS 扩展用 `with(locals){...}` 包裹模板。`@@if` 条件里裸引用 `define()` 注册过的短名（如 `current_location?.includes(...)`），生成阶段 `define()` 恰好先执行所以不报；但 open/preparation 阶段条目执行顺序不保证（features.md 称 “Unordered processing”），短名可能尚未注册 → `with` 找不到标识符 → ReferenceError。`?.` 只防 TypeError，防不住未声明标识符的 ReferenceError。

修复：条件里不裸引用 `define()` 注册的短名，改用 `getvar('stat_data.xxx',{defaults})`——`getvar` 是 EJS 内置函数，任何阶段都在作用域，无顺序依赖。需要短名复用时，在该条目内用 `@@private` + `const x = getvar(...)`。

> 改造完成后，旧的全局定义条目（如 `EJS预处理`）可删除：其唯一作用是用 `define()` 为其他条目提供短名，现有条目已改为条目内 `getvar`/`const` 自取，该条目不再被引用。

### EJS 抛 `Identifier ... has already been declared`

现象：段落控制里用 `<%_ const x = getvar(...) _%>` 定义局部短名后，扩展报 `Identifier 'x' has already been declared`。

根因：条目内容文件里的 `const` 在 EJS 扩展的模板作用域中无块作用域隔离，当条目被多次处理（open/preparation 阶段或重新加载时重算）时，同一段 `const x` 会被再次声明而冲突。`@@if` 装饰器行是单行求值不涉及此问题，受影响的是条目内多行 EJS 代码。

修复：在条目首行加 `@@private` 装饰器——它会在条目首尾插入 `<% { %>`/`<% } %>` 形成块作用域，每次处理的 `const` 都封闭在该块内，不再跨求值冲突。凡是条目内容中用了 `const`/`let` 定义局部变量的条目，都应加 `@@private`。
