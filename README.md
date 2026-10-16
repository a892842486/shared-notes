# Bamu's Notes

A personal knowledge base built with Markdown and MkDocs Material.

個人知識筆記網站，使用 Markdown 撰寫，透過 MkDocs Material 建置，並部署至 GitHub Pages。

## Website

**[Visit Bamu's Notes](https://a892842486.github.io/shared-notes/)**

## Topics

- Linux：發行版、桌面環境與個人化配置
- Programming：程式開發與技術筆記
- Personal Notes：生活、學習與其他整理中的知識

## Built With

- [Markdown](https://www.markdownguide.org/)
- [MkDocs](https://www.mkdocs.org/)
- [MkDocs Material](https://squidfunk.github.io/mkdocs-material/)
- [GitHub Pages](https://pages.github.com/)

## Local Development

```bash
py -m pip install mkdocs-material
py -m mkdocs serve
```

## Deployment

```bash
py -m mkdocs gh-deploy
```

原始 Markdown 與設定檔保存在 `main` 分支，網站建置後發布至 `gh-pages` 分支。
