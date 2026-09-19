---
name: update-products
description: SoignéのLPに掲載する商品情報を、Squareカタログから取得して data/products.json を更新する。「商品を更新して」「Squareと同期して」「商品情報が変わった」「LP掲載商品を差し替えたい」「価格が変わった」と言われたとき、および週次の差分監視から呼ばれたときに使う。必ず差分を提示し、承認を得てから書き込む。
---

# Soigné 商品情報の更新

`data/products.json` を Square カタログの内容に合わせる。**このファイルを手で編集してはならない。**

## 大原則

| 原則 | 内容 |
|---|---|
| **情報源はSquareのみ** | 商品名・価格・産地情報はSquareが正。リポジトリ側で書き換えない |
| **承認なしに書き込まない** | 必ず差分を提示し、明示的な承認を得てから更新する |
| **Squareには書き込まない** | このスキルは読み取り専用。カタログの変更はユーザーがSquare管理画面で行う |
| **文章は自動生成しない** | `note_short` は人が書く。新商品では空のまま提示し、ユーザーに執筆を依頼する |

## 手順

### 1. 掲載対象を取得

`data/products.json` の `source.curation_category_status` を見る。

**`ACTIVE` の場合** — `source.curation_category_id` のカテゴリに属する商品を取得する:

```
mcp__Square__make_api_request
  service: catalog
  method:  searchObjects
  request: { "object_types": ["ITEM"], "limit": 100 }
```

返ってきた ITEM のうち `item_data.categories[].id` に当該カテゴリIDを含むものが掲載対象。

**`NOT_CREATED` の場合** — カテゴリが未作成。`featured[].id` の固定リストを対象とし、**報告の冒頭で「カテゴリ未作成のため固定リストで動作しています」と明示する。**

カテゴリを新規に発見した場合(名前が「LP掲載」のCATEGORYが存在する)は、`curation_category_id` と `curation_category_status: "ACTIVE"` への更新も差分に含めて提案する。

### 2. 差分を作る

現行の `data/products.json` と比較し、以下を検出する:

- **価格の変更** — 旧→新を必ず併記
- **商品名・産地情報の変更**
- **掲載対象の増減** — 追加された商品、外れた商品
- **`ecom_visibility` の変化** — `VISIBLE` 以外になった商品は掲載から外すべき
- **整合性の警告**:
  - 価格が 0 または異常値(150gで1,000円未満 / 10,000円超)
  - `note_short` が空の商品
  - Squareに存在しないのに `featured` に残っている商品
  - `featured` が3件でない

### 3. 差分を提示する

必ず表形式で、変更がない場合も「差分なし」と明示する。

```
## 差分

| 商品 | 項目 | 現在 | Square |
|---|---|---|---|
| CostaRica La Candellila | 価格 | ¥1,720 | ¥1,850 |

## 警告
- 新商品「◯◯」には note_short がありません。掲載前に一文をご用意ください。
```

### 4. 承認を待つ

**ここで必ず止まる。** 承認が得られるまで `data/products.json` を書き換えない。

### 5. 反映

承認後:

1. `data/products.json` を更新(`fetched_at` を当日の日付に)
2. `python3 -c "import json;json.load(open('data/products.json'))"` で妥当性を確認
3. コミット。メッセージは変更内容を具体的に書く(例: `Update CostaRica La Candellila price to ¥1,850`)
4. `git push -u origin <現在のブランチ>` でプッシュ

## 週次監視から呼ばれた場合

差分がなければ**何も報告しない。** 差分があるときだけ、上記2〜3の形式で報告し、承認を待つ。

## 必要な権限

Square MCP コネクタの `ITEMS_READ` のみ。書き込み権限は使わない。アクセストークンをリポジトリに保存してはならない。
