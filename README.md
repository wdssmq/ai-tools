# AI Tools Library

可复用的 GitHub Copilot / VS Code AI 工作流指令库。

## 使用方式

将 `.github/instructions/` 下的指令文件复制到目标仓库同一路径，或在 VS Code 中将本仓库作为包含该目录的工作区打开。指令会由 VS Code 根据其 front matter 自动发现和应用。

## 内容

- `generate-php-cs-fixer-file-list.instructions.md`：递归生成 PHP-CS-Fixer 文件清单，并提供可直接执行的 Git Bash 命令；
    - 使用 BCompare 比较项目文件，将有差异的文件复制至 `diff/` 目录，使用指令对原项目中的相应文件执行 PHP-CS-Fixer 检查；
