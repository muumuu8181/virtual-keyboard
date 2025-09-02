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

- `virtual-keyboard.html` - メインアプリケーション

## 起動方法 (How to Run)

1. `virtual-keyboard.html` をブラウザで開く
2. または、ローカルサーバーを起動:
   ```bash
   python3 -m http.server 3000
   ```
3. ブラウザで `http://localhost:3000/virtual-keyboard.html` にアクセス

## 技術仕様 (Technology)

- HTML5, CSS3, JavaScript
- レスポンシブデザイン
- Clipboard API対応
- タッチ操作対応

## 作成者 (Author)

Created with Claude Code 🤖