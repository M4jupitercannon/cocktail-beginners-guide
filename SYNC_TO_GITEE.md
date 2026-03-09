# Gitee 镜像同步指南

本文档说明如何将本仓库同步到 Gitee（码云），以便中国大陆用户流畅访问。

---

## 方法一：Gitee 导入 GitHub 仓库（推荐，最简单）

### 步骤

1. 登录 [Gitee](https://gitee.com)（如无账号则注册一个）
2. 点击右上角 **"+"** → **"从 GitHub/GitLab 导入仓库"**
3. 授权 Gitee 访问你的 GitHub 账号
4. 在仓库列表中找到 `cocktail-beginners-guide`，点击 **"导入"**
5. 等待导入完成（通常 1-2 分钟）

### 后续同步

Gitee 导入的仓库**不会自动同步**。每次你更新 GitHub 后，需要手动同步：

1. 进入 Gitee 仓库页面
2. 点击仓库名右侧的 **"同步"** 按钮（刷新图标）
3. 确认同步即可

---

## 方法二：手动添加 Gitee 为远程仓库

如果你更喜欢命令行操作：

### 1. 在 Gitee 上创建空仓库

1. 登录 Gitee → 新建仓库
2. 仓库名：`cocktail-beginners-guide`
3. **不要**勾选"使用 README 初始化仓库"
4. 创建

### 2. 添加 Gitee 远程地址

```bash
cd /home/ziwei/cocktail-beginners-guide

# 添加 Gitee 作为第二个远程仓库
git remote add gitee https://gitee.com/M4jupitercannon/cocktail-beginners-guide.git

# 推送到 Gitee
git push gitee main
```

### 3. 日常同步

以后每次更新内容后，同时推送到两个平台：

```bash
# 推送到 GitHub
git push origin main

# 推送到 Gitee
git push gitee main

# 或者一条命令同时推送
git remote set-url --add --push origin https://gitee.com/M4jupitercannon/cocktail-beginners-guide.git
git push origin main  # 这会同时推送到 GitHub 和 Gitee
```

---

## 方法三：GitHub Actions 自动同步（进阶）

可以配置 GitHub Actions，每次推送到 GitHub 后自动同步到 Gitee。

### 1. 在 Gitee 上生成私人令牌

1. Gitee → 设置 → 私人令牌 → 生成新令牌
2. 勾选 `projects` 权限
3. 复制令牌

### 2. 在 GitHub 仓库添加 Secret

1. GitHub 仓库 → Settings → Secrets and variables → Actions
2. 添加以下 secrets：
   - `GITEE_TOKEN`：上一步的 Gitee 私人令牌
   - `GITEE_PASSWORD`：你的 Gitee 密码

### 3. 创建 GitHub Actions 工作流

创建文件 `.github/workflows/sync-to-gitee.yml`：

```yaml
name: Sync to Gitee

on:
  push:
    branches: [main]

jobs:
  sync:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: Mirror to Gitee
        uses: wearerequired/git-mirror-action@master
        env:
          SSH_PRIVATE_KEY: ${{ secrets.GITEE_PRIVATE_KEY }}
        with:
          source-repo: git@github.com:M4jupitercannon/cocktail-beginners-guide.git
          destination-repo: git@gitee.com:M4jupitercannon/cocktail-beginners-guide.git
```

> 注意：此方法需要配置 SSH 密钥对，将公钥添加到 Gitee，私钥作为 GitHub Secret。

---

## 推荐方案

| 方案 | 难度 | 自动化 | 推荐场景 |
|------|------|--------|---------|
| **方法一：Gitee 导入** | 最简单 | 手动点击同步 | 更新不频繁时推荐 |
| **方法二：双远程推送** | 简单 | 手动 push | 命令行用户推荐 |
| **方法三：GitHub Actions** | 较复杂 | 全自动 | 频繁更新时推荐 |
