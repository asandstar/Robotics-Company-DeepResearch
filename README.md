# Sunday Robotics Deep Research

A static, interactive deep-research report on major robotics companies and industry dynamics, with one-click deployable GitHub Pages setup.

Project description (for GitHub/About section):

- Topic tags: robotics, robotics-company, deep-research, github-pages, interactive-report
- Maintainer note: 中文版本内容为主，页面内嵌样式与资源，适合长期归档与共享。

## 本地预览

直接打开 `index.html`，或在仓库根目录启动任意静态文件服务器，例如：

```bash
python3 -m http.server 8000
```

然后访问 <http://localhost:8000>。

## 部署到 GitHub Pages

1. 将代码推送到 GitHub 仓库的 `main` 分支。
2. 在仓库的 **Settings → Pages → Build and deployment → Source** 中选择 **GitHub Actions**。
3. 打开 **Actions** 页面，等待 `Deploy GitHub Pages` 工作流完成。

部署后的默认地址为：

```text
https://<github-username>.github.io/<repository-name>/
```

工作流会把 `index.html` 作为网站主页发布，`Sunday.html` 作为完整报告页发布。主页为面向读者的专题入口，点击即可进入交互式报告，不会出现跳转空白体验。报告使用内嵌样式、脚本、字体和图片，不需要额外安装依赖或执行前端构建。
