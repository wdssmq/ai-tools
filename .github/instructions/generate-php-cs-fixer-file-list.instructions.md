---
description: "扫描目录生成 PHP-CS-Fixer 文件清单，或直接校验 conflict-files.txt 并拼接格式检查命令"
applyTo: "**"
---

# 生成文件路径清单

当需要列出某个目录中的文件路径时（默认为`../diff`），按以下规则生成该目录根部的 `list.md`。用户直接提供 `conflict-files.txt` 时，也可以跳过扫描和生成 `list.md`，按下文的直接输入流程校验并拼接命令。

## 直接使用 conflict-files.txt

1. 将用户提供的 `conflict-files.txt` 作为输入文件，不扫描目录，也不修改或复制该文件；
2. 逐行校验文件内容：不得有空行、重复项、目录项或额外说明；路径可以是相对于实际项目根目录的路径（使用 `/` 分隔），也可以是该项目根目录内文件的绝对路径；
3. 要求用户指定实际项目根目录；未指定时先询问，不得根据 `conflict-files.txt` 所在目录猜测；
4. 确认项目根目录中存在 `.php-cs-fixer.dist.php` 和 `vendor/bin/php-cs-fixer`，并确认每条路径均对应项目根目录内的文件；任一校验失败时说明具体路径和原因，不输出命令；
5. 校验通过后，输出一条可直接复制到 Git Bash 执行的命令。命令从输入文件读取全部路径为数组并传给 PHP-CS-Fixer，不生成 `list.md`：

```bash
cd "<实际项目根目录>" && mapfile -t files < "<conflict-files.txt 的绝对 Git Bash 路径>" && vendor/bin/php-cs-fixer fix --config=.php-cs-fixer.dist.php "${files[@]}"
```

输入文件路径使用绝对 Git Bash 路径，即将 Windows 盘符路径转换为 `/盘符小写/其余路径` 的形式；路径含空格时必须加双引号。相对路径按项目根目录解析；绝对路径须位于项目根目录内。若用户提供的文件未通过格式或文件存在性校验，不得直接将其内容拼接进命令。

## 扫描目录生成 list.md

1. 将目标目录作为路径根目录，递归扫描其中的所有文件；
2. 使用相对于目标目录的路径，并统一使用 `/` 作为路径分隔符；
3. 每个文件路径单独占一行，按字典序升序排列；
4. 排除 `.gitkeep`、`list.md`；
5. 不要在清单中写入目录、绝对路径、空行、编号或额外说明；
6. 写入后，将 `list.md` 中的路径与实际扫描结果逐项比较，确认路径完整、无重复且不含已排除项；
7. `list.md` 生成或更新后，输出一条可直接复制到 Git Bash 执行的命令：先切换到用户指定的实际项目根目录，再使用 `mapfile` 将 `list.md` 读入数组，并将全部文件路径传给 `vendor/bin/php-cs-fixer fix --config=.php-cs-fixer.dist.php`；未指定项目根目录时，先询问用户，不要根据目标目录猜测；
8. 命令中的 `list.md` 必须使用其绝对 Git Bash 路径，即将 Windows 盘符路径转换为 `/盘符小写/其余路径` 的形式；路径含空格时必须加双引号；

每次生成或更新清单后，使用实际路径替换下列占位符，并输出替换完成、可直接执行的命令：

```bash
cd "<实际项目根目录>" && mapfile -t files < "<list.md 的绝对 Git Bash 路径>" && vendor/bin/php-cs-fixer fix --config=.php-cs-fixer.dist.php "${files[@]}"
```

目标目录可以是独立的文件副本目录，不必与实际项目根目录相同。输出命令前，先确认项目根目录中存在 `.php-cs-fixer.dist.php` 和 `vendor/bin/php-cs-fixer`，且清单中的每条相对路径在该项目根目录下均对应文件；不满足时说明问题，不输出命令。
