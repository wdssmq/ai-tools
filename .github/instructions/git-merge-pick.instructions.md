---
description: "读取本地仓库路径配置，辅助 Git 分支合并和 cherry-pick 操作"
---

# Git 分支合并和 cherry-pick

用于在固定的本地目标仓库中执行分支合并或 cherry-pick。目标仓库为纳入工作区且显示式写入 `ai-tools/.git-local/git-repository.path` 文件的路径。

## 目标仓库

1. 读取 `ai-tools/.git-local/git-repository.path`，获取可能为多个路径的文件夹路径及标注信息。
2. 关于 `.path` 文件的说明：
    - 每一行记录一个本地仓库路径及可选的标注信息，格式为 `<path> [<label>]`。
    - 路径必须为绝对路径，标注信息用于快速识别和对应工作树路径。
    - 标注可能不是实际的分支名，要后续通过实际的 Git 仓库信息进行确认。
    - 跳过空行和 # 开头行。
    - 允许多个标签，每一对方括号内为一个独立的标注信息。
3. 获取到多个路径时，视为同一个仓库不同的工作树路径，按要求在相应路径下执行操作。
4. 用户可使用标注简称来对应相应的工作树路径。
5. 先检查并汇报 `git status --short --branch`、用户选定目标或来源 worktree 内的当前分支、最近提交和相关分支的提交记录。
6. 目标工作区有未提交修改、正在进行 rebase/merge/cherry-pick，或当前分支无法明确时，不执行合并或 pick，先请求处理。
7. 由用户明确目标标签或分支来选择工作树路径，不得通过切换分支代替选择 worktree。
8. 对于写入和修改操作，必须在用户确认后进行，并在执行前再次检查变更范围和潜在冲突，明确展示以下信息：
    - 目标 worktree；
    - 目标分支；
    - 来源分支或完整提交 SHA；
    - 将要变更的提交列表；
    - 预览 diff；
    - 最终命令。

## 合并分支

1. 明确目标分支和来源分支；默认目标分支为用户选定目标 worktree 内的当前分支，来源分支必须由用户指定或从当前任务明确得出。
2. 合并前使用 `git log --oneline --decorate --graph` 和 `git diff <target>...<source>` 核对提交范围与文件变化。
3. 默认使用执行 `git merge --no-ff <source>` 以保留合并节点。
4. 如果发生冲突，停止自动操作，列出冲突文件和冲突原因；只在用户确认解决方案后修改文件。解决后检查冲突标记，运行受影响代码的最窄测试或检查，再执行 `git add` 和 `git merge --continue`。


## cherry-pick

1. 各自分析目标分支和来源分支的记录（严格为最新 20 条），以「提交信息」文本相似度识别最新一条等价提交，以 patch-id 或实际变更作补充验证。
3. 以最新等价提交之后的来源分支提交为候选，并排除目标分支已包含或已存在等价变更的提交；多个提交按祖先到后代的顺序处理。若指定提交已在目标分支中以不同 SHA 存在，不执行 cherry-pick，并说明已存在的目标提交。
4. 如果候选提交是 merge commit，必须单独展示其父提交并请求用户明确指定主线父提交；不得自动加入普通 cherry-pick 列表。
5. 输出待 pick 提交的短 SHA 和主题信息，供用户确认。
6. 拼接 `git cherry-pick <sha1> <sha2> <sha3>`，供用户确认后直接执行，此处使用完整提交 SHA。
7. 发生冲突时停止并报告状态。解决后检查冲突标记，运行受影响代码的最窄测试或检查，再执行 `git add` 和 `git cherry-pick --continue`；确认不应应用该提交时，先说明原因，再使用 `git cherry-pick --skip`。


## 完成检查

在每次合并或 cherry-pick 操作前，执行：

```bash
before=$(git rev-parse HEAD)

```

操作成功后：

```bash
git status --short --branch
git log -1 --oneline --decorate
echo "Previous HEAD: $before"
git diff $before HEAD

```

汇报目标仓库、目标分支、实际合并或 pick 的提交、是否产生冲突、验证命令及结果。除非用户明确要求，不执行 `git push`、`git reset`、`git checkout`、`git clean` 或删除分支。
