# バージョン管理ガイド

## 🎯 重要原則: **Single Source of Truth**

バージョン変更は**1箇所のみ**で完結します。

---

## 📝 バージョン更新手順

### ステップ1（唯一の変更箇所）: index.htmlを更新

**ファイル**: `index.html`
**行番号**: 17行目

```css
:root {
    --app-version: "v0.XX";  /* ★ここだけ変更すればOK */
    --scale: 1;
}
```

### 完了！

この1箇所を変更するだけで、以下が**自動的に**更新されます:
- ✅ アプリのバージョン表示（画面右上）
- ✅ Service Workerのキャッシュ名
- ✅ PWAのバージョン管理
- ✅ コンソールログのバージョン表示

### 仕組み

1. **CSS変数に定義** (index.html 17行目)
   ```css
   --app-version: "v0.53";
   ```

2. **JavaScriptで自動取得** (index.html 682行目)
   ```javascript
   const APP_VERSION = getComputedStyle(document.documentElement)
       .getPropertyValue('--app-version').trim().replace(/['"]/g, '');
   ```

3. **Service Workerに渡す** (index.html 688行目)
   ```javascript
   navigator.serviceWorker.register(`./sw.js?version=${APP_VERSION}`)
   ```

4. **Service Workerで受け取る** (sw.js 2-3行目)
   ```javascript
   const urlParams = new URLSearchParams(self.location.search);
   const APP_VERSION = urlParams.get('version') || 'v0.53';
   ```

---

## ❌ やってはいけないこと

### 禁止事項1: ドキュメントに直接バージョン記載

**悪い例** ❌:
```markdown
# README.md
**バージョン**: v0.52
```

**良い例** ✅:
```markdown
# README.md
**バージョン**: 最新版は画面右上を確認
```

または、バージョンバッジで自動取得:
```markdown
[![Version](https://img.shields.io/badge/version-dynamic-green)]()
```

### 禁止事項2: 複数ファイルに同じバージョンをハードコード

**悪い例** ❌:
- `index.html`: v0.52
- `sw.js`: v0.52
- `README.md`: v0.52
- `ARCHITECTURE.md`: v0.52
- `CONTRIBUTING.md`: v0.52
- `mobile-installation-guide.md`: v0.52

→ **6箇所も変更が必要！**

**良い例** ✅:
- `index.html`: v0.53（CSS変数） ← **ここだけ変更**
- `sw.js`: 自動取得（変更不要）
- その他のドキュメント: バージョン記載なし、または動的参照

→ **1箇所だけ変更！**

---

## 🔧 現在のバージョン管理方式

### アプリケーション層

#### index.html（17行目） - **唯一の変更箇所**
```css
:root {
    --app-version: "v0.53";  /* ★ここだけ変更 */
}
```

**使用箇所**:
- 画面右上のバージョン表示（`.version-display::after`で自動取得）
- JavaScript内で自動取得して各所に伝播

#### sw.js（2-3行目） - **自動取得（変更不要）**
```javascript
const urlParams = new URLSearchParams(self.location.search);
const APP_VERSION = urlParams.get('version') || 'v0.53';
const CACHE_NAME = `virtual-keyboard-${APP_VERSION}`;
```

**使用箇所**:
- Service Workerのキャッシュ名（index.htmlから自動取得）
- PWAのバージョン管理

---

## 📊 ドキュメント層のベストプラクティス

### README.md

**現在の方式** ❌ (改善前):
```markdown
[![Version](https://img.shields.io/badge/version-v0.51-green)]
```

**推奨方式** ✅ (改善後):
```markdown
[![Version](https://img.shields.io/badge/version-latest-green)]
または
バージョン: アプリ画面右上を参照
```

### ARCHITECTURE.md / CONTRIBUTING.md

**現在の方式** ❌ (改善前):
```markdown
**バージョン**: v0.52
```

**推奨方式** ✅ (改善後):
```markdown
**バージョン**: 最新版（index.html参照）
または
バージョン記載を削除
```

### mobile-installation-guide.md

**現在の方式** ❌ (改善前):
```markdown
- **バージョン**: v0.52
```

**推奨方式** ✅ (改善後):
```markdown
- **バージョン**: 最新版
または
バージョン記載を削除
```

---

## 🚀 将来の改善案

### 案1: package.jsonで一元管理

```json
{
  "name": "virtual-keyboard",
  "version": "0.52.0"
}
```

ビルドスクリプトで各ファイルに自動注入:
```bash
npm run build  # package.jsonからバージョン取得してindex.html/sw.jsを更新
```

### 案2: バージョン専用ファイル

```javascript
// version.js
export const APP_VERSION = 'v0.52';
```

各ファイルでインポート:
```javascript
// index.html
import { APP_VERSION } from './version.js';

// sw.js
importScripts('./version.js');
```

### 案3: GitHub Actionsで自動更新

```yaml
# .github/workflows/version-update.yml
- name: Update version
  run: |
    VERSION=$(grep -oP 'v\d+\.\d+' index.html | head -1)
    sed -i "s/v[0-9]\+\.[0-9]\+/$VERSION/g" sw.js
```

---

## 📌 現時点での運用ルール

### v0.53時点の運用（確定版）

1. **バージョン変更時**: `index.html`（17行目）**のみ**変更
2. **ドキュメント**: バージョン記載は削除or「最新版」と記載
3. **バージョンバッジ**: `latest`または動的取得に変更（将来対応）

### 次回バージョンアップ時の確認項目

- [ ] index.html 17行目のバージョン更新（**これだけでOK**）
- [ ] ブラウザで画面右上のバージョン表示を確認
- [ ] コンソールログで`App Version: vX.XX`を確認
- [ ] Service Worker更新確認（DevTools > Application > Service Workers）
- [ ] キャッシュ名が`virtual-keyboard-vX.XX`になっているか確認

---

## 🎓 まとめ

### 原則
> **バージョンは1箇所で管理、他は自動取得or記載しない**

### メリット
- ✅ 変更箇所が**1箇所のみ**（index.html 17行目）
- ✅ 更新漏れが**絶対にない**
- ✅ ドキュメントと実態の乖離を防止
- ✅ メンテナンスコストが**極小**
- ✅ ヒューマンエラーを防止

### 将来的な完全自動化
- package.json + ビルドスクリプト
- または、単一定数ファイル + 動的インポート

---

**最終更新**: 2025-10-05（v0.53対応 - 完全1箇所変更実現）
**Created with Claude Code** 🤖
