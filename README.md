# Lab Notes

このリポジトリは [Hugo](https://gohugo.io/) を用いて静的Webサイトを構築し、GitHub Pages で公開するためのプロジェクトです。

## フォルダ構成

```
.
├── archetypes/         # 新規コンテンツ作成時のテンプレート
├── assets/             # SCSSや画像などのアセット
├── content/            # サイトのコンテンツ（Markdown）
├── layouts/            # テンプレートやパーシャル
├── public/             # ビルド後の公開用ファイル（自動生成）
├── resources/          # ビルド時のキャッシュ等（自動生成）
├── static/             # 静的ファイル（favicon等）
├── themes/             # Hugoテーマ
├── hugo.yaml           # Hugo設定ファイル
└── ...
```

## クイックスタート（久しぶりの人向け）

```sh
# 1. 記事を作る（ファイル名: YYMMDD-連番.md）
hugo new posts/$(date +%Y%m)/$(date +%y%m%d)-01.md

# 2. 作成されたファイルを編集する
#    - title, tags を設定
#    - 本文を書く
#    - 公開するなら draft = false にする

# 3. ローカルで確認（draft記事も表示される）
hugo server -D

# 4. 公開（push するだけ。GitHub Actions が自動でビルド＆デプロイ）
git add . && git commit -m "記事を追加" && git push
```

### 記事テンプレート

```toml
+++
date = '2025-01-26T08:34:30+09:00'
title = '記事のタイトル'
tags = ['Hugo', 'AWS']
draft = true
+++

本文をここに書く。
```

### ファイル配置ルール

```
content/posts/
  └── YYYYMM/          # 年月ディレクトリ
      ├── YYMMDD-01.md  # 記事ファイル（日付-連番）
      └── YYMMDD-02.md
```

## 必要なツール
- [Hugo](https://gohugo.io/getting-started/installing/) (`brew install hugo`)
- [Git](https://git-scm.com/)

## GitHub Pages での公開（CI/CD設定）

このリポジトリでは、GitHub Actions を利用して Hugo サイトを自動ビルドし、`gh-pages` ブランチを通じて GitHub Pages で公開します。

### 自動デプロイの仕組み
- `main` ブランチに push されると、GitHub Actions がトリガーされます。
- サイトをビルドし、生成された `public/` ディレクトリの内容を `gh-pages` ブランチにデプロイします。
- デプロイには [peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages) を利用します。

### ワークフロー設定例
`.github/workflows/gh-pages.yml` を作成し、以下のように記述します:

```yaml
name: Deploy Hugo site to GitHub Pages
on:
  push:
    branches:
      - main
jobs:
  deploy:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          submodules: true
      - name: Setup Hugo
        uses: peaceiris/actions-hugo@v3
        with:
          hugo-version: 'latest'
      - name: Build
        run: hugo --minify
      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GITHUB_TOKEN }}
          publish_dir: ./public
```

#### ポイント
- `main` ブランチへの push で自動的に公開されるため、手動で `public/` をコミット・プッシュする必要はありません。
- `gh-pages` ブランチは自動生成・管理されます。
- 詳細やカスタマイズは [peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages) のドキュメントを参照してください。

## その他
- テーマやカスタマイズは `themes/` や `layouts/`、`assets/` ディレクトリで管理します。
- 記事は `content/posts/` 以下に Markdown で追加してください。

---

何か不明点があれば [Hugo公式ドキュメント](https://gohugo.io/documentation/) を参照してください。
