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

Claude Code 上で以下のスラッシュコマンドを使うのが基本フローです。

```text
1. /new [タイトル]    新しい記事の枠を作成（draft = true）
2. 本文を書く          tags の設定もここで
3. /review            公開前チェック（任意）
4. /publish           draft = false にして date 更新、commit & push
```

push されると GitHub Actions が自動でビルド＆デプロイしてくれます。

### ローカルで確認したいとき

```sh
hugo server -D   # draft 記事も表示
```

### 手動でやる場合

```sh
# 1. 記事を作る（ファイル名: YYYYMMDD-連番.md）
hugo new posts/$(date +%Y%m)/$(date +%Y%m%d)-01.md

# 2. 作成されたファイルを編集する
#    - title, tags を設定
#    - 本文を書く
#    - 公開するなら draft = false にする

# 3. 公開
git add . && git commit -m "記事を追加" && git push
```

### 記事テンプレート

```toml
+++
date = '2026-05-01T09:04:30+09:00'
title = '記事のタイトル'
tags = ['Hugo', 'AWS']
draft = true
+++

本文をここに書く。
```

### ファイル配置ルール

```
content/posts/
  └── YYYYMM/             # 年月ディレクトリ
      ├── YYYYMMDD-01.md  # 記事ファイル（日付-連番）
      └── YYYYMMDD-02.md
```

## 必要なツール
- [Hugo](https://gohugo.io/getting-started/installing/) (`brew install hugo`)
- [Git](https://git-scm.com/)
- [Claude Code](https://docs.claude.com/en/docs/claude-code)（スラッシュコマンドでの執筆フローを使う場合）

## スラッシュコマンド

`.claude/commands/` 配下に、このプロジェクト専用のコマンドを定義しています。

| コマンド | 役割 |
| --- | --- |
| `/new [タイトル]` | `content/posts/YYYYMM/YYYYMMDD-NN.md` を作成（`draft = true`）。タイトル省略時はファイル名がそのまま入る |
| `/review` | 公開前チェック。front matter の不備、コードブロックの言語指定、誤字・脱字などを確認 |
| `/publish [ファイルパス]` | `draft = false` に切り替え、`date` を現在時刻に更新、commit & push。ファイル省略時は直近の draft 記事を自動選択 |

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
