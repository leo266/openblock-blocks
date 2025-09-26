# GitHub Actions 未触发构建的解决方案

## 问题诊断

你的提交已经成功推送到 `main` 分支，但 GitHub Actions 没有触发 NPM 包构建。经过检查，发现以下问题：

## 主要原因：缺少必需的 GitHub Secrets

你的 GitHub Actions 工作流需要以下两个密钥才能正常运行：

### 1. `GH_TOKEN` - GitHub 访问令牌
- **用途**: 用于创建 Git tags 和推送到仓库
- **权限**: 需要 `repo` 权限

### 2. `NPM_TOKEN` - NPM 发布令牌  
- **用途**: 用于发布包到 NPM 官方仓库
- **权限**: 需要发布权限

## 解决步骤

### 步骤 1: 创建 GitHub Personal Access Token

1. 访问 GitHub Settings: https://github.com/settings/tokens
2. 点击 "Generate new token" → "Generate new token (classic)"
3. 设置以下权限：
   - `repo` (完整仓库访问权限)
   - `workflow` (工作流权限)
4. 复制生成的令牌

### 步骤 2: 创建 NPM Access Token

1. 登录 NPM: https://www.npmjs.com/
2. 访问 Access Tokens: https://www.npmjs.com/settings/tokens
3. 点击 "Generate New Token"
4. 选择 "Automation" 类型
5. 复制生成的令牌

### 步骤 3: 在 GitHub 仓库中配置 Secrets

1. 访问你的仓库: https://github.com/leo266/openblock-blocks
2. 点击 "Settings" 标签
3. 在左侧菜单中选择 "Secrets and variables" → "Actions"
4. 点击 "New repository secret"
5. 添加以下两个密钥：

   **密钥 1:**
   - Name: `GH_TOKEN`
   - Secret: [你的 GitHub Personal Access Token]

   **密钥 2:**
   - Name: `NPM_TOKEN`  
   - Secret: [你的 NPM Access Token]

## 验证设置

配置完成后，你可以通过以下方式验证：

### 方法 1: 推送新的提交
```bash
# 创建一个小的更改
echo "# Test" >> README.md
git add README.md
git commit -m "test: trigger GitHub Actions"
git push origin main
```

### 方法 2: 手动触发工作流
如果你想添加手动触发选项，可以修改工作流文件：

```yaml
on:
  push:
    branches: [ main ]
    paths-ignore:
      - 'README.md'
      - '.github/*'
  workflow_dispatch:  # 添加这行以支持手动触发
```

## 检查工作流状态

1. 访问你的仓库的 Actions 页面: https://github.com/leo266/openblock-blocks/actions
2. 查看最新的工作流运行状态
3. 如果失败，点击查看详细日志

## 常见问题

### Q: 为什么我的提交没有触发工作流？
A: 可能的原因：
- 缺少必需的 GitHub Secrets（主要原因）
- 仓库的 Actions 权限被禁用
- 工作流文件语法错误

### Q: 如何检查 Actions 是否被启用？
A: 访问仓库 Settings → Actions → General，确保 "Allow all actions and reusable workflows" 被选中

### Q: NPM 包发布失败怎么办？
A: 检查：
- NPM_TOKEN 是否有效
- 包名是否已被占用
- package.json 中的版本号是否正确

## 下一步

配置完 Secrets 后，你的自动化发布流程将包括：

1. ✅ 自动测试
2. ✅ 生成预发布版本号
3. ✅ 创建 Git tag
4. ✅ 发布到 NPM
5. ✅ 部署到 GitHub Pages

每次推送到 `main` 分支都会自动触发这个完整的发布流程。