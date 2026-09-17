# ブランチ運用

## ブランチ構成

```
main              ← リリース済みの安定版。RHDP / GitHub Pages はここを参照
├── develop       ← 開発統合ブランチ。feature をここにマージ
├── feature/*     ← 機能追加・修正用
└── upstream      ← Showroom テンプレートの変更追跡用
```

## ブランチの役割

| ブランチ | 用途 | マージ先 | ルール |
|----------|------|----------|--------|
| `main` | 安定版 | — | 直接 push 禁止、PR 必須 |
| `develop` | 開発統合 | `main` | PR 推奨 |
| `feature/*` | 個別作業 | `develop` | 自由 |
| `upstream` | テンプレート追跡 | `develop` | 手動マージのみ |

## 日常のワークフロー

### 新しい作業を始める

```bash
git checkout develop
git pull origin develop
git checkout -b feature/add-module05
```

### 作業してコミット

```bash
git add content/modules/ROOT/pages/05-new-page.adoc
git commit -m "Add module 05: new exercise"
```

### develop にマージ

```bash
git push origin feature/add-module05
# GitHub で develop 向けの PR を作成
# レビュー後にマージ
```

### main にリリース

develop が安定したら develop → main の PR を作成してマージ。

## feature ブランチの命名規則

| 接頭辞 | 用途 | 例 |
|--------|------|-----|
| `feature/` | 新しいページやセクションの追加 | `feature/add-use-cases` |
| `fix/` | 既存コンテンツの修正 | `fix/typo-in-helm-section` |
| `update/` | 既存コンテンツの更新 | `update/rhdp-deploy-steps` |
