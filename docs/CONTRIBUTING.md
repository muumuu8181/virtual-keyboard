# コントリビューションガイド

**プロジェクト**: テンプレート機能付き仮想キーボード

このドキュメントは、virtual-keyboardプロジェクトへの貢献方法を説明します。

---

## 目次

- [行動規範](#行動規範)
- [開発環境セットアップ](#開発環境セットアップ)
- [開発フロー](#開発フロー)
- [コード規約](#コード規約)
- [コミットメッセージ規約](#コミットメッセージ規約)
- [プルリクエスト](#プルリクエスト)
- [テスト](#テスト)
- [リリースプロセス](#リリースプロセス)

---

## 行動規範

### 基本原則
1. 敬意を持ったコミュニケーション
2. 建設的なフィードバック
3. オープンなディスカッション

### 禁止事項
- 個人攻撃、差別的発言
- スパム、宣伝
- 他者の知的財産権侵害

---

## 開発環境セットアップ

### 必要なツール

```bash
# 必須
- Git
- テキストエディタ（VS Code推奨）
- モダンブラウザ（Chrome/Firefox/Edge）

# 推奨
- Python 3（ローカルサーバー用）
- Node.js（http-server用、オプション）
```

### リポジトリクローン

```bash
git clone https://github.com/muumuu8181/virtual-keyboard.git
cd virtual-keyboard
```

### ローカル起動

```bash
# 方法1: Pythonサーバー
python -m http.server 3000

# 方法2: Node.js
npx http-server -p 3000

# ブラウザで開く
open http://localhost:3000/
```

---

## 開発フロー

### 1. Issue作成

新機能追加・バグ修正前に、まずIssueを作成してください。

**Issue例**:
```markdown
## 概要
テンプレートのエクスポート機能を追加したい

## 背景
複数デバイス間でテンプレートを共有したい

## 提案内容
- JSON形式でカスタムテンプレートをエクスポート
- ファイル選択でインポート

## 受け入れ基準
- [ ] エクスポートボタンでJSONダウンロード
- [ ] インポートボタンでファイル選択
- [ ] 既存テンプレートとマージ（重複時は確認ダイアログ）
```

### 2. ブランチ作成

```bash
# 機能追加
git checkout -b feature/export-templates

# バグ修正
git checkout -b fix/copy-button-error

# ドキュメント
git checkout -b docs/update-readme
```

**ブランチ命名規則**:
- `feature/xxx`: 新機能
- `fix/xxx`: バグ修正
- `docs/xxx`: ドキュメント
- `refactor/xxx`: リファクタリング
- `test/xxx`: テスト追加

### 3. 開発

#### 重要原則
⚠️ **`index.html`のみを編集**してください

- CSS/JS分離禁止
- 新規ファイル作成禁止（ドキュメント除く）

#### バージョン更新

機能追加時は必ずバージョンを`v0.01`刻みで更新:

```javascript
// 1. index.html
:root {
    --app-version: "v0.51";
}

// 2. sw.js
const CACHE_NAME = 'virtual-keyboard-v0.51';
```

### 4. テスト

#### 手動テスト項目

```
□ 既存機能の動作確認
  □ キーボード入力
  □ テンプレート検索
  □ コピー機能
  □ カスタムテンプレート登録・編集・削除
  □ メモ機能

□ 新機能の動作確認
  □ 仕様通りの動作
  □ エラーケース処理

□ レスポンシブ確認
  □ デスクトップ
  □ タブレット
  □ スマートフォン

□ ブラウザ互換性
  □ Chrome
  □ Firefox
  □ Safari
  □ Edge
```

### 5. コミット

```bash
git add index.html
git commit -m "feat: テンプレートエクスポート機能追加"
```

### 6. プッシュ

```bash
git push origin feature/export-templates
```

### 7. プルリクエスト作成

GitHub上でPRを作成し、以下を記載:

```markdown
## 概要
テンプレートのJSON形式エクスポート機能を追加

## 変更内容
- エクスポートボタン追加（📤ボタン）
- `exportTemplates()`関数実装
- ダウンロード処理（Blob API使用）

## 関連Issue
Closes #42

## テスト
- [x] ローカルで手動テスト完了
- [x] Chrome/Firefox/Safariで動作確認
- [x] モバイル表示確認

## スクリーンショット
（機能のスクリーンショット）

## チェックリスト
- [x] バージョン更新（v0.50 → v0.51）
- [x] sw.jsのキャッシュ名更新
- [x] README更新
- [x] 既存機能に影響なし
```

---

## コード規約

### JavaScript

#### 命名規則
```javascript
// 変数・関数: キャメルケース
let templateDisplay = document.getElementById('template-display');
function lookupTemplate(code) { ... }

// 定数: 大文字スネークケース
const CACHE_NAME = 'virtual-keyboard-v0.50';
const MAX_ZOOM = 2.0;

// クラス: パスカルケース（将来使用時）
class TemplateManager { ... }
```

#### コメント
```javascript
// 単一行コメント: 処理の説明
display.textContent += keyValue;

/* 複数行コメント:
   複雑なロジック、
   セクション区切り */
```

#### 関数
```javascript
// 関数宣言推奨
function saveCustomTemplates(templates) {
    localStorage.setItem('customTemplates', JSON.stringify(templates));
}

// アロー関数（コールバック時）
document.querySelectorAll('.key').forEach(key => {
    key.addEventListener('click', handleKeyClick);
});
```

#### 非同期処理
```javascript
// async/await推奨
async function readClipboard() {
    try {
        const text = await navigator.clipboard.readText();
        return text;
    } catch (err) {
        console.error('クリップボード読み取り失敗:', err);
        throw err;
    }
}
```

### HTML

#### インデント
- 4スペース

#### 属性順序
```html
<!-- 1. id, 2. class, 3. data-*, 4. その他 -->
<button id="copyBtn" class="key function-key" data-action="copy">
    コピー
</button>
```

### CSS

#### 命名規則
- ケバブケース

```css
/* 良い例 */
.template-display { ... }
.input-display { ... }

/* 悪い例 */
.templateDisplay { ... }
.InputDisplay { ... }
```

#### セレクタ順序
1. 要素セレクタ
2. クラスセレクタ
3. IDセレクタ
4. 疑似クラス

```css
body { ... }
.keyboard { ... }
#display { ... }
.key:hover { ... }
```

---

## コミットメッセージ規約

### フォーマット

```
<type>: <subject>

<body>

<footer>
```

### Type一覧

| Type | 説明 | 例 |
|------|------|-----|
| feat | 新機能 | `feat: テンプレートエクスポート機能追加` |
| fix | バグ修正 | `fix: コピーボタンの動作修正` |
| docs | ドキュメント | `docs: READMEにFAQ追加` |
| style | コード整形 | `style: インデント統一` |
| refactor | リファクタリング | `refactor: lookupTemplate関数を最適化` |
| perf | パフォーマンス改善 | `perf: テンプレート検索を高速化` |
| test | テスト追加 | `test: カスタムテンプレート登録テスト追加` |
| chore | ビルド・設定 | `chore: バージョンv0.51に更新` |

### 例

```bash
# 良い例
git commit -m "feat: ズーム機能追加（50%-200%）"
git commit -m "fix: Safari環境でのクリップボード動作修正"
git commit -m "docs: CONTRIBUTING.mdを追加"

# 悪い例
git commit -m "update"
git commit -m "fix bug"
git commit -m "いろいろ修正"
```

---

## プルリクエスト

### レビュープロセス

1. **自動チェック**: （CI/CD未導入のため手動）
2. **コードレビュー**: メンテナーが1-3日以内にレビュー
3. **修正対応**: 指摘事項に対応
4. **承認**: メンテナーがApprove
5. **マージ**: メンテナーがマージ実行

### レビュー観点

- [ ] バージョン更新されているか
- [ ] sw.jsのキャッシュ名更新されているか
- [ ] 既存機能に影響がないか
- [ ] コード規約に準拠しているか
- [ ] ドキュメント更新されているか

---

## テスト

### 手動テスト手順

#### 1. 基本機能テスト

```bash
# ローカルサーバー起動
python -m http.server 3000

# テスト項目
1. キーボードで「req1」入力 → テンプレート表示確認
2. テンプレートエリアクリック → コピー完了確認
3. 📝登録ボタン → カスタムテンプレート登録確認
4. 📋一覧ボタン → 一覧表示・編集・削除確認
5. ズーム±ボタン → 拡大縮小確認
```

#### 2. PWAテスト

```bash
# Service Worker確認
1. Chrome DevTools > Application > Service Workers
2. オフライン化（Network: Offline）
3. ページ再読み込み → 動作確認
```

#### 3. レスポンシブテスト

```bash
# Chrome DevTools > Device Toolbar
1. iPhone SE (375x667)
2. iPad (768x1024)
3. Desktop (1920x1080)
```

---

## リリースプロセス

### 1. バージョン決定

- **パッチ**: v0.50 → v0.51（バグ修正、小機能）
- **マイナー**: v0.50 → v1.00（大機能追加）
- **メジャー**: v1.00 → v2.00（破壊的変更）

### 2. 変更履歴更新

```markdown
## v0.51 (2025-10-06)
- ✨ テンプレートエクスポート機能追加
- 🐛 Safari環境でのコピー機能修正
- 📝 CONTRIBUTING.md追加
```

### 3. タグ作成

```bash
git tag -a v0.51 -m "Release v0.51"
git push origin v0.51
```

### 4. GitHub Pagesデプロイ

```bash
# mainブランチへのマージで自動デプロイ
git checkout main
git merge feature/export-templates
git push origin main
```

---

## FAQ

### Q. 外部ライブラリを追加できますか？
A. 原則不可。Vanilla JS原則を維持してください。

### Q. index.html以外のファイルを作成できますか？
A. ドキュメント（`docs/`配下）のみ可。

### Q. CSS/JSを分離できますか？
A. 現在は不可。将来的にv1.00以降で検討予定。

### Q. テストコードは必要ですか？
A. 現在は手動テストのみ。将来的に自動テスト導入予定。

---

## サポート

質問・相談は以下へ:
- GitHub Issues: https://github.com/muumuu8181/virtual-keyboard/issues
- Email: （設定されていない場合は追記）

---

**貢献ありがとうございます！**

Created with Claude Code 🤖
