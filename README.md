# サンプル管理システム セットアップ手順

## 1. Firebaseプロジェクト設定

### 1.1 Firebaseコンソールにアクセス
1. https://console.firebase.google.com/ にアクセス
2. 「プロジェクトを追加」をクリック
3. プロジェクト名を入力（例: `sample-manager`）
4. Googleアナリティクスは無効でOK

### 1.2 Authentication（認証）の設定
1. 左メニュー「Authentication」→「始める」
2. 「Sign-in method」タブ
3. 「Google」を選択して「有効にする」
4. プロジェクトのサポートメールを選択して保存

### 1.3 Firestore Database の設定
1. 左メニュー「Firestore Database」→「データベースを作成」
2. 「本番モード」を選択
3. ロケーション: `asia-northeast1`（東京）を選択
4. 作成後、「ルール」タブで以下に変更:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /{document=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

### 1.4 Storage の設定
1. 左メニュー「Storage」→「始める」
2. 「本番モード」を選択
3. ロケーション: `asia-northeast1`
4. 作成後、「Rules」タブで以下に変更:

```
rules_version = '2';
service firebase.storage {
  match /b/{bucket}/o {
    match /{allPaths=**} {
      allow read, write: if request.auth != null;
    }
  }
}
```

### 1.5 Webアプリの登録
1. プロジェクト概要（歯車アイコン）→「プロジェクトの設定」
2. 「マイアプリ」セクション下の「</>」（Web）アイコンをクリック
3. アプリのニックネームを入力（例: `sample-manager-web`）
4. 「アプリを登録」をクリック
5. 表示される `firebaseConfig` をコピー

## 2. アプリの設定

### 2.1 Firebase設定を入力
`index.html` を開き、以下の部分を自分のプロジェクトの値に置き換え:

```javascript
const firebaseConfig = {
    apiKey: "ここにapiKey",
    authDomain: "your-project.firebaseapp.com",
    projectId: "your-project",
    storageBucket: "your-project.appspot.com",
    messagingSenderId: "123456789",
    appId: "1:123456789:web:abcdef"
};
```

## 3. 公開（ホスティング）

### 方法A: Firebase Hosting（推奨）
1. Firebase CLI をインストール: `npm install -g firebase-tools`
2. ログイン: `firebase login`
3. 初期化: `firebase init hosting`
4. デプロイ: `firebase deploy`

### 方法B: ローカルで使用
- `index.html` をブラウザで直接開く（Chrome推奨）
- ただし一部機能（画像アップロード）が制限される場合あり

### 方法C: GitHub Pages
1. GitHubリポジトリを作成
2. `index.html` をプッシュ
3. Settings → Pages で公開

## 4. 使い方

### 初回ログイン
- 最初にログインしたユーザーが自動的に管理者になります
- 管理者は「設定」タブで他のユーザーを追加できます

### サンプルインポート
1. 「データ取込」タブを開く
2. 都医学研のExcelファイルをドラッグ＆ドロップ
3. プレビューを確認して「インポート実行」

### 使用記録
1. サンプル一覧で「使用」ボタンをクリック
2. 検体種類、使用量、用途を入力
3. 「記録する」で保存
4. 残量は自動計算されます

### 画像の紐付け
1. サンプル詳細を開く（No.をクリック）
2. 「画像を追加」ボタン
3. 紙のアンケート写真などをアップロード

## トラブルシューティング

### 「アクセスが許可されていません」と表示される
- 管理者に連絡して、設定タブでメールアドレスを追加してもらってください

### インポートできない
- Excelファイルの形式を確認（.xlsx形式推奨）
- 「Sample」シートがあることを確認

### 画像がアップロードできない
- Firebase Storage が正しく設定されているか確認
- ファイルサイズが10MB以下か確認
