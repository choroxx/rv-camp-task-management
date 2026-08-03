# 合宿コントロールルーム

合宿イベントの準備タスクと進捗を管理するダッシュボードです。GitHub Pagesで公開し、Supabaseをバックエンドとして利用しています。

## 概要

タスクの登録・進捗管理に加えて、Slack上でのやり取り（日報・連絡・議事録）を自動でタスク化し、AIによる評価コメントを付与する仕組みまで含んだ本番運用アプリです。

## 主な機能

- タスク管理・進捗ダッシュボード（フェーズ別の進捗表示）
- Slack連携（RV日報Bot）：日報スレッドの返信まで含めて進捗を自動同期
- `/renraku` スラッシュコマンドによる折衝・議事録ログの記録（種別ごとに色分け表示）
- Slackメッセージから抽出したアクションアイテムを担当者付きで自動タスク化（重複チェックあり）
- AIによる進捗コメント（特定の著名人風キャラクターの評価コメントをClaude Haikuで生成）
- 使い方マニュアル（`外部折衝ログ_使い方マニュアル.html`、スクリーンショット埋め込み）

## 技術構成

| 区分 | 内容 |
|---|---|
| フロントエンド | HTML / CSS / JS、GitHub Pagesで公開 |
| バックエンド | Supabase（東京リージョン）：メインプロジェクト（タスク・フェーズ・エントリ・ユーザー・Slackメンバー）＋外部ログ用プロジェクト（折衝・議事録ログ） |
| Edge Functions | Deno（`evaluate-entry`、`sync-slack-progress` など） |
| スケジューリング | pg_cron / pg_net |
| 外部連携 | Slack API（RV日報Bot）、Anthropic API（Claude Haiku） |

## セットアップ

1. 本リポジトリをclone
   ```
   git clone https://github.com/choroxx/rv-camp-task-management.git
   ```
2. Supabaseプロジェクトを2つ用意（メイン／外部ログ用）し、テーブルとEdge Functionsをデプロイ
3. 各Edge Functionsに必要な環境変数を設定
   - Supabase URL / anon key（メイン・外部ログ両方）
   - Slack Bot Token（`rv-nippou-bot`）
   - Anthropic APIキー
   - スケジュール系Functionはトリガーからの呼び出しのためJWT検証を無効化
4. Slack側で `/renraku` スラッシュコマンドとイベントAPIのWebhook送信先をこのリポジトリのEdge Functions URLに設定

## 公開・共同編集

1. GitHubで対象リポジトリを開き、`Settings > Pages` で `Deploy from a branch` → `main` を選択
   → 数分後に `https://choroxx.github.io/rv-camp-task-management/` で閲覧可能
2. 編集メンバーは `Settings > Collaborators` から招待
   - Gitに慣れている人：通常どおりclone → ブランチ → Pull Request
   - Gitに慣れていない人：リポジトリを開いた状態で `.`（ピリオド）を押すとgithub.devが開き、ブラウザだけで編集・コミット可能

## 既知の制約

- スキーマ変更後はPostgRESTのキャッシュが古いままになることがあるため、`NOTIFY pgrst, 'reload schema'` を実行する
- SupabaseダッシュボードのWebhook UIには既知の不具合があるため、`pg_net` を使ったデータベーストリガー経由の呼び出しを利用している
- Slack同期は「直近N日」ではなく `latest_reply` のタイムスタンプで全履歴をフィルタする方式（古いスレッドへの新規返信を取りこぼさないため）


## 関係者

| 役割 | 担当 |
|---|---|
| 承認・合宿リーダー | （非公開） |
| 主催者 | （非公開） |
