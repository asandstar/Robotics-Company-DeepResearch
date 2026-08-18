# Sunday Robotics Deep Research

Sunday Robotics 深度研究交互报告，以及可直接部署到 GitHub Pages 的配置。

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

工作流会把 `Sunday.html` 作为发布站点的根页面 `index.html`，因此线上访问不会发生二次跳转。报告使用内嵌样式、脚本、字体和图片，不需要额外安装依赖或执行前端构建。
