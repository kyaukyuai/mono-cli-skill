---
name: mono-cli
description: mono CLIツール — 個人開発者のためのグロースプラットフォームのCLI
version: 0.1.9
invariants:
  - ミューテーション前に必ず --dry-run で検証する
  - 削除前にユーザーに確認する
  - リスト取得時は --fields でレスポンスサイズを制限する
  - プログラム的にパースする場合は --json を使う
  - ペイロード構築前に mono schema <resource> でフィールドを確認する
---

# mono CLI

個人開発者のためのグロースプラットフォーム「mono」のコマンドラインツール。

## セットアップ

```bash
# 1. インストール
npm install -g @kyaukyuai/mono-cli

# 2. トークンを作成（Webダッシュボード → 設定 → APIトークン）

# 3. CLIにログイン
mono auth login --pat mono_xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx

# 4. 接続確認
mono auth whoami --json
mono status --json
```

## 認証

CLIはPersonal Access Token (PAT) を使用します。トークンは以下の優先順位で解決されます:

1. `--token <PAT>` フラグ
2. `MONO_TOKEN` 環境変数
3. `~/.mono/config.json` の保存値

デフォルトの API ベースURL は `https://www.mono.style` です。
`mono config set base-url <url>` で永続的に上書きできます。

## コマンド一覧

### 認証

```bash
mono auth login --pat <PAT>    # トークン保存
mono auth logout               # トークン削除
mono auth whoami               # ユーザー情報
```

### 作品 (Works)

```bash
# 一覧取得
mono works list --json
mono works list --fields id,title,status --json
mono works list --status live --ndjson
mono works list --status live --limit 20 --offset 0 --sort created_at:desc --json
mono works search "React" --json
mono works stats --json

# 詳細取得
mono works get <id> --json

# 作成
mono works create --json-data '{"title":"My App","category":"Webアプリ","description":"素晴らしいアプリです"}' --dry-run
mono works create --json-data '{"title":"My App","category":"Webアプリ","description":"素晴らしいアプリです"}' --json
mono works create --json-file ./work.json --json
cat ./work.json | mono works create --json-stdin --json

# 更新
mono works update <id> --json-data '{"title":"Updated Title"}' --dry-run
mono works update <id> --json-data '{"title":"Updated Title"}' --json
mono works update <id> --json-file ./work.json --json

# 削除
mono works delete <id> --dry-run
mono works delete <id> --yes --json
```

### 記事 (Articles)

```bash
# 一覧取得
mono articles list --json
mono articles list --category tech --sort popular --limit 10 --json

# 詳細取得
mono articles get <id> --json

# 作成
mono articles create --json-data '{"title":"記事タイトル"}' --dry-run
mono articles create --json-data '{"title":"記事タイトル","body":"本文...","category":"tech"}' --json
mono articles create --json-file ./article.json --json
mono articles create --json-data '{"title":"記事タイトル"}' --file ./article.md --json
mono articles create --file ./article.md --category development --json
 # frontmatter (title/category/excerpt/coverImageUrl) と H1 は自動反映
 # 二重フロントマターも自動マージ、重複H1は自動除去
mono articles list --status draft --json
mono articles create --file ./article.md --tags "AI,Claude Code" --json
mono articles search "Claude Code" --json
mono articles stats --json

# 更新
mono articles update <id> --json-data '{"title":"更新タイトル"}' --json
mono articles update <id> --json-file ./article.json --json

# 削除
mono articles delete <id> --dry-run
mono articles delete <id> --yes --json

# 公開/下書き切り替え
mono articles publish <id> --json
```

### 質問 (Questions / Q&A / qa)

```bash
# 一覧取得
mono questions list --json
mono questions list --sort-by popular --status open --tag javascript --json

# 詳細取得
mono questions get <id> --json
mono questions search "デプロイ" --json

# 作成
mono questions create --json-data '{"title":"質問タイトルは10文字以上","body":"質問の本文は20文字以上で記述してください"}' --dry-run
mono questions create --json-data '{"title":"質問タイトルは10文字以上","body":"質問の本文は20文字以上で記述してください"}' --json
mono questions create --json-file ./question.json --json

# 更新
mono questions update <id> --json-data '{"status":"solved"}' --json
mono questions update <id> --json-file ./question.json --json

# 削除
mono questions delete <id> --dry-run
mono questions delete <id> --yes --json

# 回答
mono questions answer <id> --json-data '{"body":"回答の本文は20文字以上で記述してください"}' --json
mono questions answer <id> --json-file ./answer.json --json
```

### プロフィール (Profile)

```bash
# 自分のプロフィール取得
mono profile get --json
mono profile get --fields name,bio,role --json

# プロフィール更新
mono profile update --json-data '{"name":"新しい名前","bio":"自己紹介"}' --dry-run
mono profile update --json-data '{"name":"新しい名前","bio":"自己紹介","githubUrl":"https://github.com/user"}' --json
mono profile update --json-file ./profile.json --json
```

### 画像アップロード (Upload / images)

```bash
# アップロード（URL取得）
mono upload ./screenshot.png --json
# => { "url": "https://...", "fileName": "...", "size": 12345, "type": "image/png" }

# ファイル検証のみ（アップロードなし）
mono upload ./screenshot.png --dry-run

# 特定フィールドのみ取得
mono upload ./photo.jpg --fields url --json
```

対応形式: `.jpg`, `.jpeg`, `.png`, `.gif`, `.webp`, `.svg`（最大5MB）

### タグ (Tags)

```bash
mono tags list --json
mono tags create --name "AI" --json
```

### ステータス

```bash
mono status --json
```

### 設定

```bash
mono config list --json
mono config set base-url https://www.mono.style
mono config set fields id,title,status
mono config set output-format json
mono config path
```

### スキーマ

```bash
mono schema works.create
mono schema --all
```

### 補完

```bash
mono completions bash > /usr/local/etc/bash_completion.d/mono
mono completions zsh > ~/.zsh/completions/_mono
mono completions fish > ~/.config/fish/completions/mono.fish
```

### スキーマ内省

```bash
mono schema works            # worksリソースの全操作スキーマ
mono schema works.create     # 作成操作のスキーマのみ
mono schema articles         # articlesリソースの全操作スキーマ
mono schema questions        # questionsリソースの全操作スキーマ
mono schema profile          # profileリソースのスキーマ
```

### その他

```bash
mono version                 # バージョン表示
```

## グローバルフラグ

| フラグ | 説明 | デフォルト |
|--------|------|-----------|
| `--json` | JSON出力 | false (human) |
| `--ndjson` | リストをNDJSON出力 | false |
| `--fields <f1,f2>` | フィールドマスク | all |
| `--dry-run` | ミューテーション検証のみ | false |
| `--base-url <url>` | APIベースURL上書き | config値 |
| `--token <pat>` | トークン上書き | config値 |
| `--verbose` | デバッグ出力 | false |
| `--timeout <ms>` | タイムアウト | 30000 |
| `--retry <n>` | リトライ回数 | 0 |
| `--retry-wait <ms>` | リトライ待機 | 500 |
| `--print-curl` | curlコマンドを出力 | false |
| `--no-mask-token` | print-curl のトークンマスク解除 | false |
| `--quiet` | 標準出力を最小化 | false |
| `--no-color` | カラー出力無効 | false |

## ワークフロー例

### 作品を安全に作成する

```bash
# 1. スキーマを確認
mono schema works.create

# 2. ドライランで検証
mono works create --json-data '{"title":"Portfolio Site","category":"Webアプリ","description":"Next.jsで作ったポートフォリオサイト"}' --dry-run

# 3. 作成
mono works create --json-data '{"title":"Portfolio Site","category":"Webアプリ","description":"Next.jsで作ったポートフォリオサイト"}' --json
```

### 記事を作成して公開する

```bash
# 1. 下書き作成
mono articles create --json-data '{"title":"開発日記","body":"# はじめに\n\n本文...","category":"dev-log"}' --json

# 2. 公開
mono articles publish <id> --json
```

### 質問して回答を確認する

```bash
# 1. 質問を投稿
mono questions create --json-data '{"title":"Next.js 16でのルーティングについて","body":"App Routerでの動的ルートの設定方法を教えてください。具体的には..."}' --json

# 2. 質問の詳細と回答を確認
mono questions get <id> --json
```

### 画像付きで作品を作成する

```bash
# 1. 画像をアップロード
mono upload ./screenshot.png --json
# => { "url": "https://xxxxx.public.blob.vercel-storage.com/...", ... }

# 2. 返却URLを使って作品を作成
mono works create --json-data '{"title":"My App","category":"Webアプリ","description":"素晴らしいアプリです","thumbnailUrl":"https://xxxxx.public.blob.vercel-storage.com/..."}' --json
```

## 出力フォーマット

- **human** (デフォルト): テーブル形式、人間可読
- **json** (`--json`): プリティプリントJSON、プログラム処理向け
- **ndjson** (`--ndjson`): 1行JSON、ストリーミング処理向け

## エラーハンドリング

エラーはJSON形式で標準エラー出力に出力されます:

```json
{
  "error": "認証が必要です。`mono auth login` でログインしてください。",
  "exitCode": 1
}
```

終了コード:
- `0`: 成功
- `1`: エラー (認証、バリデーション、APIエラー)
