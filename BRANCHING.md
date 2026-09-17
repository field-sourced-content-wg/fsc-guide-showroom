# ブランチ運用ルール

## ブランチ構成

```
main              ← 本番（デプロイ対象）。直接 push 禁止
├── develop       ← 開発統合ブランチ。feature ブランチをここにマージ
├── feature/*     ← 機能追加・修正用（例: feature/add-module05）
└── upstream      ← upstream テンプレートの変更追跡用
```

## ブランチの役割

| ブランチ | 用途 | マージ先 | 保護 |
|----------|------|----------|------|
| `main` | リリース済みの安定版。RHDP / GitHub Pages はここを参照 | — | 直接 push 禁止、PR 必須 |
| `develop` | 開発中の統合ブランチ。日常の作業はここに集約 | `main` | PR 推奨 |
| `feature/*` | 個別の機能追加や修正 | `develop` | — |
| `upstream` | Showroom テンプレートや FSC テンプレートの変更を追跡 | `develop` | 手動マージのみ |

## 日常のワークフロー

### 1. 新しい作業を始める

```bash
git checkout develop
git pull origin develop
git checkout -b feature/add-module05
```

### 2. 作業してコミット

```bash
git add content/modules/ROOT/pages/05-new-page.adoc
git commit -m "Add module 05: new exercise"
```

### 3. develop にマージ（PR 推奨）

```bash
git push origin feature/add-module05
# GitHub で develop 向けの PR を作成
# チームメンバーにレビューを依頼
# レビュー後にマージ
```

### 4. main にリリース

develop の内容が安定したら、develop → main の PR を作成してマージ。

```bash
# GitHub で main 向けの PR を作成
# 動作確認後にマージ
```

## upstream からの変更取り込み

Showroom テンプレートや FSC テンプレートが更新された場合の取り込み手順。

### 初回セットアップ（一度だけ）

```bash
# upstream リモートを追加
git remote add upstream https://github.com/rhpds/showroom_template_nookbag.git
```

### 変更の取り込み

```bash
# upstream の最新を取得
git fetch upstream

# upstream ブランチに切り替えて更新
git checkout upstream
git merge upstream/main

# develop にマージ
git checkout develop
git merge upstream
# コンフリクトがあれば解決
git push origin develop
```

### 取り込み時の注意

- `site.yml`, `antora.yml`, `ui-config.yml` はカスタマイズ済みのため、コンフリクトが発生しやすい → 自分の変更を優先
- `content/modules/ROOT/pages/` 内は独自コンテンツなのでコンフリクトは少ない
- テーマ（UI bundle）やビルド設定の更新が主な取り込み対象

## コンフリクト解決の指針

| ファイル | 方針 |
|----------|------|
| `site.yml` | 自分のカスタマイズを優先（タイトル、URL 等） |
| `content/antora.yml` | 自分の属性定義を優先 |
| `ui-config.yml` | 自分のタブ構成を優先 |
| `.github/workflows/` | upstream の更新を取り込む |
| `package.json` | upstream の更新を取り込む |

## feature ブランチの命名規則

| 接頭辞 | 用途 | 例 |
|--------|------|-----|
| `feature/` | 新しいページやセクションの追加 | `feature/add-use-cases` |
| `fix/` | 既存コンテンツの修正 | `fix/typo-in-helm-section` |
| `update/` | 既存コンテンツの更新 | `update/rhdp-deploy-steps` |

## レビュープロセス

1. PR を作成し、1名以上のチームメンバーをレビュアーに指定
2. レビュアーは内容の正確性と AsciiDoc の記法を確認
3. Approve 後にマージ
4. マージ後に feature ブランチを削除
