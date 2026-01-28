# 開発環境セットアップガイド

このテンプレートを使って新しいPythonプロジェクトを始めるためのガイドです。

## このテンプレートについて

このテンプレートには以下のツールが設定済みです：

- **uv** - 高速なPythonパッケージマネージャー
- **Ruff** - 高速なリンター・フォーマッター
- **mypy** - 静的型チェッカー
- **pytest** - テストフレームワーク
- **pre-commit** - Git commitフック
- **GitHub Actions** - CI/CD（テスト・リント・カバレッジ）

## 前提条件

以下のツールをインストールしてください：

### 1. uv（PythonパッケージマネージャーとPythonバージョン管理）

uvはPythonのインストールとパッケージ管理を両方行います。Python本体を別途インストールする必要はありません。

```bash
# macOS / Linux
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### 2. Git

すでにインストール済みの場合がほとんどですが、ない場合は：

```bash
# macOS
brew install git

# Linux (Ubuntu/Debian)
sudo apt install git
```

### 3. GitHub CLI（オプションだが推奨）

```bash
# macOS
brew install gh

# Linux
# https://github.com/cli/cli/blob/trunk/docs/install_linux.md
```

インストール後、認証を行います：
```bash
gh auth login
```

## セットアップ手順

### オプション1: Claude Codeを使う（最も簡単）

Claude Codeがある場合は、以下のコマンドを実行するだけです：

```
「SETUP.mdを読んで初期設定を実行してください」
```

すべての設定が自動的に完了します。

### オプション2: 手動セットアップ

#### ステップ1: テンプレートをクローン

```bash
# GitHubでこのテンプレートから新しいリポジトリを作成
# または
git clone <このリポジトリのURL> my-project
cd my-project
```

#### ステップ2: プロジェクト情報を更新

##### pyproject.toml
```toml
[project]
name = "my-project"  # ← プロジェクト名に変更
version = "0.1.0"
description = "My awesome project"  # ← 説明を変更
```

##### .python-version
使用するPythonバージョンを指定：
```bash
echo "3.13" > .python-version
```

#### ステップ3: Pythonの依存関係をインストール

```bash
# Pythonバージョンをインストール
uv python install

# 仮想環境を作成し、依存関係をインストール
uv sync
```

#### ステップ4: pre-commitフックをセットアップ

```bash
# pre-commitフックをインストール
uv run pre-commit install

# （オプション）全ファイルに対してフックを実行してテスト
uv run pre-commit run --all-files
```

#### ステップ5: 既存のGitリポジトリを削除して新規作成

```bash
# 既存の.gitディレクトリを削除
rm -rf .git

# 新しいGitリポジトリとして初期化
git init
git add .
git commit -m "feat: initial commit"
```

#### ステップ6: GitHubリポジトリを作成してプッシュ

##### GitHub CLIを使う場合（推奨）
```bash
# パブリックリポジトリとして作成
gh repo create my-project --public --source=. --remote=origin --push

# または、プライベートリポジトリとして作成
gh repo create my-project --private --source=. --remote=origin --push
```

##### 手動の場合
1. GitHubでリポジトリを作成
2. ローカルリポジトリと接続：
```bash
git remote add origin git@github.com:username/my-project.git
git branch -M main
git push -u origin main
```

#### ステップ7: GitHub設定

##### 7-1. Discussionsを有効化

**GitHub CLI**
```bash
gh api -X PATCH repos/username/my-project -f has_discussions=true
```

**手動**
1. リポジトリの **Settings** → **General**
2. **Features** で **Discussions** にチェック

##### 7-2. GitHub Actionsの権限設定

**GitHub CLI**
```bash
gh api -X PUT repos/username/my-project/actions/permissions/workflow --input - <<'EOF'
{
  "default_workflow_permissions": "write",
  "can_approve_pull_request_reviews": false
}
EOF
```

**手動**
1. リポジトリの **Settings** → **Actions** → **General**
2. **Workflow permissions** で **Read and write permissions** を選択
3. **Save**

##### 7-3. ラベルを作成

**GitHub CLI**
```bash
gh label create "feature" --color "a2eeef" --description "✨ 新機能の追加" --force
gh label create "refactor" --color "fbca04" --description "♻️ リファクタリング" --force
gh label create "docs" --color "0075ca" --description "📝 ドキュメントの変更" --force
gh label create "internal" --color "ededed" --description "⚙️ 内部実装の変更" --force
gh label create "breaking" --color "d93f0b" --description "💥 破壊的変更" --force
gh label create "security" --color "b60205" --description "🔒 セキュリティ修正" --force
gh label create "upgrade" --color "0e8a16" --description "⬆️ 依存関係のアップグレード" --force
gh label edit "bug" --description "🐛 バグ修正"
```

**手動**
リポジトリの **Issues** → **Labels** から手動で作成

#### ステップ8: README.mdとconfig.ymlを更新

##### README.md
テンプレートの内容を削除し、プロジェクト用の説明に書き換えます：

```markdown
# My Project

[![Test](https://github.com/username/my-project/workflows/Test/badge.svg)](https://github.com/username/my-project/actions?query=workflow%3ATest)
[![Lint](https://github.com/username/my-project/workflows/Lint/badge.svg)](https://github.com/username/my-project/actions?query=workflow%3ALint)
[![Coverage](https://coverage-badge.samuelcolvin.workers.dev/username/my-project.svg)](https://coverage-badge.samuelcolvin.workers.dev/redirect/username/my-project)

My awesome project

## 開発

開発に参加する場合は[ONBOARDING.md](ONBOARDING.md)を参照してください。
```

##### .github/ISSUE_TEMPLATE/config.yml
リポジトリURLを更新：

```yaml
contact_links:
  - name: 質問・ヘルプ / Question / Help
    url: https://github.com/username/my-project/discussions  # ← 更新
```

## 開発の開始

### コードを書く

1. 新しいブランチを作成：
```bash
git checkout -b feature/my-feature
```

2. コードを書く（`src/` ディレクトリ内など）

3. テストを書く（`tests/` ディレクトリ内）

### テストを実行

```bash
# すべてのテストを実行
uv run pytest

# カバレッジ付きで実行
uv run pytest --cov

# 特定のテストファイルを実行
uv run pytest tests/test_something.py
```

### リントとフォーマット

```bash
# Ruffでリントとフォーマットを実行
uv run ruff check .
uv run ruff format .

# mypyで型チェック
uv run mypy .
```

### pre-commitフックを手動実行

```bash
# ステージングされたファイルに対して実行
uv run pre-commit run

# すべてのファイルに対して実行
uv run pre-commit run --all-files
```

### コミットとプッシュ

```bash
git add .
git commit -m "feat: add new feature"
git push origin feature/my-feature
```

commitすると、自動的にpre-commitフックが実行されます。

### プルリクエストを作成

```bash
# GitHub CLI
gh pr create --title "Add new feature" --body "説明"

# または、GitHubのWebUIから作成
```

## オプション設定

### Smokeshow（カバレッジレポート公開）

カバレッジレポートをオンラインで公開したい場合：

1. [https://smokeshow.helpmanual.io/](https://smokeshow.helpmanual.io/) でアカウント作成
2. 認証キーを取得
3. GitHubシークレットに追加：
```bash
gh secret set SMOKESHOW_AUTH_KEY
# プロンプトでキーを入力
```

GitHub Actionsが自動的にカバレッジレポートを公開します。

## トラブルシューティング

### uvが見つからない
```bash
# パスを確認
echo $PATH

# uvを再インストール
curl -LsSf https://astral.sh/uv/install.sh | sh
```

### pre-commitフックが失敗する
```bash
# フックを更新
uv run pre-commit autoupdate

# 再実行
uv run pre-commit run --all-files
```

### GitHub Actionsが失敗する
- リポジトリの **Settings** → **Actions** で権限設定を確認
- ワークフローファイル（`.github/workflows/`）のエラーログを確認

### 依存関係のエラー
```bash
# キャッシュをクリアして再インストール
rm -rf .venv
uv sync
```

## 参考リンク

- [uv Documentation](https://docs.astral.sh/uv/)
- [Ruff Documentation](https://docs.astral.sh/ruff/)
- [mypy Documentation](https://mypy.readthedocs.io/)
- [pytest Documentation](https://docs.pytest.org/)
- [pre-commit Documentation](https://pre-commit.com/)
- [GitHub Actions Documentation](https://docs.github.com/ja/actions)

## 次のステップ

1. `src/` ディレクトリにコードを追加
2. `tests/` ディレクトリにテストを追加
3. [ONBOARDING.md](ONBOARDING.md) で開発ワークフローを確認
4. Issue/PRテンプレートをカスタマイズ
5. 開発を始める！

---

質問がある場合は、GitHub Discussionsを活用してください。

Happy coding! 🚀
