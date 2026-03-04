# シフトSOS（ShiftSOS）

**バイトの代わりが LINEですぐ見つかるシフト管理ツール**

---

## 📋 プロジェクト概要

シフトSOS は、飲食店・小売店などアルバイトスタッフを多く抱える職場向けの LINE 連携シフト管理サービスです。急な欠員連絡・代行募集・承認をすべて LINE 上で自動化します。

**サービス URL:** https://shiftsos2.web.app/

### 主な機能
| 機能 | 概要 |
|------|------|
| SOS 申請 | スタッフが日付・理由を入力し代行募集を一斉送信 |
| 早い者勝ち / 承認制 | 管理者が決定フローを選択可能 |
| 優先通知 | 優先順位リストを設定し、段階的に通知を拡大 |
| 利用規約同意フロー | Firestore から最新版を取得してモーダル表示 |
| インタラクティブデモ | LP 内でアプリ操作を疑似体験できる |

---

## 🏗️ ファイル構成

```
shiftSOS/             ← このリポジトリ（LP のみ）
└── index.html        ← すべてのコード（HTML / CSS / JS）が 1 ファイルに集約
```

> **注意:** バックエンド（LINE Bot・Cloud Functions・Firestore ルール等）は別リポジトリ `shift-backend` で管理されています。

---

## 🔧 技術スタック

| 種別 | ライブラリ / サービス | 用途 |
|------|--------------------|------|
| CSS フレームワーク | [Tailwind CSS v3 (CDN)](https://tailwindcss.com/) | スタイリング全般 |
| アイコン | [Lucide](https://lucide.dev/) | UI アイコン |
| フォント | Google Fonts (Noto Sans JP / Noto Serif JP) | 日本語フォント |
| BaaS | Firebase (Firestore / Auth) | 利用規約データ取得・匿名認証 |
| ホスティング | Firebase Hosting | 静的ファイル配信 |

---

## ⚙️ Firebase 設定

`index.html` の約 688 行目に Firebase の設定値が埋め込まれています。

```js
const firebaseConfig = {
    apiKey: "AIzaSy...",
    authDomain: "shiftsos2.firebaseapp.com",
    projectId: "shiftsos2",
    storageBucket: "shiftsos2.firebasestorage.app",
    messagingSenderId: "323780246412",
    appId: "1:323780246412:web:..."
};
```

> 設定値は Firebase コンソール → **プロジェクトの設定 → 全般 → マイアプリ** から確認できます。

### Firestore コレクション構造

| コレクション | ドキュメント ID 形式 | 主なフィールド | 用途 |
|-------------|-------------------|---------------|------|
| `terms_versions` | `YYYY-M-D`（例: `2025-1-1`） | `terms`: HTML 文字列 | 利用規約 |
| `privacy_versions` | `YYYY-M-D` | `privacy`: HTML 文字列 | プライバシーポリシー |

最新ドキュメントを自動で選択して表示します（日付降順ソート）。

---

## 📄 index.html の構成

ファイルは大きく以下のブロックに分かれています。

### HTML セクション（表示コンテンツ）

| 行番号 (目安) | セクション | 概要 |
|-------------|-----------|------|
| L1–138 | `<head>` | 外部ライブラリ読み込み・Tailwind 設定・カスタム CSS |
| L146–177 | Header | ナビゲーション・ベータ告知バー |
| L179–227 | Hero | メインビジュアル・CTA ボタン |
| L229–275 | Problem | 課題提示セクション |
| L277–357 | Solution | 解決策 3 カード |
| L359–455 | Features | 機能詳細（管理者目線） |
| L457–485 | Target | ターゲット業態 |
| L487–523 | CTA | LINE 友だち追加 CTA |
| L526–542 | Footer | フッター・法的リンク |
| L544–651 | Modals（HTML） | 運営者情報・お問い合わせ・利用規約モーダル |
| L653–678 | Demo Modal（HTML） | アプリデモのスマホフレーム |

### JavaScript セクション

| 行番号 (目安) | スクリプト | 概要 |
|-------------|-----------|------|
| L681–821 | Firebase モジュール | 匿名認証・Firestore から規約取得・モーダル表示 |
| L823–873 | ユーティリティ | Lucide アイコン初期化・IntersectionObserver・スムーススクロール |
| L874–964 | モーダル制御 | 運営者情報・お問い合わせ・デモモーダルの開閉 |
| L966–1321 | `renderScreen()` | デモアプリの各画面レンダリング（SPA ライク） |
| L1324–1358 | `setupNav()` | デモのボトムナビ生成（スタッフ / 管理者で切替） |
| L1360–1402 | ユーティリティ関数 | `switchTab` / `loginAs` / `togglePriorityList` |

---

## 🚀 ローカル確認方法

このファイルは静的な HTML のため、`index.html` をブラウザで直接開くだけで確認できます。  
ただし **Firebase (Firestore) の読み込みは本番キーに接続**されるため、利用規約モーダルを開くと実データが表示されます。

```bash
# シンプルなローカルサーバーを使う場合（Python）
python -m http.server 8000
```

---

## 📦 デプロイ手順

Firebase Hosting を使っています。

```bash
# Firebase CLI がない場合は先にインストール
npm install -g firebase-tools

# ログイン
firebase login

# デプロイ（shiftSOS フォルダで実行）
firebase deploy --only hosting
```

> `firebase.json` と `.firebaserc` はバックエンドリポジトリ側で管理されています。  
> LP のみデプロイする場合はバックエンドリポジトリのルートから実行してください。

---

## 🔗 関連リソース

| リソース | 説明 |
|---------|------|
| [Firebase Console](https://console.firebase.google.com/project/shiftsos2) | Firestore・認証・Hosting の管理 |
| [LINE Developers](https://developers.line.biz/) | LINE Bot / LIFF 設定 |
| [お問い合わせフォーム](https://forms.gle/iZLFqaA6EKMti2Ep8) | サイト内フォームの実体（Google Forms） |
| [LINE 公式アカウント](https://lin.ee/n5DBNC0) | ユーザー向け友だち追加 URL |

---

## 👨‍💻 開発者

- **代表・開発者:** 真鍋優成
- **開発動機:** 店舗責任者としての経験から、シフト調整の非効率を解消するために開発