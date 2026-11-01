---
name: zbp-app-lint-template
description: "Install the zbp-app-lint application template into a user-specified Z-BlogPHP application repository. Use when adding or syncing the lint and release workflow templates from zbp-app-lint."
---

# 安装 zbp-app-lint 应用模板

将本技能 `assets/zbp-app-template/` 中维护的模板文件安装到用户指定的 Z-BlogPHP 应用仓库目录。

## 执行步骤

1. 要求用户提供目标应用仓库的绝对路径；不得从当前工作区或本技能所在目录推测目标。
2. 确认该路径存在且是预期的 Z-BlogPHP 应用目录。读取其 Git 状态和本技能将写入的目标路径：
   - `.editorconfig`
   - `.gitignore`
   - `.github/workflows/lint.yml`
   - `.github/workflows/release.yml`
3. 安装前告知用户：`lint.yml` 配置了 `contents: write`，并可自动提交和推送格式修复；`release.yml` 会在满足条件时打包发布，并尝试上传到 `filepost.dev`。说明目标路径及将处理的文件，获得用户确认后再写入。
4. 对每个目标文件分别处理：
   - 文件不存在：按相对路径从技能的 `assets/zbp-app-template/` 复制；
   - 文件与来源完全相同：跳过；
   - 文件已存在但内容不同：保留原文件，展示差异并请求用户明确同意覆盖；未获同意不得覆盖。
5. 不要修改模板内容、目标目录中的其他文件，也不要执行提交或推送。
6. 完成后逐项比较来源与目标文件，检查 Git 状态和 diff，并汇报新增、跳过及经确认覆盖的文件。

## 模板来源

本技能内的 `assets/zbp-app-template/` 是随技能保存的模板副本，包含的相对路径与 `zbp-app-lint` 仓库中的 `zbp-app-template/` 一致。
