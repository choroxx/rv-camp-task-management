# 合宿準備ダッシュボード（モックアップ）

見た目確認用のプロトタイプです。`index.html` を開くだけで動きます（外部サーバー不要）。

## GitHubで公開・共同編集する手順

1. GitHubで新しいリポジトリを作成する（例: `gasshuku-prep-mockup`）
2. このフォルダの中身（`index.html`）をそのままリポジトリに追加してコミット・プッシュする
   ```
   git init
   git add index.html README.md
   git commit -m "初期モックアップ"
   git remote add origin https://github.com/【あなたのアカウント】/gasshuku-prep-mockup.git
   git push -u origin main
   ```
3. GitHub上でリポジトリの `Settings > Pages` を開き、`Deploy from a branch` → `main` を選択して保存する
   → 数分後に `https://【あなたのアカウント】.github.io/gasshuku-prep-mockup/` でブラウザから誰でも見られるURLが発行される（これは「見るだけ」用の共有リンク）
4. 編集してほしいメンバーを `Settings > Collaborators` から招待する
   - Gitに慣れている人 → 通常どおり clone してブランチを切って Pull Request
   - Gitに慣れていない人 → GitHub上でリポジトリを開いた状態でキーボードの `.`（ピリオド）を押すと、ブラウザだけで動くコードエディタ（github.dev）が開き、ローカルにcloneせずに直接編集・コミットできる

## コードを触らずに動きだけ確認したい人向け

`index.html` をダブルクリックすればブラウザで開けます。ローカルで開いている間だけ動作し、リロードすると入力内容は消えます（これは仕様で、実データを貯めるにはバックエンドが別途必要です）。
