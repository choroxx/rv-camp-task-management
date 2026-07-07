# RV合宿 — 公式LINE取得・デザイン反映・URL差し替えガイド
（作成アカウント：**art.crea01@gmail.com**（仮・個人）で統一。本番移行手順も併記）

---

## STEP 1：公式LINEアカウントの取得（約30分・無料）

### 1-1. LINEビジネスIDの作成
1. https://account.line.biz/ を開く
2. 「メールアドレスで登録」→ **art.crea01@gmail.com** を入力
3. 届いた確認メールのリンクから登録完了（パスワードは厳重管理）

### 1-2. 公式アカウントの開設
1. https://entry.line.biz/ から無料開設
2. 入力内容（推奨値）：
   - アカウント名：`RV合宿【別府國盗リ合戦】`
   - 業種：大分類「メディア・エンタメ」→ 小分類「イベント」
   - 運用目的：「イベント運営」
3. 開設後、**LINE Official Account Manager**（https://manager.line.biz/）にログインできることを確認

### 1-3. ベーシックID と 友だち追加URL の確認
- 管理画面 →「設定」→「アカウント設定」に **ベーシックID（@xxxxxaaa 形式）** が表示される
- 友だち追加URLは `https://lin.ee/xxxxxxx` 形式で「友だち追加ガイド」から取得
- → このURLを後述 STEP 4 でサイトに差し替え

### 1-4. Messaging API の有効化（開発連携）
1. 管理画面 →「設定」→「Messaging API」→「利用する」
2. プロバイダー名：`RV`（新規作成）
3. https://developers.line.biz/console/ で以下を控える：
   - チャネルシークレット
   - チャネルアクセストークン（長期）を発行
4. Webhook URL：`https://api.art-crea.jp/line/webhook` を設定し「Webhookの利用」ON
5. 「応答メッセージ」OFF（Bot制御に一本化）

---

## STEP 2：デザイン反映（別府合戦の世界観をLINEに）

### 2-1. プロフィール設定
| 項目 | 設定内容 |
|---|---|
| プロフィール画像 | 赤軍エンブレム画像（IMG_5268系）※正方形にトリミングして使用 |
| 背景画像 | 「別府合戦」集合ビジュアル（IMG_5270系） |
| ステータスメッセージ | `8/27 別府國盗リ合戦 ⚔️ 毎時つぶやきで+10石` |

### 2-2. リッチメニュー（生成済みファイルを使用）
**生成物：`rv_line_richmenu.png`（2500×1686px・LINE公式推奨サイズ）**

| ボタン | 動作（`rv_line_richmenu.json` に定義済み） |
|---|---|
| 💬 毎時つぶやき | テキスト「つぶやき」を送信 → Botが受付&加点 |
| 📸 写真ミッション | カメラを起動（`"type":"camera"`） |
| 📍 位置情報を送る | 位置情報送信画面を起動（`"type":"location"`） |
| 🗺️ 戦況盤を見る | https://board.art-crea.jp/ を開く（同期盤） |
| 🏆 ランキング | テキスト「ランキング」→ Botが石高速報を返信 |
| 🚨 緊急連絡 | テキスト「緊急」→ Botが GM電話番号と通話導線を即返信 |

**設定方法A（管理画面・ノーコード）**：
LINE Official Account Manager →「トークルーム管理」→「リッチメニュー」→ 新規作成 → テンプレート「大・6分割」を選択 → `rv_line_richmenu.png` をアップロード → 各エリアにアクションを手動設定

**設定方法B（API・JSON流し込み）**：
```bash
# 1. メニュー作成
curl -X POST https://api.line.me/v2/bot/richmenu \
  -H "Authorization: Bearer {チャネルアクセストークン}" \
  -H "Content-Type: application/json" \
  -d @rv_line_richmenu.json
# → richMenuId が返る

# 2. 画像アップロード
curl -X POST https://api-data.line.me/v2/bot/richmenu/{richMenuId}/content \
  -H "Authorization: Bearer {トークン}" \
  -H "Content-Type: image/png" \
  -T rv_line_richmenu.png

# 3. 全ユーザーに適用
curl -X POST https://api.line.me/v2/bot/user/all/richmenu/{richMenuId} \
  -H "Authorization: Bearer {トークン}"
```

### 2-3. あいさつメッセージ（友だち追加時）
```
⚔️ RV合宿【別府國盗リ合戦】へようこそ！

まずは武将登録をお願いします👇
①お名前（本名）
②生年月日（相性診断でチーム編成に使います）
③緊急連絡先

8/27は下のメニューから
💬毎時つぶやき（+10石）と📍位置情報（+5石）をお忘れなく！
```

---

## STEP 3：Googleフォームの作成（art.crea01@gmail.com）

### 3-1. 申し込みフォーム
1. art.crea01@gmail.com で https://forms.google.com/ にログイン →「新しいフォーム」
2. タイトル：`RV合宿【別府國盗リ合戦 8/27】参加申し込み`
3. 設問（推奨構成）：
   - 氏名（記述・必須）／ふりがな
   - 生年月日（日付・必須）※相性診断・チーム編成用
   - 電話番号（記述・必須）※緊急連絡用
   - LINE表示名（記述）
   - Slackで使用するメールアドレス（記述）
   - 希望武将キャラ（プルダウン30武将 or 「おまかせ」）
   - 食物アレルギー・持病等（任意）
   - 個人情報の取扱い同意（チェック・必須）
4. 設定 →「回答」→ スプレッドシートにリンク（→ 将来DBに取り込み）
5. 「送信」→ リンクアイコン →「URLを短縮」→ **`https://forms.gle/xxxxxxx` を控える**

### 3-2. 問い合わせフォーム
同様に `RV合宿 お問い合わせ` フォームを作成（氏名・メール・問い合わせ内容の3問で十分）

---

## STEP 4：サイトへのURL差し替え（コメントアウト運用）

サイトHTML（`rv_camp_allinone.html` / `rv_camp_production/index.html`）には、
以下の形式で**仮URL（個人アカウント作成分）と本番URLの切り替えコメント**を設置済みです：

```html
<!-- ▼申込Googleフォーム URL▼ -->
<!-- 【仮】art.crea01@gmail.com で作成した forms.gle のURLを href に貼る -->
<a class="gform" href="https://forms.gle/XXXXXXXXXXXX" target="_blank" rel="noopener">📝 Googleフォームで申し込む</a>
<!-- 【本番】法人/イベント用アカウントに移行したら↓を有効化し、上の行を削除
<a class="gform" href="https://forms.gle/HONBAN-URL" target="_blank" rel="noopener">📝 Googleフォームで申し込む</a>
-->
<!-- ▲申込Googleフォーム URL▲ -->
```

差し替え箇所は「▼」で検索すれば全て見つかります（申込フォーム／問い合わせフォーム／LINE友だち追加×2／Slack招待）。

---

## STEP 5：本番移行時のチェックリスト

| 項目 | 仮（現在） | 本番移行 |
|---|---|---|
| Google アカウント | art.crea01@gmail.com | イベント用 or Google Workspace に**フォームの所有権を移転**（フォーム右上⋮→共同編集者追加→所有者変更） |
| LINE公式 | 同メールのビジネスID | 管理画面「権限管理」で本番管理者を追加 → 権限「管理者」付与後、必要なら作成者を降格 |
| 認証済アカウント | 未認証（灰色バッジ） | 集客を強化するなら認証申請（青バッジ・検索露出UP） |
| 料金プラン | フリー（月200通） | 36名×毎時プッシュを行う日はライトプラン以上を検討 |
| GMキー（同期盤） | rv2026gm | 本番用の推測されにくいキーに変更 |
