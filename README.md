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

## 必要なツール
- [Hugo](https://gohugo.io/getting-started/installing/)
- [Git](https://git-scm.com/)

## サイトのビルド方法

1. 依存ツールのインストール（初回のみ）
   ```sh
   brew install hugo
   ```

2. コンテンツの追加
    ``` sh
    hugo new posts/post-name.md
    ```

3. サイトのローカルプレビュー
   ```sh
   hugo server -D
   ```
   - `http://localhost:1313` で確認できます。

4. 静的ファイルのビルド
   ```sh
   hugo
   ```
   - `public/` ディレクトリに出力されます。

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
