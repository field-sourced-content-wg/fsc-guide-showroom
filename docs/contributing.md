# コントリビューション手順

## コンテンツの追加・修正フロー

```
1. feature ブランチ作成
2. AsciiDoc ページを追加・編集
3. ローカルプレビューで確認
4. PR を作成（develop 向け）
5. レビュー・マージ
```

## 新しいページを追加する

### 1. feature ブランチを作成

```bash
git checkout develop
git pull origin develop
git checkout -b feature/add-new-page
```

### 2. ページファイルを作成

`content/modules/ROOT/pages/` に AsciiDoc ファイルを作成:

```bash
vi content/modules/ROOT/pages/XX-page-name.adoc
```

ページの基本テンプレート:

```asciidoc
= ページタイトル
:navtitle: 短いタイトル

== 学習目標

* 目標1
* 目標2

== エクササイズ 1: 作業名

=== 手順

. 手順の説明:
+
[source,bash,role="execute"]
----
コマンド
----

NOTE: 補足情報

== まとめ

* ポイント1
* ポイント2
```

### 3. ナビゲーションに追加

`content/modules/ROOT/nav.adoc` にページを追加:

```asciidoc
* xref:XX-page-name.adoc[ページタイトル]
```

### 4. ローカルプレビュー

```bash
podman run --rm --name antora -v $PWD:/antora -p 8080:8080 -i -t ghcr.io/juliaaano/antora-viewer
```

ブラウザで http://localhost:8080 を開いて確認。

### 5. PR を作成

```bash
git add -A
git commit -m "Add new page: XX-page-name"
git push origin feature/add-new-page
gh pr create --base develop --title "Add: ページタイトル" --body "追加内容の説明"
```

## 既存ページを修正する

feature ブランチを作成して修正し、develop 向けの PR を作成。手順は新規追加と同じ。

## レビュープロセス

1. PR を作成し、1名以上のチームメンバーをレビュアーに指定
2. レビュアーは以下を確認:
   - 内容の正確性
   - AsciiDoc の記法
   - ナビゲーション（nav.adoc）との整合性
3. Approve 後にマージ
4. マージ後に feature ブランチを削除

## コーディング規約

### ファイル命名

- 番号付きプレフィックス: `01-`, `02-`, ... で順序を管理
- ケバブケース: `my-page-name.adoc`

### AsciiDoc 記法

- ページタイトルは `=`（H1）、セクションは `==`（H2）以降
- 実行可能なコマンドは `[source,bash,role="execute"]` を使用
- 注意書きは `NOTE:`, `IMPORTANT:`, `TIP:` を使用
- 変数は `antora.yml` で定義し `{変数名}` で参照
