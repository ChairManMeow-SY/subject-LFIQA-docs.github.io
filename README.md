# subject-LFIQA 用户手册

基于 [MkDocs](https://www.mkdocs.org/) + [Material for MkDocs](https://squidfunk.github.io/mkdocs-material/) 构建。

## 本地预览

```bash
pip install mkdocs-material
mkdocs serve
```

浏览器打开 `http://127.0.0.1:8000` 即可预览。

## 构建

```bash
mkdocs build
```

生成的静态文件在 `site/` 目录。

## 部署

Push 到 `main` 分支后，GitHub Actions 自动构建并部署到 GitHub Pages。需要先在仓库 Settings → Pages 中将 Source 设为 **GitHub Actions**。
