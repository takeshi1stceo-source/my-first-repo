# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

iPhoneで閲覧できる週間献立表・買い物リストのシングルページWebアプリ。`index.html` 1ファイルで完結しており、ビルドツール・依存パッケージは一切ない。

## アーキテクチャ

`index.html` はHTML・CSS・JavaScriptをすべてインラインで持つ。

**状態管理:** `state` オブジェクト1つで全データを管理し、変更のたびに `localStorage` へ JSON シリアライズして永続化する。

```
state = {
  weekOffset: number,      // 現在表示中の週（0=今週）
  meals: {                 // weekKey → mealKey → { name, ingredients[] }
    'YYYY-Www': { 'YYYY-MM-DD_夜': { name, ingredients } }
  },
  shopItems: [],           // { id, name, done }
  memo: string
}
```

**週キー:** `getWeekKey(offset)` で `YYYY-Www` 形式を生成。献立データのネストキーとして使用。

**食事区分:** `const MEALS` 配列で制御。現在は `['夜']` のみ。

**3タブ構成:**
- `page-meal` — 週間献立グリッド（`renderMealGrid()` で動的生成）
- `page-shop` — 買い物リスト（`renderShopList()`）
- `page-memo` — フリーメモ

**献立→買い物連携:** `importFromMeals()` が現在週の全 `ingredients[]` を重複排除して `shopItems` へ追加する。

## 開発・確認方法

ビルド不要。`index.html` をブラウザで直接開くか、ローカルサーバーを立てて確認する。

```bash
python3 -m http.server 8080
# → http://localhost:8080
```

iPhoneで確認する場合は同一LAN内のPCのIPアドレスを使用する（例: `http://192.168.x.x:8080`）。
