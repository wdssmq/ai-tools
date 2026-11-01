---
description: "将本仓库维护的全局 Agent 约束安装或更新到当前用户的 VS Code 配置目录。用于同步、应用、安装或更新全局 agent 约束。"
---

# 应用全局 Agent 约束

将 [全局约束模板](../templates/global-agent-constraints.instructions.md) 作为唯一来源，同步到用户级 VS Code 配置目录中的 `global-agent-constraints.instructions.md`。

## 确定目标

1. 先读取源模板，验证它包含 YAML front matter、非空 `description`，以及 `applyTo: "**"`。若验证未通过，立即终止执行并向用户报告具体校验失败原因，严禁继续后续步骤。
2. 默认根据当前操作系统确定 VS Code 用户级提示文件目录：Windows 使用 `~\AppData\Roaming\Code\User\prompts`，macOS 使用 `~/Library/Application Support/Code/User/prompts`，Linux 使用 `~/.config/Code/User/prompts`。将 `~` 解析为当前用户的主目录，并展示解析后的绝对路径。
3. 用户明确提及 `CodeBuddy` 时，同时将 `~\.codebuddy\rules\` 作为 CodeBuddy 目标目录；否则只配置 VS Code。对每个目标目录，均须在读取或写入目标文件前要求用户最终确认；用户提供其他目录时，使用用户提供的绝对路径。所有目标目录均获确认前不得继续。
4. 每个目标文件必须为对应已确认目录中的 `global-agent-constraints.instructions.md`。不得修改这些目录中的任何其他文件。

## 安装或更新

1. 对每个目标文件分别执行以下操作：若文件不存在，展示源文件路径、目标文件路径和将执行的复制操作，并请求用户确认后创建目标目录（如需要）和目标文件。
2. 对每个已存在的目标文件，分别比较它与源模板：
   - 内容相同：不写入，并报告该目标文件已是最新版本；
   - 内容不同：展示该文件的统一 diff、源文件和目标文件路径，说明目标文件将被完整替换。仅在用户确认后覆盖。
3. 不要合并模板与目标文件内容，也不要修改源模板；全局约束只在本仓库模板中维护。

## 完成检查

完成写入后重新读取每个目标文件，确认其内容与源模板完全一致，并报告：

- 源模板和各目标文件的绝对路径；
- 各目标文件的操作类型（新安装、更新或无需操作）；
- 验证结果。

提醒用户重启聊天或新建聊天，使 VS Code 和（如已配置）CodeBuddy 重新加载用户级指令。
