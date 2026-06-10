# マルコメ杯アプリ 引き継ぎメモ（2026-06-11）

## プロジェクト概要
競馬仲間3人（西澤・髙坂・小出）で使うG1競馬ゲームアプリ。
- **POG**（指名馬育成ゲーム）
- **G1三頭勝負**（毎週G1で3頭を選んでポイント対決）

---

## ファイル構成
```
/Users/nishitk_apple/Claude/marukome/
  ├── index.html             トップページ
  ├── pog.html               POGアプリ
  ├── santozanmai.html       G1三頭勝負アプリ
  ├── pog_race_history.csv   POG出走レース記録（ローカル管理用）
  ├── top_2606.png           トップページ画像（優勝記念版・現在使用中）
  ├── top_2605.jpg           旧トップページ画像（5月版）
  └── marukomecup_top.png    旧トップページ画像
```

---

## インフラ
| 項目 | 内容 |
|------|------|
| ホスティング | GitHub Pages |
| URL | https://tknishi2438-ctrl.github.io/marukome/ |
| GitHub | https://github.com/tknishi2438-ctrl/marukome |
| データ保存 | Firebase Firestore（プロジェクト：marukome-cup） |

### デプロイ方法（毎回この3行）
```bash
git add pog.html santozanmai.html index.html
git commit -m "変更内容のメモ"
git push
```

---

## Firebase設定（コード内に埋め込み済み）
```
apiKey: "AIzaSyCxtkpZ-jVcjk7713SwvfGLn6XN5OE81Hs"
projectId: "marukome-cup"
authDomain: "marukome-cup.firebaseapp.com"
```

### Firestoreのドキュメント構造
- **POG（現在シーズン）**: `db.collection('g1app').doc('pog_data')`
- **POG（アーカイブ）**: `db.collection('g1app').doc('pog_s31')` など回数ごと
- **POGメタ情報**: `db.collection('g1app').doc('pog_meta')` → `{ archives: [{round, title, archivedAt}] }`
- **三頭勝負**: `db.collection('g1app').doc('data')`

### Firestoreセキュリティルール
```
allow read, write: if true;
```
期限なし・全員読み書き可。仲間内アプリなのでこれで運用中。

### Firestore直接操作の方法（覚書）
Node.jsが使えない環境でも、Python3の標準ライブラリ（urllib）でFirestore REST APIを叩ける。
```bash
python3 /tmp/add_races.py  # スクリプトは毎回作成する
```
- GET: `https://firestore.googleapis.com/v1/projects/marukome-cup/databases/(default)/documents/g1app/pog_data?key=<APIKEY>`
- PATCH: 同URLに `updateMask.fieldPaths=races&updateMask.fieldPaths=nRId` を追加

### レース登録の手順（定期作業）
1. `pog_race_history.csv` の内容とFirestoreのJSONを突き合わせて未登録レースを特定
2. 開催期間（`start`〜`end`）内のものだけを対象にする
3. Pythonスクリプトをその都度 `/tmp/` に作成してPATCH
4. `nRId` = 最後のレースid + 1 で更新

---

## 現在のFirestoreデータ状態（2026-06-11時点）
| 項目 | 値 |
|------|-----|
| シーズン | **第32回**（2026-06-13 〜 2027-05-30）※2027年日本ダービー当日まで |
| レース数 | 0件（nRId: 1） |
| 指名馬 | 0頭（これから登録） |
| winnerId | なし（削除済み） |
| 特別ポイント | レコード5・単勝万券10・馬連万券5・馬単万券5・ダービー50・G1複数50（**抽せん馬ボーナスは廃止**） |

### アーカイブ
- `pog_s31`：第31回（レース148件・馬45頭・優勝 髙坂171pt）。当時のポイント設定（抽せん馬5pt含む）で計算される。

---

## POGアプリの重要な仕様（pog.html）

### ポイント計算の仕組み
- **基本ポイント表**（`S.ptTable`）: Firestoreに保存。シーズンごとに独立。
- **特別ポイント**（`S.spPts`）: Firestoreに保存。シーズンごとに独立。
- アーカイブ参照時は、そのシーズン当時のポイント設定で計算される。

### 優勝マークの仕組み
- Firestoreの `winnerId`（オーナーのid）が設定されているとき、ランキング1位の「1st」バッジ左上に🏆がふわっと点滅表示される。
- `winnerId` がない（新シーズン開始後）は自動的に消える。
- アーカイブ表示時も、そのシーズンのデータに `winnerId` が含まれていれば表示される。

### 第31回最終スコア
| 順位 | 指名者 | ポイント |
|------|--------|---------|
| 🏆 1位 | 髙坂 | 171pt |
| 2位 | 西澤 | 121pt |
| 3位 | 小出 | 101pt |

### 新シーズン移行の手順
1. 全レース結果が確定したら、アプリの「管理」→「データ管理」タブへ
2. **「新シーズンを開始する →」** ボタンを押す
3. 第31回が `pog_s31` としてアーカイブされ、第32回が始まる
4. 新シーズン開始後、「管理」→「設定」タブでポイント設定を変更して「保存する」

### ポイント設定変更時の注意
新シーズンでルールを変更する場合：
- アプリの設定タブで変更 → 「保存する」で Firestore に書き込まれる
- 過去シーズンのポイントには影響しない（シーズンごとに保存済み）

---

## これまでの変更履歴

### 2026-05-10
- トップページ画像を5月版に更新（`marukomecup_top.png` → `top_2605.jpg`）、タイトルオーバーレイ削除
- 三頭勝負：プレイヤー別内訳のデザイン改善（白背景＋グリーン系ボーダー、グリッド整列）
- POG・三頭勝負共通：競争除外（0pt）・競争中止（−3pt）を着順選択肢に追加
- POGレース登録：id 129〜131

### 2026-05-11
- POGレース登録：id 132〜137

### 2026-05-18
- POGレース登録：id 138〜142

### 2026-05-30
- POG：基本ポイント表（`ptTable`）をFirestoreに保存する方式に変更
- POGレース登録：id 143〜147

### 2026-06-10
- POGレース登録：id 148〜150（シーズン最終週分）
  - id 148: シーガルワールド 3歳未勝利 5着（5/30）
  - id 149: グランマエストロ 3歳未勝利 6着（5/31）
  - id 150: アウダーシア 東京優駿(GI) 11着（5/31）
- POG第31回シーズン終了（優勝：髙坂 171pt）
- Firestoreに `winnerId: 2`（髙坂）を保存
- ランキング1位に🏆ふわっと点滅アニメーション追加（1stバッジ左上）
- トップページ画像を優勝記念版に差し替え（`top_2606.png`）

### 2026-06-10〜11（第32回セットアップ）
- 第32回シーズン開始を確認（ユーザーがアプリのボタンで移行済み・`pog_s31`アーカイブ正常）
- **不具合修正**：新シーズンに旧シーズンの4レースが復活する問題を解消。原因はpog.html内に残っていた2026-04-22の応急処置コード（未登録レース4件をページ読込のたびに自動追加）。コードを削除しFirestoreを清掃。
- `winnerId` をFirestoreから削除（🏆は第31回アーカイブ表示のみに）
- **抽せん馬ボーナスを廃止**（第32回〜）：設定画面・馬登録/編集の「抽せん馬」チェック・レース入力の自動表示・馬一覧の「抽」列を削除。計算ロジックはガード付きで残しアーカイブ互換を維持。Firestoreの`spPts`からも`chu`を削除。
- 集計期間を設定：2026-06-13 〜 2027-05-30（2027年日本ダービー＝5月最終日曜）

---

## 残件
- **第32回の指名馬登録**：メンバーが決まり次第、アプリの「指名馬」タブから登録。
- 基本ポイント表（`ptTable`）のルール変更があれば「管理」→「設定」タブで変更→「保存する」。

---

## ユーザー情報
- GitHubユーザー名：tknishi2438-ctrl
- 初心者なので丁寧に説明すること
- ターミナル操作はコマンドを1行ずつ教えず、私（Claude）が直接実行する
