# 部署到 GitHub Pages 傻瓜教程（完全零基础也能做）

目标：把 `gh-pages/` 里的网页发布成一个**长期免费、不会休眠**的公开链接：
`https://你的用户名.github.io/guyi/`

---

## 方法一：用 GitHub Desktop（最推荐，不用记命令）

### 第 1 步：注册 GitHub 账号
1. 打开 https://github.com
2. 点右上角 **Sign up**（注册）
3. 填用户名、邮箱、密码 → 一路下一步完成注册
4. **记住你的用户名**（就是网址里 github.com/ 后面那段）

### 第 2 步：下载安装 GitHub Desktop
1. 打开 https://desktop.github.com
2. 下载 macOS 版，安装后打开
3. 用刚才注册的账号**登录**

### 第 3 步：把本地文件夹加进来
1. 在 GitHub Desktop 里点 **File → Add Local Repository…**
2. 选择这个文件夹：
   `/Users/mianmian/WorkBuddy/2026-08-08-16-13-19/gh-pages`
3. 点 **Add**

### 第 4 步：发布到 GitHub
1. 点右上角 **Publish repository**（发布仓库）
2. Repository name（仓库名）填：`guyi`
3. **必须勾选 Public**（公开，免费；私有仓库开 Pages 要付费）
4. 点 **Publish**（发布）
5. 等进度条走完，网页就上传成功了

### 第 5 步：开启 GitHub Pages
1. 浏览器打开 `https://github.com/你的用户名/guyi`
2. 点上方 **Settings**（设置）
3. 左侧找到 **Pages**
4. Source（来源）选 **Deploy from a branch**
5. Branch（分支）选 **main**，文件夹选 **/ (root)**
6. 点 **Save**（保存）

### 第 6 步：访问你的网站
等 **1～2 分钟**，打开：
`https://你的用户名.github.io/guyi/`

✅ 完成！这个链接长期有效、免费、不会休眠。

---

## 方法二：用命令行（需要生成 Token）

> GitHub 现在**不支持用账号密码推送**，必须用 Personal Access Token（令牌）。

### 第 1 步：注册 GitHub 账号（同上）

### 第 2 步：生成 Token
1. 登录后打开：https://github.com/settings/tokens
2. 点 **Generate new token (classic)**
3. Note 随便填（如 `pages`）
4. Expiration 选 **No expiration**（永不过期）或自定义
5. 勾选 **repo**（全部子项自动勾上）
6. 最底部点 **Generate token**
7. **立刻复制**那串 `ghp_xxx` 令牌（只显示一次！）

### 第 3 步：在终端运行（把括号内容替换）
```bash
cd /Users/mianmian/WorkBuddy/2026-08-08-16-13-19/gh-pages
git remote add origin https://github.com/你的用户名/guyi.git
git branch -M main
git push -u origin main
```
- 用户名填你的 GitHub 用户名
- 密码处**粘贴刚才复制的 Token**（不是账号密码）

### 第 4 步：开启 Pages（同方法一第 5 步）

---

## 以后怎么更新网站？
每次我帮你改完功能、重新构建后，gh-pages 里的文件会更新。你需要：
- **Desktop 版**：在 GitHub Desktop 里写个 Summary（如 `更新`），点 **Commit to main** → 再点 **Push origin**
- **命令行**：`cd .../gh-pages && git add . && git commit -m "更新" && git push`

更新后 GitHub Pages 通常 1 分钟内自动生效。

---

## 第 7 步：配置保活定时任务（防 Supabase 免费版休眠）

GitHub Actions 会**每天自动访问一次**你的 Supabase，避免「连续 7 天无请求被暂停」。

1. 打开 `https://github.com/你的用户名/guyi` → 点 **Settings**（设置）
2. 左侧 **Secrets and variables** → **Actions**
3. 点 **New repository secret**，依次添加两个：
   - 名称：`SUPABASE_URL`，值：你的 Project URL（形如 `https://xxxx.supabase.co`）
   - 名称：`SUPABASE_ANON_KEY`，值：你的 anon public key（`eyJ...` 长串）
   > 这两个值都在 Supabase 后台 **Project Settings → API** 复制。
4. 回到仓库，点上方 **Actions** 标签，能看到「保活 Supabase（防免费版休眠）」工作流
5. 点进去 → 右侧 **Run workflow** → 手动跑一次测试，看到绿色 ✅ 即成功
6. 之后**每天自动跑，无需管它**

> 说明：保活脚本每次运行会向仓库写一条时间戳（`ping-log.txt`），这是**故意的**——
> 防止 GitHub 在 60 天无活动时自动禁用这个定时任务本身。不影响你的网站。

---

## 常见问题
- **打不开网页？** 等 2 分钟再试；确认仓库是 Public、Pages 已选 main 分支。
- **推送要密码？** 用 Token 不是登录密码。
- **数据会丢吗？** 不会。网页数据存在你浏览器本地 + Supabase 云端，和 GitHub 无关。
- **保活任务没跑？** 去 **Actions** 页看是否有绿色对勾；若显示 disabled，点 **Enable workflow** 重新启用（配好 Secrets 后手动 Run 一次即可恢复）。
- **Supabase 还被暂停了？** 保活运行后首次访问仍可能慢几秒（自动唤醒），属正常；只要每天有 ping 就不会被长期暂停。
