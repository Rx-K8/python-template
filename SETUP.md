# テンプレートプロジェクト初期設定ガイド

> **このドキュメントはClaude Code用の初期設定手順書です**
> Claude Codeに「SETUP.mdを読んで初期設定を実行してください」と指示すると、このドキュメントに記載された手順を自動的に実行します。

## 前提条件

- `gh` CLI がインストールされ、認証済みであること（`gh auth status`で確認）
- `uv` がインストールされていること

## 初期設定の流れ

### 1. ユーザーへの質問

Claude Codeは以下の情報を対話的に尋ねます：
- **プロジェクト名**（例：`my-project`）
- **プロジェクトの説明**（例：`My awesome project`）
- **GitHubリポジトリ名**（例：`sample-repo`）
- **Pythonバージョン**（例：`3.13`、デフォルトは現在の.python-versionの値）
- **リポジトリの公開設定**（公開/プライベート）

### 2. ファイルの更新

#### pyproject.toml
```toml
[project]
name = "my-project"
version = "0.1.0"
description = "My awesome project"
requires-python = ">=3.13"
```

プロジェクト名と説明を更新します。

#### .python-version
```
3.13
```

使用するPythonバージョンを設定します。

#### .github/ISSUE_TEMPLATE/config.yml
```yaml
contact_links:
  - name: 質問・ヘルプ / Question / Help
    url: https://github.com/username/sample-repo/discussions
```

GitHubリポジトリのURLを更新します（`{owner}/{repo}`のプレースホルダーを実際の値に置換）。

#### README.md
テンプレート内容を削除し、以下の内容に置き換えます：

```markdown
# My Project

[![Test](https://github.com/username/sample-repo/workflows/Test/badge.svg)](https://github.com/username/sample-repo/actions?query=workflow%3ATest)
[![Lint](https://github.com/username/sample-repo/workflows/Lint/badge.svg)](https://github.com/username/sample-repo/actions?query=workflow%3ALint)
[![Coverage](https://coverage-badge.samuelcolvin.workers.dev/username/sample-repo.svg)](https://coverage-badge.samuelcolvin.workers.dev/redirect/username/sample-repo)

My awesome project

## 開発

開発に参加する場合は[ONBOARDING.md](ONBOARDING.md)を参照してください。
```

### 3. 既存のGitリポジトリを削除

```bash
rm -rf .git
```

テンプレートの.gitディレクトリを削除し、新しいリポジトリとして初期化します。

### 4. 新しいGitHubリポジトリを作成

```bash
# パブリックリポジトリの場合
gh repo create sample-repo --public --source=. --remote=origin

# プライベートリポジトリの場合
gh repo create sample-repo --private --source=. --remote=origin
```

新しいGitHubリポジトリを作成し、ローカルリポジトリと接続します。

### 5. 初回コミットとプッシュ

```bash
git add .
git commit -m "feat: initial commit

Co-Authored-By: Claude Sonnet 4.5 <noreply@anthropic.com>"

git push -u origin main
```

初回コミットを作成し、GitHubにプッシュします。

### 6. developブランチの作成とデフォルトブランチの変更

```bash
# developブランチを作成してプッシュ
git checkout -b develop
git push -u origin develop

# デフォルトブランチをdevelopに変更
gh api -X PATCH repos/username/sample-repo -f default_branch=develop
```

developブランチを作成し、GitHubのデフォルトブランチをdevelopに設定します。

### 7. ブランチ保護の設定

```bash
# mainブランチの保護（削除禁止）
gh api -X PUT repos/username/sample-repo/branches/main/protection \
  --input - <<'EOF'
{
  "enforce_admins": false,
  "required_pull_request_reviews": null,
  "required_status_checks": null,
  "restrictions": null,
  "allow_deletions": false
}
EOF

# developブランチの保護（削除禁止）
gh api -X PUT repos/username/sample-repo/branches/develop/protection \
  --input - <<'EOF'
{
  "enforce_admins": false,
  "required_pull_request_reviews": null,
  "required_status_checks": null,
  "restrictions": null,
  "allow_deletions": false
}
EOF
```

mainブランチとdevelopブランチが削除されないように保護ルールを設定します。

### 8. GitHub Discussionsの有効化

```bash
gh api -X PATCH repos/username/sample-repo -f has_discussions=true
```

GitHub DiscussionsをAPI経由で有効化します。

### 9. GitHub Actionsの権限設定

```bash
gh api -X PUT repos/username/sample-repo/actions/permissions/workflow --input - <<'EOF'
{
  "default_workflow_permissions": "write",
  "can_approve_pull_request_reviews": false
}
EOF
```

GitHub Actionsのワークフロー権限を"Read and write permissions"に設定します。

### 10. GitHubラベルの作成

以下のラベルを作成します：

| ラベル | 用途 | 色 |
|--------|------|------|
| `feature` | 新機能の追加 | `a2eeef` |
| `bug` | バグ修正 | `d73a4a` |
| `refactor` | リファクタリング | `fbca04` |
| `docs` | ドキュメントの変更 | `0075ca` |
| `internal` | 内部実装の変更 | `ededed` |
| `breaking` | 破壊的変更 | `d93f0b` |
| `upgrade` | 依存関係のアップグレード | `0e8a16` |
| `infra` | インフラ関連の変更 | `0e8a16` |
| `chore` | 雑務・メンテナンス | `ededed` |

```bash
gh label create "feature" --repo username/sample-repo --color "a2eeef" --description "新機能の追加" --force
gh label create "refactor" --repo username/sample-repo --color "fbca04" --description "リファクタリング" --force
gh label create "docs" --repo username/sample-repo --color "0075ca" --description "ドキュメントの変更" --force
gh label create "internal" --repo username/sample-repo --color "ededed" --description "内部実装の変更（.github、scriptsなど）" --force
gh label create "breaking" --repo username/sample-repo --color "d93f0b" --description "破壊的変更" --force
gh label create "upgrade" --repo username/sample-repo --color "0e8a16" --description "依存関係のアップグレード" --force
gh label create "infra" --repo username/sample-repo --color "0e8a16" --description "インフラ関連の変更" --force
gh label create "chore" --repo username/sample-repo --color "ededed" --description "雑務・メンテナンス" --force
gh label edit "bug" --repo username/sample-repo --description "バグ修正"
```

### 11. オプション設定（Smokeshow）

カバレッジレポートを公開する場合のみ実施：

```bash
# smokeshowをグローバルにインストール
uv tool install smokeshow

# 認証キーを設定（https://smokeshow.helpmanual.io/ で取得）
gh secret set SMOKESHOW_AUTH_KEY
```

## Claude Codeでの使用方法

このテンプレートから新しいプロジェクトを作成する際は、Claude Codeに以下のように指示してください：

```
「SETUP.mdを読んで初期設定を実行してください」
```

Claude Codeが：
1. このドキュメントを読み込み
2. 必要な情報を対話的に質問し
3. すべての初期設定を自動的に実行します

## 確認事項

初期設定後、以下を確認してください：

- [ ] pyproject.tomlが正しく更新されている
- [ ] .python-versionが正しく設定されている
- [ ] README.mdが書き換えられている
- [ ] 新しいGitHubリポジトリが作成されている
- [ ] 初回コミットがプッシュされている
- [ ] GitHub Discussionsが有効化されている
- [ ] GitHub Actionsの権限が"write"に設定されている
- [ ] GitHubラベルが9種類作成されている
- [ ] デフォルトブランチがdevelopに設定されている
- [ ] mainブランチとdevelopブランチに保護ルールが設定されている

## トラブルシューティング

### gh CLIの認証エラー
```bash
gh auth login
```

### 権限エラー
GitHubアカウントにリポジトリ作成権限があることを確認してください。

### リポジトリ名の重複
既に同じ名前のリポジトリが存在する場合は、別の名前を選択してください。

---

**注意**: このドキュメントはテンプレートリポジトリにのみ残し、新しいプロジェクトでは削除してください。
