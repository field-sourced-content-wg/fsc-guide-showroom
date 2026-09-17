# upstream 変更の取り込み

Showroom テンプレート（`rhpds/showroom_template_nookbag`）や FSC テンプレートが更新された場合の取り込み手順。

> **注意**: FSC テンプレートの Showroom バージョンが最新かどうかは、`rhpds/showroom-deployer` と照合してください。

## 初回セットアップ（一度だけ）

```bash
# upstream リモートを追加
git remote add upstream https://github.com/rhpds/showroom_template_nookbag.git

# 確認
git remote -v
# origin    https://github.com/okadamas/exp-mokada-fsc-guide.git (fetch)
# upstream  https://github.com/rhpds/showroom_template_nookbag.git (fetch)
```

## 変更の取り込み手順

### 1. upstream の最新を取得

```bash
git fetch upstream
```

### 2. upstream ブランチで変更をマージ

```bash
git checkout upstream
git merge upstream/main
```

### 3. develop に取り込み

```bash
git checkout develop
git merge upstream
```

### 4. コンフリクトの解決（発生した場合）

```bash
# コンフリクトファイルを確認
git status

# 手動で解決後
git add <解決したファイル>
git commit
```

### 5. push

```bash
git push origin upstream
git push origin develop
```

## コンフリクト解決の指針

| ファイル | 方針 |
|----------|------|
| `site.yml` | **自分を優先**（タイトル、URL はカスタマイズ済み） |
| `content/antora.yml` | **自分を優先**（属性定義はカスタマイズ済み） |
| `ui-config.yml` | **自分を優先**（タブ構成はカスタマイズ済み） |
| `.github/workflows/` | **upstream を優先**（CI/CD の改善を取り込む） |
| `package.json` | **upstream を優先**（依存関係の更新を取り込む） |
| `content/modules/ROOT/pages/` | **コンフリクトなし**（独自コンテンツなので衝突しない） |

## 取り込みの頻度

- 必須ではないが、月1回程度の確認を推奨
- Showroom テーマの大きなアップデート時は早めに取り込む
- セキュリティ関連の更新は速やかに取り込む

## 取り込み対象の判断

upstream の変更で取り込む価値があるもの:

- UI テーマ（`ui-bundle.zip`）の更新
- GitHub Actions ワークフローの改善
- Antora 関連の依存関係アップデート
- バグ修正

取り込み不要なもの:

- テンプレートのサンプルページ（独自コンテンツに置き換え済み）
- テンプレートの `antora.yml` のサンプル属性

## Showroom イメージバージョンの更新（環境定義リポジトリ）

Showroom のイメージバージョンを更新する場合は、`fsc-guide-env` の `helm/values.yaml` を更新します。

### 最新バージョンの確認

```bash
gh api repos/rhpds/showroom-deployer/contents/charts/showroom-single-pod/values.yaml --jq '.content' | base64 -d | grep "image:"
```
