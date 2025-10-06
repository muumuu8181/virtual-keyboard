# アーキテクチャ設計書

**プロジェクト**: テンプレート機能付き仮想キーボード
**最終更新**: 2025-10-05

---

## 目次

- [システム概要](#システム概要)
- [アーキテクチャパターン](#アーキテクチャパターン)
- [コンポーネント設計](#コンポーネント設計)
- [データモデル](#データモデル)
- [API設計](#api設計)
- [セキュリティ](#セキュリティ)
- [パフォーマンス最適化](#パフォーマンス最適化)
- [拡張性](#拡張性)

---

## システム概要

### アーキテクチャ原則

1. **単一ファイル原則**: 全機能を`index.html`に集約
2. **ゼロ依存**: 外部ライブラリ不使用（Vanilla JS）
3. **PWAファースト**: オフライン優先設計
4. **レスポンシブ**: モバイル・デスクトップ両対応

### 技術スタック

```
┌─────────────────────────────────────┐
│  index.html (Single Page App)      │
├─────────────────────────────────────┤
│  HTML5 構造層                       │
│  CSS3 表現層（グリッドレイアウト）  │
│  JavaScript ES6+ 振る舞い層         │
└─────────────────────────────────────┘
         ↓          ↓         ↓
    ┌────────┐ ┌─────────┐ ┌──────┐
    │Storage │ │Clipboard│ │ PWA  │
    │  API   │ │   API   │ │Cache │
    └────────┘ └─────────┘ └──────┘
```

---

## アーキテクチャパターン

### MVCパターン（簡易版）

#### Model（データ層）
```javascript
// デフォルトテンプレート（静的）
const defaultTemplates = {
    'req1': '...',
    'rev1': '...',
    ...
};

// カスタムテンプレート（動的、localStorage）
let customTemplates = loadCustomTemplates();

// メモ（動的、localStorage）
let memos = loadMemos();
```

#### View（表示層）
```html
<!-- 入力表示 -->
<div id="display" class="input-display"></div>

<!-- テンプレート表示 -->
<div id="template-container">
    <div id="template-display"></div>
    <div id="copy-status"></div>
</div>

<!-- キーボードUI -->
<div class="keyboard">...</div>
```

#### Controller（制御層）
```javascript
// イベントハンドラー
document.querySelectorAll('.key').forEach(key => {
    key.addEventListener('click', handleKeyClick);
});

// ビジネスロジック
function lookupTemplate(code) { ... }
function saveCustomTemplates(data) { ... }
```

---

## コンポーネント設計

### 1. キーボードコンポーネント

#### 責務
- 文字入力受付（画面キーボード・物理キーボード）
- 入力内容の表示更新

#### 実装
```html
<div class="keyboard-panel left-keyboard">
    <div class="keyboard-row">
        <button class="key" data-key="Q">Q</button>
        ...
    </div>
</div>
```

```javascript
function handleKeyClick(event) {
    const keyValue = this.getAttribute('data-key');
    display.textContent += keyValue;

    // 4文字以上でリアルタイム検索
    if (display.textContent.length >= 4) {
        lookupTemplate(display.textContent);
    }
}
```

### 2. テンプレート管理コンポーネント

#### 責務
- テンプレート検索
- カスタムテンプレートCRUD
- テンプレート一覧表示

#### データフロー
```
[入力] → lookupTemplate(code)
    ↓
getAllTemplates() // defaultTemplates + customTemplates
    ↓
template = allTemplates[code.toLowerCase()]
    ↓
templateContent.textContent = template
    ↓
templateContainer.style.display = 'flex'
```

### 3. クリップボードコンポーネント

#### 責務
- テンプレートコピー
- クリップボード読み取り（カスタムテンプレート登録時）

#### 実装
```javascript
// コピー（Primary）
navigator.clipboard.writeText(text)
    .then(() => { /* 成功処理 */ })
    .catch(() => { /* フォールバック */ });

// フォールバック（execCommand）
const textArea = document.createElement('textarea');
textArea.value = text;
document.body.appendChild(textArea);
textArea.select();
document.execCommand('copy');
document.body.removeChild(textArea);
```

### 4. ストレージコンポーネント

#### 責務
- localStorage読み書き
- データシリアライズ/デシリアライズ

#### データ構造
```javascript
// customTemplates
{
    "code1": "template text 1",
    "code2": "template text 2"
}

// memos
[
    {
        "id": 1633024800000,
        "text": "memo content",
        "created": "2025/10/05 12:00:00"
    }
]

// appZoom
"1.2" // 文字列型（0.5-2.0）
```

### 5. PWAコンポーネント

#### Service Worker（sw.js）
```javascript
// インストール時: キャッシュ作成
self.addEventListener('install', event => {
    event.waitUntil(
        caches.open(CACHE_NAME).then(cache => {
            return cache.addAll(urlsToCache);
        })
    );
});

// フェッチ時: キャッシュ優先
self.addEventListener('fetch', event => {
    event.respondWith(
        caches.match(event.request)
            .then(response => response || fetch(event.request))
    );
});

// アクティベート時: 古いキャッシュ削除
self.addEventListener('activate', event => {
    event.waitUntil(
        caches.keys().then(cacheNames => {
            return Promise.all(
                cacheNames.map(cacheName => {
                    if (cacheName !== CACHE_NAME) {
                        return caches.delete(cacheName);
                    }
                })
            );
        })
    );
});
```

---

## データモデル

### テンプレートモデル

```typescript
// 型定義（参考）
interface Template {
    code: string;           // 3-4文字の識別子
    text: string;           // テンプレート本文
    category?: string;      // REQ/REV/CHK/OTH
    isCustom: boolean;      // カスタム判定
    createdAt?: Date;       // 作成日時（カスタムのみ）
}
```

### メモモデル

```typescript
interface Memo {
    id: number;            // タイムスタンプベースID
    text: string;          // メモ本文
    created: string;       // 日時文字列（日本時間）
}
```

### ズーム設定モデル

```typescript
interface ZoomConfig {
    scale: number;         // 0.5-2.0
}
```

---

## API設計

### localStorage API（内部API）

#### 1. テンプレート操作

```javascript
// 読み込み
function loadCustomTemplates(): Object {
    const saved = localStorage.getItem('customTemplates');
    return saved ? JSON.parse(saved) : {};
}

// 保存
function saveCustomTemplates(templates: Object): void {
    localStorage.setItem('customTemplates', JSON.stringify(templates));
}

// 全テンプレート取得
function getAllTemplates(): Object {
    return { ...defaultTemplates, ...customTemplates };
}
```

#### 2. メモ操作

```javascript
// 読み込み
function loadMemos(): Array<Memo> {
    const saved = localStorage.getItem('memos');
    return saved ? JSON.parse(saved) : [];
}

// 保存
function saveMemos(memos: Array<Memo>): void {
    localStorage.setItem('memos', JSON.stringify(memos));
}
```

#### 3. ズーム設定

```javascript
// 読み込み
const savedZoom = localStorage.getItem('appZoom');
if (savedZoom) {
    currentZoom = parseFloat(savedZoom);
}

// 保存
function updateZoom(): void {
    localStorage.setItem('appZoom', currentZoom);
}
```

### Clipboard API

```javascript
// 読み取り
async function readClipboard(): Promise<string> {
    try {
        return await navigator.clipboard.readText();
    } catch (err) {
        throw new Error('クリップボード読み取り失敗');
    }
}

// 書き込み
async function writeClipboard(text: string): Promise<void> {
    try {
        await navigator.clipboard.writeText(text);
    } catch (err) {
        // フォールバック処理
        execCommandCopy(text);
    }
}
```

---

## セキュリティ

### 脅威モデル

| 脅威 | 対策 | 実装状況 |
|------|------|----------|
| XSS | `textContent`使用（`innerHTML`回避） | ✅ 実装済み |
| CSRF | 該当なし（ステートレス、外部通信なし） | N/A |
| データ漏えい | localStorageのみ（サーバー送信なし） | ✅ 実装済み |
| クリックジャッキング | `X-Frame-Options`（GitHub Pages自動設定） | ✅ 実装済み |

### XSS対策

```javascript
// 安全な実装
templateContent.textContent = template; // ✅

// 危険な実装（使用禁止）
templateContent.innerHTML = template;   // ❌
```

### データ永続化のセキュリティ

- **localStorage**: 同一オリジン限定（他サイトからアクセス不可）
- **HTTPS**: GitHub Pages強制（中間者攻撃対策）

---

## パフォーマンス最適化

### レンダリング最適化

#### 1. リアルタイム検索の遅延
```javascript
// 4文字以上で自動検索（過剰な検索を防止）
if (currentInput.length >= 4) {
    lookupTemplate(currentInput);
}
```

#### 2. イベント委譲
```javascript
// 個別リスナー回避
document.querySelectorAll('.key').forEach(key => {
    key.addEventListener('click', handleKeyClick);
});
```

### ストレージ最適化

#### localStorageアクセス最小化
```javascript
// 起動時に一度だけ読み込み
let customTemplates = loadCustomTemplates();

// メモリ上で操作
customTemplates[code] = text;

// 変更時のみ保存
saveCustomTemplates(customTemplates);
```

### PWAキャッシュ戦略

```javascript
// Cache-First戦略
self.addEventListener('fetch', event => {
    event.respondWith(
        caches.match(event.request)
            .then(response => {
                // キャッシュヒット: 即座に返却
                if (response) return response;

                // キャッシュミス: ネットワーク取得
                return fetch(event.request);
            })
    );
});
```

---

## 拡張性

### 将来の拡張ポイント

#### 1. ファイル分離（スケーラビリティ向上）

**現状**: 単一ファイル（1165行）
**将来**:
```
virtual-keyboard/
├── index.html
├── styles/
│   ├── main.css
│   ├── keyboard.css
│   └── templates.css
├── scripts/
│   ├── app.js
│   ├── templates.js
│   ├── storage.js
│   └── clipboard.js
└── data/
    └── default-templates.json
```

#### 2. テンプレートエクスポート/インポート

```javascript
// エクスポート
function exportTemplates() {
    const data = {
        version: '1.0',
        templates: customTemplates,
        memos: memos,
        exportedAt: new Date().toISOString()
    };

    const blob = new Blob([JSON.stringify(data, null, 2)], { type: 'application/json' });
    const url = URL.createObjectURL(blob);

    const a = document.createElement('a');
    a.href = url;
    a.download = 'templates-backup.json';
    a.click();
}

// インポート
function importTemplates(file) {
    const reader = new FileReader();
    reader.onload = (e) => {
        const data = JSON.parse(e.target.result);
        customTemplates = { ...customTemplates, ...data.templates };
        saveCustomTemplates(customTemplates);
    };
    reader.readAsText(file);
}
```

#### 3. クラウド同期

```javascript
// Firebase Realtime Database統合例
function syncToCloud(userId) {
    const db = firebase.database();
    db.ref(`users/${userId}/templates`).set(customTemplates);
}

function syncFromCloud(userId) {
    const db = firebase.database();
    db.ref(`users/${userId}/templates`).once('value', (snapshot) => {
        customTemplates = snapshot.val() || {};
        saveCustomTemplates(customTemplates);
    });
}
```

#### 4. テンプレートカテゴリ拡張

```javascript
// 新規カテゴリ追加
const defaultTemplates = {
    ...
    // DOC系列 - ドキュメント
    'doc1': 'API仕様書テンプレート',
    'doc2': 'ユーザーガイドテンプレート',

    // BUG系列 - バグ報告
    'bug1': 'バグ報告書テンプレート',
    'bug2': 'インシデントレポートテンプレート'
};
```

#### 5. 多言語対応（i18n）

```javascript
const i18n = {
    ja: {
        copyButton: 'コピー',
        copySuccess: 'コピー完了！',
        registerButton: '📝登録'
    },
    en: {
        copyButton: 'Copy',
        copySuccess: 'Copied!',
        registerButton: '📝Register'
    }
};

// 言語切替
function setLanguage(lang) {
    document.getElementById('copyButton').textContent = i18n[lang].copyButton;
}
```

---

## 非機能要件

### 可用性
- **稼働率**: 99.9%（GitHub Pages SLA依存）
- **オフライン動作**: Service Workerによる完全オフライン対応

### スケーラビリティ
- **同時利用者数**: 制限なし（クライアントサイド完結）
- **データ容量**: localStorage上限（5-10MB）

### 保守性
- **コード行数**: 1165行（許容範囲: 2000行未満）
- **依存関係**: ゼロ（外部ライブラリなし）

### 互換性
- **ブラウザ**: Chrome 80+, Firefox 75+, Safari 13+, Edge 80+
- **OS**: Windows, macOS, Linux, Android, iOS

---

**作成者**: Claude Code
**ドキュメントバージョン**: 1.0
