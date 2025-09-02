# テンプレート機能付き仮想キーボード

A virtual keyboard with template functionality for quick text input and copying.

## 機能 (Features)

- **A-Z キーボード**: QWERTY配列のアルファベットキーボード
- **テンキー**: 電卓風の数字入力パッド
- **テンプレート機能**: コード入力で定型文を呼び出し
- **コピー機能**: ワンクリックでクリップボードにコピー
- **レスポンシブデザイン**: モバイル・デスクトップ両対応

## 使用方法 (Usage)

1. キーボードで3文字のコードを入力（例：`a01`）
2. 自動的にテンプレート文が表示される
3. 「コピー」ボタンでクリップボードにコピー
4. 他のアプリケーションで貼り付け

## テンプレート例 (Template Examples)

- `a01` → こんにちは
- `a02` → こんばんは
- `b01` → Good morning
- `c01` → ありがとうございます
- `d01` → 会議室

## ファイル構成 (Files)

- `index.html` - メインアプリケーション（GitHub Pages公開用）

## 起動方法 (How to Run)

1. `index.html` をブラウザで開く
2. または、ローカルサーバーを起動:
   ```bash
   python3 -m http.server 3000
   ```
3. ブラウザで `http://localhost:3000/` にアクセス

## 技術仕様 (Technology)

- HTML5, CSS3, JavaScript
- レスポンシブデザイン
- Clipboard API対応
- タッチ操作対応

## 開発ルール (Development Rules)

⚠️ **重要**: このプロジェクトでは`index.html`のみを編集してください

- **メインファイル**: `index.html` （GitHub Pagesで公開される）
- **編集禁止**: 重複ファイルの作成は避ける（`virtual-keyboard.html`など）
- **バージョン管理**: v0.01ずつインクリメント（現在: v0.21 → 次回: v0.22）
- **ファイル管理**: 不要なファイル作成により混乱を避けるため、新規ファイル作成前には必ず確認

## GitHub Pages

Live Demo: https://muumuu8181.github.io/virtual-keyboard/

## 作成者 (Author)

Created with Claude Code 🤖