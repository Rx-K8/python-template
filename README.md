# プロジェクト名

<!-- このテンプレートを使用する場合は、このREADME.mdを削除して、プロジェクト固有の内容に書き換えてください -->

プロジェクトの簡単な説明をここに記載してください。

## 開発

開発に参加する場合は[ONBOARDING.md](ONBOARDING.md)を参照してください。

---

## テンプレート使用時の初期設定

このテンプレートから新しいプロジェクトを作成する場合、以下の手順を実行してください:

### 1. プロジェクト名の変更

`pyproject.toml`を開き、以下を更新:
- `name`: プロジェクト名
- `description`: プロジェクトの説明
- `requires-python`: 必要なPythonバージョン（デフォルトは `>=3.13`）

### 2. Pythonバージョンの変更（必要に応じて）

`.python-version`ファイルを編集して、使用するPythonバージョンを指定:
```bash
echo "3.12" > .python-version
```

### 3. GitHub Actionsの設定

リポジトリで以下の設定を行う:
1. **Settings** > **Actions** > **General** を開く
2. **Workflow permissions** で "**Read and write permissions**" を選択
3. **Save** をクリック

### 4. このREADME.mdを書き換え

このファイルをプロジェクト固有の内容に書き換えてください。