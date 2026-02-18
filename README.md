# GC Surface Tension Game

`ruby.wasm` を使った、ローカル交代プレイ用のブラウザゲームです。  
1行ずつ Ruby を実行し、GC とオブジェクト生成数で勝敗を決めます。

## Files

- `index.html`: アプリ本体（UI + ロジック）
- `spec.md`: 仕様書（English）

## Quick Start

1. このディレクトリで簡易サーバを起動
2. ブラウザで `index.html` を開く
3. プレイヤー名と GC目標回数 `n` を設定して開始

例:

```bash
cd /home/ko1/app/gctension
python3 -m http.server 8000
```

ブラウザで `http://localhost:8000` を開いてください。

## Rules (Short)

1. プレイヤーは1人1行ずつ Ruby を入力
2. 構文エラーの行は確定できない
3. 各ターンで `alloc_delta`（生成オブジェクト増分）をその人のスコアに加算
4. GC進行の合計が `n` に達したら終了
5. 最後のGCを起こした人はスコア `0`
6. 最高スコアの人が勝ち（同点は引き分け）
