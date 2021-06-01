---
author: "kanta"
title: "サイトの構成"
date: 2021-06-01T13:44:23+09:00
description: "サイトの構築方法とデプロイ方法の備忘録"
tags: ["CSG", "HUGO", "markdown", "GitHub Pages"]
series: ["備忘録"]
aliases: ["migrate-from-jekyl"]
ShowToc: true
TocOpen: true
---

# 1. 目的

作品を作った際に、再現性を得られるように工程などをメモしておくブログを作りたかった。

# 2. 使用サービス

## HUGO

[公式ページ](https://gohugo.io/)

> Hugo is one of the most popular open-source 
> static site generators. With its amazing speed and flexibility,
> Hugo makes building websites fun again.

- 静的サイトジェネレータ(SSG)の1つ
- 他のSSGよりもビルドが高速
- Golangで記述

### install

GitHubから入手する

```bash
git clone https://github.com/gohugoio/hugo.git
cd hugo
go install --tags extended
hugo version
```

```bash
# result
hugo v0.83.1-5AFE0A57+extended windows/amd64 BuildDate=2021-05-02T14:38:05Z VendorInfo=gohugoio
```

### how to use

新しいプロジェクトを開始する

```bash
hugo new <project_name>
cd <project_name>
```
好きなテンプレートを[ここ](https://themes.gohugo.io/)から選ぶ

それぞれのテンプレートをプロジェクトに入れる。
(GitHubに入れ方は載ってる)

### PaperModの場合

[PaperMod](https://github.com/adityatelange/hugo-PaperMod)

```bash
# 通常は設定ファイルがtoml形式だが、ymlで生成する
hugo new portfolio -f yml
cd portfolio

# GitHubからPaperModをthemesにクローンする
git clone https://github.com/adityatelange/hugo-PaperMod themes/PaperMod --depth=1

# 設定ファイルにテーマを記述
echo 'theme: "PaperMod"' >> config.toml
```

あとは、config.tomlを自分の思うようにいじるだけ

[自分の設定](https://github.com/KantaTamura/portfolio/blob/main/config.yml)

### なぜこれを使おうと思ったか

基本的にwebの技術なので、java scriptで記述されたものが多かったが、
Goで記述されているものを見つけたから。(Goそんなにかけないけど...)

[ここ](https://jamstack.org/generators/)を見て、ランキングで選んだ

本当は[Rust製](https://www.getzola.org/)のものを使おうとしたが、
日本語の情報が少なかったので妥協

ブログをmarkdown記述できる点

## GitHub Pages

本来はGitHub上のリポジトリのウェブサイトをGitHub上で構築できるもの。

ポートフォリオリポジトリを作成して構築

- サイトは1GB以下
- 月当たり 100GB のソフトな帯域幅制限
- 時間当たり 10 ビルドのソフトな制限

今回は、[GitHub Pages Action](https://github.com/marketplace/actions/github-pages-action)を用いて自動化

mainブランチにpushすれば、自動でビルドされたものがgh-pagesブランチにpushされ、それをGitHub Pagesでウェブサイトにしている

```yaml
name: github pages

on:
  push:
    branches:
      - main  # Set a branch name to trigger deployment
  pull_request:

jobs:
  deploy:
    runs-on: ubuntu-18.04
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true  # Fetch Hugo themes (true OR recursive)
          fetch-depth: 0    # Fetch all history for .GitInfo and .Lastmod

      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v2
        with:
          hugo-version: '0.83.1'

      - name: Build
        run: hugo --minify

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          deploy_key: ${{ secrets.ACTIONS_DEPLOY_KEY }}
          publish_dir: ./public
```

詳しくは、公式ページのドキュメント参照

# 3. 所感

非常にかんたんに体裁の整ったウェブページが作れて感動した

markdown to htmlのパーサには興味があるので今度実装してみたい

