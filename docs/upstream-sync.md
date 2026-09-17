# upstream 変更の取り込み

FSC ガイドには2つのリポジトリがあり、それぞれ upstream が異なります。

| リポジトリ | upstream | 何を取り込むか |
|---|---|---|
| fsc-guide-showroom（コンテンツ） | `rhpds/showroom_template_nookbag` | テーマ、ビルド設定、CI |
| fsc-guide-env（環境定義） | `rhpds/showroom-deployer` | Showroom イメージバージョン、Helm テンプレート |

> **注意**: `rhpds/field-sourced-content-template` の Showroom バージョンは最新でない場合があります。イメージバージョンは `rhpds/showroom-deployer` と照合してください。

---

## A. コンテンツリポジトリ（fsc-guide-showroom）の更新

### 初回セットアップ（一度だけ）

```bash
cd fsc-guide-showroom
git remote add upstream https://github.com/rhpds/showroom_template_nookbag.git

git remote -v
# origin    https://github.com/field-sourced-content-wg/fsc-guide-showroom.git (fetch)
# upstream  https://github.com/rhpds/showroom_template_nookbag.git (fetch)
```

### 取り込み手順

```bash
git fetch upstream
git checkout upstream
git merge upstream/main
git checkout develop
git merge upstream
# コンフリクトがあれば解決
git push origin upstream
git push origin develop
```

### コンフリクト解決の指針

| ファイル | 方針 |
|----------|------|
| `site.yml` | **自分を優先**（タイトル、URL はカスタマイズ済み） |
| `content/antora.yml` | **自分を優先**（属性定義はカスタマイズ済み） |
| `ui-config.yml` | **自分を優先**（タブ構成はカスタマイズ済み） |
| `.github/workflows/` | **upstream を優先**（CI/CD の改善を取り込む） |
| `package.json` | **upstream を優先**（依存関係の更新を取り込む） |
| `content/modules/ROOT/pages/` | **コンフリクトなし**（独自コンテンツなので衝突しない） |

---

## B. 環境定義リポジトリ（fsc-guide-env）の更新

環境定義の Showroom コンポーネントを更新する手順です。

### 1. 最新バージョンを確認

```bash
gh api repos/rhpds/showroom-deployer/contents/charts/showroom-single-pod/values.yaml --jq '.content' | base64 -d | grep "image:"
```

### 2. 自分の環境と比較

```bash
cd fsc-guide-env
grep -E "image:|zero_touch_bundle:" helm/values.yaml
```

### 3. 差異がある場合の更新

バージョン番号だけの変更なら `helm/values.yaml` を編集。テンプレート構造が変わっている場合は `showroom-deployer` から `helm/components/showroom/` のファイルを差し替え。

```bash
# テンプレートの差し替えが必要な場合
gh api repos/rhpds/showroom-deployer/contents/charts/showroom-single-pod/values.yaml --jq '.content' | base64 -d > helm/components/showroom/values.yaml
gh api repos/rhpds/showroom-deployer/contents/charts/showroom-single-pod/templates/deployment.yaml --jq '.content' | base64 -d > helm/components/showroom/templates/showroom.yaml
# 他のテンプレートも同様
```

### 4. RHDP で動作確認

更新後は必ず RHDP で環境をデプロイして動作確認すること。

---

## 取り込みの頻度

- 月1回程度の確認を推奨
- Showroom の大きなアップデート時（メジャーバージョン変更）は早めに取り込む
- セキュリティ関連の更新は速やかに取り込む
