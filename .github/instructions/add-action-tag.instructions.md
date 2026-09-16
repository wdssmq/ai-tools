---
description: "在 action 仓库中根据版本记录分析并添加发布 tag"
applyTo: "**"
---

# 添加 action 发布 tag

当需要在 action 仓库中添加发布 tag 时，按以下规则执行：

1. 目标仓库是用户指定的 action 仓库，不是当前存放本指令文件的仓库；如果未提供目标仓库路径，先要求用户提供，不要猜测路径；
2. 先检查目标仓库当前分支、工作区状态、最近提交、已有 tag，以及 README、CHANGELOG、package.json、action.yml 或其他版本记录；
3. 以版本记录和当前提交内容为依据分析实际发布版本号，如用户指定了新的版本号且与记录冲突时要求确认；
4. 确认版本号后，在目标发布提交（默认当前 `HEAD`）创建带注释的完整版本 tag，并创建或更新对应的主版本 tag；例如实际版本为 `1.0.0` 时使用：

```bash
git tag -a v1.0.0 -m "Release v1.0.0" HEAD
git tag -fa v1 -m "Release v1" HEAD

```

5. 如果完整版本 tag 或主版本 tag 已存在，先检查其指向和发布记录；只有确认应移动主版本 tag 时才使用 `-f`，不要覆盖已有完整版本 tag；
6. 完成后执行以下命令核对 tag 指向和当前提交：

```bash
git show-ref --tags
git log -1 --oneline --decorate HEAD

```

7. 汇报实际分析得到的版本号、目标提交、创建或移动的 tag，以及校验命令输出的关键结果；如果工作区有未提交修改或版本号无法可靠判断，不执行 tag 操作。
