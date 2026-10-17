---
description: "将本仓库维护的全局 Agent 约束安装或更新到当前用户的 VS Code 配置目录。用于同步、应用、安装或更新全局 agent 约束。"
---

# 应用全局 Agent 约束

将 [全局约束模板](../templates/global-agent-constraints.instructions.md) 作为唯一来源，同步到用户级 VS Code 配置目录中的 `global-agent-constraints.instructions.md`。

## 确定目标

1. 先读取源模板，验证它包含 YAML front matter、非空 `description`，以及 `applyTo: "**"`。
2. 默认使用 `~\AppData\Roaming\Code\User\prompts` 作为 VS Code 用户级提示文件目录。将 `~` 解析为当前用户的主目录，并展示解析后的绝对路径。
3. 对于 CodeBuddy，使用 `~\.codebuddy\rules\` 目录，用户明确提及 `CodeBuddy` 时，同时为两者配置约束，否则只针对 VSCode；
3. 在读取或写入目标文件前，要求用户最终确认该目录；用户提供其他目录时，使用用户提供的绝对路径。未获确认不得继续。
4. 目标文件必须为 `<已确认目录>\global-agent-constraints.instructions.md`。不得修改该目录中的任何其他文件。

## 安装或更新

1. 若目标文件不存在，展示源文件路径、目标文件路径和将执行的复制操作，并请求用户确认后创建目标目录（如需要）和目标文件。
2. 若目标文件存在，比较它与源模板：
   - 内容相同：不写入，并报告已是最新版本；
   - 内容不同：展示统一 diff、源文件和目标文件路径，说明目标文件将被完整替换。仅在用户确认后覆盖。
3. 不要合并模板与目标文件内容，也不要修改源模板；全局约束只在本仓库模板中维护。

## 完成检查

完成写入后重新读取目标文件，确认其内容与源模板完全一致，并报告：

- 源模板和目标文件的绝对路径；
- 操作类型（新安装、更新或无需操作）；
- 验证结果。

提醒用户重启聊天或新建聊天，使 VS Code 重新加载用户级指令。
