# テンプレート機能付き仮想キーボード

[![PWA](https://img.shields.io/badge/PWA-enabled-blue)](https://muumuu8181.github.io/virtual-keyboard/)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)

A virtual keyboard with template functionality for quick text input and copying.

**Live Demo**: [https://muumuu8181.github.io/virtual-keyboard/](https://muumuu8181.github.io/virtual-keyboard/)

---

## 📖 目次

- [概要](#概要)
- [主要機能](#主要機能)
- [クイックスタート](#クイックスタート)
- [使用方法](#使用方法)
- [テンプレート仕様](#テンプレート仕様)
- [アーキテクチャ](#アーキテクチャ)
- [開発ガイド](#開発ガイド)
- [トラブルシューティング](#トラブルシューティング)
- [ドキュメント](#ドキュメント)

---

## 概要

このプロジェクトは、テンプレート機能を搭載した仮想キーボードアプリケーションです。コード入力により事前登録されたテンプレート文を即座に呼び出し、クリップボードにコピーできます。PWA対応により、モバイル・デスクトップ両環境でオフライン動作します。

### 主な用途
- **定型文の高速入力**: 要件定義、レビュー指示、チェックリスト等の業務文書
- **カスタムテンプレート管理**: 個人用の定型文をローカルに保存
- **クロスプラットフォーム**: Android、iOS、デスクトップで統一されたUI

---

## 主要機能

### ✨ コア機能
- **QWERTYキーボード**: 標準配列のアルファベット・数字キー
- **テンプレート検索**: 3-4文字のコード入力で自動表示（例: `req1`, `oth7`）
- **ワンクリックコピー**: テンプレート表示エリアをクリックでクリップボードにコピー
- **カスタムテンプレート登録**: クリップボードから読み取り、任意のコードで保存
- **メモ機能**: テンプレートとは別にメモを保存・管理

### 🎨 UI機能
- **レスポンシブデザイン**: モバイル・タブレット・デスクトップ対応
- **ズーム機能**: 50%-200%の範囲で画面拡大縮小（保存機能付き）
- **物理キーボード対応**: PC環境での高速入力

### 📱 PWA機能
- **オフライン動作**: Service Workerによるキャッシュ
- **ホーム画面追加**: Android/iOSでネイティブアプリ風に起動
- **フルスクリーン表示**: ブラウザUIを最小限に抑えた没入体験

### 💾 データ永続化
- **localStorage**: カスタムテンプレート、メモ、ズーム設定を保存
- **データエクスポート**: 将来的にJSON形式でのバックアップ対応予定

---

## クイックスタート

### 🚀 最速5秒で起動

#### 方法1: ブラウザで直接開く
```bash
# ファイルエクスプローラーでindex.htmlをダブルクリック
# または
start index.html  # Windows
open index.html   # macOS
```

#### 方法2: ローカルサーバー起動（推奨）
```bash
# Python 3の場合
python -m http.server 3000

# Node.jsの場合（http-serverインストール済み）
npx http-server -p 3000

# ブラウザでアクセス
# http://localhost:3000/
```

#### 方法3: GitHub Pagesで利用（最も簡単）
[https://muumuu8181.github.io/virtual-keyboard/](https://muumuu8181.github.io/virtual-keyboard/) にアクセス

### 📱 モバイルインストール
詳細は [`docs/mobile-installation-guide.md`](docs/mobile-installation-guide.md) を参照

---

## 使用方法

### 基本操作

#### 1. テンプレート検索
```
1. 画面中央のキーボードで文字を入力（例: req1）
2. 4文字以上入力すると自動的にテンプレートが表示される
3. または「検索」ボタンをクリック
```

#### 2. テンプレートコピー
```
- テンプレート表示エリア（緑色の枠）をクリック
- 「コピー」ボタンが「コピー完了！」に変わる
- 他のアプリケーションでCtrl+V（Cmd+V）で貼り付け
```

#### 3. カスタムテンプレート登録
```
1. 事前にコピーしたいテキストをクリップボードにコピー
2. 「📝登録」ボタンをクリック
3. テンプレート文を編集（必要に応じて）
4. コード欄に任意のコードを入力（例: work1）
5. 「📝登録」ボタンで保存
```

#### 4. テンプレート一覧・編集・削除
```
1. 「📋一覧」ボタンをクリック
2. カテゴリ別に折りたたみ表示
3. カスタムテンプレートには「編集」「削除」ボタンが表示
```

#### 5. メモ機能
```
- 「📄メモ」ボタン: 新規メモを追加
- 「👁️確認」ボタン: 保存済みメモを閲覧・削除
```

### キーボードショートカット（物理キーボード）
| キー | 動作 |
|------|------|
| A-Z, 0-9 | 文字入力 |
| Backspace | 一文字削除 |
| Delete | 全削除 |
| Enter | テンプレート検索 |
| Space | スペース入力 |

---

## テンプレート仕様

### デフォルトテンプレート

#### REQ系列（要件定義）
| コード | 用途 | 内容 |
|--------|------|------|
| `req1` | 要件定義 | 共通要件定義の詳細テンプレート（パフォーマンス、セキュリティ、ドキュメント要件等） |

#### REV系列（レビュー）
| コード | 用途 | 内容 |
|--------|------|------|
| `rev1` | コードレビュー | 多角レビュー手順（QA/Secエージェント、通過条件、エビデンス等） |

#### CHK系列（動作確認）
| コード | 用途 | 内容 |
|--------|------|------|
| `chk1` | 品質チェック | リンク健全性、重複情報の禁止 |

#### OTH系列（その他）
| コード | 用途 | 内容 |
|--------|------|------|
| `oth1` | ブラウザ確認 | 作成物のブラウザ表示指示 |
| `oth2` | ドキュメント化 | README連携ドキュメント作成指示 |
| `oth3` | テンプレート追加 | 既存番号+1でテンプレート追加指示 |
| `oth4` | サブエージェント調査 | 詳細調査依頼指示 |
| `oth5` | 作業フォルダ準備 | 日付フォルダ+bufフォルダ作成指示 |
| `oth6` | フォルダ評価 | フォルダ構成・ドキュメント10点満点評価指示 |
| `oth7` | フォルダ整理 | 拡張性高く整理、README網羅的拡充指示 |
| `oth42` | 第三者調査 | 作業結果の妥当性をサブエージェントに調査依頼 |

### カスタムテンプレート

#### データ保存仕様
- **保存先**: `localStorage['customTemplates']`
- **形式**: JSON形式（キー: コード、値: テンプレート文）
- **命名規則**: 3文字以上の任意の文字列（英数字推奨）
- **上書き**: 既存コードと同じ場合は確認ダイアログ表示

#### 例
```javascript
// localStorageに保存される形式
{
  "work1": "作業開始テンプレート",
  "meet1": "会議議事録テンプレート"
}
```

---

## アーキテクチャ

### システム構成

```
┌─────────────────────────────────────────────┐
│          index.html (SPA)                   │
├─────────────────────────────────────────────┤
│  HTML: キーボードUI + テンプレート表示      │
│  CSS:  レスポンシブスタイル                 │
│  JS:   イベント処理 + localStorage管理      │
└─────────────────────────────────────────────┘
              ↓                    ↓
    ┌─────────────────┐    ┌──────────────┐
    │  localStorage   │    │ Clipboard API│
    │  (永続化)       │    │  (コピー)    │
    └─────────────────┘    └──────────────┘

┌─────────────────────────────────────────────┐
│  Service Worker (sw.js)                     │
│  - オフラインキャッシュ                     │
│  - バージョン管理                           │
└─────────────────────────────────────────────┘
```

### ファイル構成

```
virtual-keyboard/
├── index.html           # メインアプリケーション（1165行）
│   ├── <style>          # 埋め込みCSS（レスポンシブ、ズーム対応）
│   └── <script>         # 埋め込みJavaScript（全機能実装）
├── manifest.json        # PWA設定（アイコン、表示モード等）
├── sw.js                # Service Worker（キャッシュ管理）
├── README.md            # 本ファイル
└── docs/
    ├── mobile-installation-guide.md  # モバイル向けPWAインストール手順
    ├── changelog-oth-feature.md      # OTH系列機能変更履歴
    ├── ARCHITECTURE.md               # 詳細アーキテクチャ（予定）
    ├── API.md                        # テンプレートAPI仕様（予定）
    └── CONTRIBUTING.md               # 開発者向けガイド（予定）
```

### データフロー

```
[ユーザー入力]
    ↓
[キーボードイベント] → display.textContent に反映
    ↓
[4文字以上入力時] → lookupTemplate(code) 自動実行
    ↓
[テンプレート検索] → defaultTemplates & customTemplates から検索
    ↓
[表示] → templateContent.textContent に設定
    ↓
[クリック] → navigator.clipboard.writeText() でコピー
```

### 技術スタック

| レイヤー | 技術 |
|----------|------|
| **フロントエンド** | Vanilla JavaScript（ES6+）、HTML5、CSS3 |
| **データ永続化** | localStorage API |
| **クリップボード** | Clipboard API（フォールバック: execCommand） |
| **PWA** | Service Worker、Web App Manifest |
| **ホスティング** | GitHub Pages |
| **バージョン管理** | Git（GitHub） |

---

## 開発ガイド

### 開発ルール

#### ⚠️ 重要原則
1. **単一ファイル原則**: `index.html` のみを編集（CSS/JS分離禁止）
2. **バージョン管理**: v0.01刻みでインクリメント
3. **重複ファイル禁止**: `virtual-keyboard.html` 等の類似ファイル作成禁止
4. **ドキュメント必須**: 機能追加時は `docs/` に変更履歴を記録

#### 🎯 バージョン更新手順（超重要）

**✅ 変更箇所はたった1箇所のみ！**

```javascript
// index.html の 17行目のみ変更すれば、全体に自動反映されます
:root {
    --app-version: "v0.XX";  // ★ここだけ変更すればOK（全体に自動反映）
}
```

**仕組み**:
- `index.html:17` の CSS変数 `--app-version` が**唯一の真実の情報源**
- Service Worker (`sw.js`) は URL パラメータ経由で自動取得
- 画面右上のバージョン表示も CSS `::after` で自動反映
- README やドキュメントには固定バージョン番号を記載しない

**注意**:
- `sw.js` のフォールバック値 (`'unknown'`) は変更不要
- バージョン番号は `v` + 数値（例: `v0.53`, `v1.00`）の形式を推奨

### コード規約

#### 命名規則
- **変数**: キャメルケース（例: `currentUser`, `templateDisplay`）
- **関数**: キャメルケース（例: `lookupTemplate()`, `saveCustomTemplates()`）
- **定数**: 大文字スネークケース（例: `CACHE_NAME`）
- **テンプレートコード**: 小文字英数字3-4文字（例: `req1`, `oth7`）

#### コメント規約
```javascript
// 単一行コメント: 処理の説明
/* 複数行コメント:
   セクション区切り、
   複雑なロジックの詳細説明 */
```

### テスト方法

#### 手動テスト項目
```
□ キーボード入力動作確認
  □ 物理キーボード（A-Z, 0-9, Backspace, Enter）
  □ 画面キーボードクリック
  □ ズーム機能（±ボタン、50%-200%）

□ テンプレート機能
  □ デフォルトテンプレート検索（req1, rev1, chk1, oth1-7）
  □ カスタムテンプレート登録
  □ カスタムテンプレート編集・削除
  □ テンプレート一覧表示・折りたたみ

□ コピー機能
  □ テンプレートクリックでコピー
  □ コピー完了通知表示

□ メモ機能
  □ メモ追加・保存
  □ メモ一覧表示・削除

□ PWA機能
  □ Service Worker登録
  □ オフライン動作
  □ ホーム画面追加（モバイル）

□ レスポンシブ
  □ デスクトップ表示
  □ タブレット表示
  □ スマートフォン表示
```

#### ブラウザテスト
```bash
# ローカルサーバー起動
python -m http.server 3000

# テスト対象ブラウザ
- Chrome/Edge (最新版)
- Firefox (最新版)
- Safari (iOS/macOS)
- Chrome Mobile (Android)
```

### デバッグ方法

#### localStorage確認
```javascript
// Chromeデベロッパーツール > Application > Local Storage
// または以下をConsoleで実行
console.log(localStorage.getItem('customTemplates'));
console.log(localStorage.getItem('memos'));
console.log(localStorage.getItem('appZoom'));
```

#### Service Worker確認
```javascript
// Application > Service Workers
// または
navigator.serviceWorker.getRegistrations().then(registrations => {
    console.log(registrations);
});
```

---

## トラブルシューティング

### よくある問題

#### Q1. テンプレートが表示されない
**原因**:
- コード入力ミス（大文字小文字、スペル）
- 4文字未満の入力（自動検索は4文字以上から）

**対処**:
```
1. コードを再確認（例: req1, oth7）
2. 「📋一覧」ボタンで正しいコードを確認
3. 「検索」ボタンを明示的にクリック
```

#### Q2. コピーができない
**原因**:
- ブラウザのクリップボード権限不許可
- HTTPS接続が必要（file://では動作しない場合あり）

**対処**:
```
1. ブラウザのアドレスバーで権限を確認・許可
2. ローカルサーバー経由でアクセス（http://localhost:3000/）
3. GitHub Pagesで利用（HTTPS保証）
```

#### Q3. オフラインで動作しない
**原因**:
- 初回アクセス時にService Workerが登録されていない
- ブラウザキャッシュがクリアされた

**対処**:
```
1. 一度オンライン状態で全ページ読み込み
2. Application > Service Workers で登録確認
3. ページ再読み込み（Ctrl+R）
```

#### Q4. PWAインストールできない
**原因**:
- HTTPSでアクセスしていない
- ブラウザがPWA非対応

**対処**:
```
1. GitHub Pagesから利用（https://muumuu8181.github.io/virtual-keyboard/）
2. Chrome/Edge/Safari最新版を使用
3. モバイル: docs/mobile-installation-guide.md 参照
```

#### Q5. カスタムテンプレートが消えた
**原因**:
- ブラウザのプライベートモード使用
- localStorageクリア
- 別のブラウザ・デバイスで使用

**対処**:
```
1. 通常モードでブラウザ使用
2. 同じブラウザ・デバイスで利用
3. 将来的なエクスポート機能に期待
```

---

## ドキュメント

### 追加ドキュメント一覧

| ドキュメント | 内容 | 対象者 |
|--------------|------|--------|
| [`docs/mobile-installation-guide.md`](docs/mobile-installation-guide.md) | モバイル向けPWAインストール手順 | エンドユーザー |
| [`docs/changelog-oth-feature.md`](docs/changelog-oth-feature.md) | OTH系列機能の変更履歴 | 開発者 |
| `docs/ARCHITECTURE.md`（予定） | 詳細アーキテクチャ設計 | 開発者 |
| `docs/API.md`（予定） | テンプレートAPI仕様 | 開発者 |
| `docs/CONTRIBUTING.md`（予定） | コントリビューションガイド | コントリビューター |

### 関連リンク
- **Live Demo**: [https://muumuu8181.github.io/virtual-keyboard/](https://muumuu8181.github.io/virtual-keyboard/)
- **GitHub Repository**: [https://github.com/muumuu8181/virtual-keyboard](https://github.com/muumuu8181/virtual-keyboard)

---

## FAQ

### Q. 商用利用は可能ですか？
A. MITライセンス想定のため可能です（LICENSE確認推奨）

### Q. テンプレートの文字数制限はありますか？
A. localStorageの容量制限（通常5-10MB）内であれば制限なし

### Q. 複数デバイス間でテンプレート同期できますか？
A. 現在未対応。将来的にJSON形式でのエクスポート/インポート機能を検討中

### Q. 多言語対応していますか？
A. UIは日本語のみ。テンプレート内容は任意の言語で登録可能

---

## 変更履歴

### 最新版
- 🎯 **バージョン管理を完全統一化**（`index.html:17` の1箇所のみ変更で全体反映）
- 📝 README のバージョン更新手順を大幅改善

### v0.51 (2025-10-05)
- ✨ `oth42` テンプレート追加（第三者調査指示）
- 🐛 バージョン不一致を修正（sw.js v0.50→v0.51）
- 🗑️ testsフォルダ完全削除（サブエージェント指摘により）
- 📝 README更新（oth42追加、バージョン統一）

### v0.50 (2025-10-05)
- ✨ `oth7` テンプレート追加（フォルダ整理指示）
- 📝 README大幅拡充（本ドキュメント）
- 📄 ARCHITECTURE.md、CONTRIBUTING.md追加
- 🔧 ドキュメントバージョン統一

### v0.48 (2025-10-04)
- ✨ OTH系列テンプレート追加（`oth1`-`oth6`）
- 📝 変更履歴ドキュメント追加

詳細は [`docs/changelog-oth-feature.md`](docs/changelog-oth-feature.md) 参照

---

**現在のバージョン**: `index.html:17` の `--app-version` を参照してください

---

## ライセンス

MIT License（予定）

---

## 作成者

Created with **Claude Code** 🤖

---

## サポート

質問・バグ報告・機能要望は以下へ:
- GitHub Issues: [https://github.com/muumuu8181/virtual-keyboard/issues](https://github.com/muumuu8181/virtual-keyboard/issues)
- Email: （設定されていない場合は追記）
