# マルコメ杯アプリ 引き継ぎメモ（2026-07-07）

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
  ├── top_2607.png           トップページ画像（現在使用中・劇画調AI生成・第32回開幕版）
  ├── top_2606.png           旧トップページ画像（優勝記念版）
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
Python3の標準ライブラリ（urllib）でFirestore REST APIを叩ける。
- GET: `https://firestore.googleapis.com/v1/projects/marukome-cup/databases/(default)/documents/g1app/pog_data?key=<APIKEY>`
- PATCH: 同URLに `updateMask.fieldPaths=races&updateMask.fieldPaths=nRId` を追加

### ⚠️ レースデータのフィールド構造（重要・間違えると画面に出ない）
`races` 配列の各要素はアプリ（pog.html）がそのまま `S.races` として使うため、
**必ずこのフィールド名・型で登録すること**：
```json
{
  "id": 9,                    // integer。nRIdの現在値から連番
  "horseId": 29,              // integer。Firestoreのhorses配列のid
  "date": "2026/06/13",       // string "YYYY/MM/DD"
  "name": "2歳新馬",           // string。レース名（raceNameではない！）
  "grade": "newface",          // G1|G2|G3|OP|flat(平場)|maiden(未勝利)|newface(新馬)
  "region": "JRA",             // JRA|local|overseas
  "place": "5",                // string！ "1"〜"9"|"last"|"stopped"|"scratch"
  "confirmed": false,          // boolean
  "sp_record": false, "sp_tansho": false, "sp_umaren": false,
  "sp_umatan": false, "sp_derby": false, "sp_g1x2": false
}
```
登録後は `nRId` を「最後のid+1」に更新する。

### レース登録の手順（定期作業）
1. ユーザーからnetkeibaのPOGページ（みんなのPOG）のスクリーンショットをもらい、出走馬・日付・レース名・着順を特定する
2. FirestoreのhorsesからhorseId（馬名で照合）を確認する
3. 可能ならJRAの各馬ページ（horses[].jraUrl）で結果を照合する
4. Pythonスクリプトで既存races配列に追記PATCH（上記フィールド構造厳守）
5. `pog_race_history.csv` にも追記（列: name,date,raceName,result）

### ⚠️ netkeibaのグループページに注意
netkeibaのPOGグループ（group_id=112540）は**第31回のもの**。
第32回の馬とは別なので、netkeibaスクレイピング（pog-race-scraperスキル）は現状使えない。
レース情報はユーザーのスクリーンショットかJRAページから取得する。

---

## 現在のFirestoreデータ状態（2026-07-07時点）
| 項目 | 値 |
|------|-----|
| シーズン | **第32回**（2026-06-13 〜 2027-05-30） |
| レース数 | 8件（id 9〜16、nRId: 17）※id 1〜8は欠番（誤登録の削除等） |
| 指名馬 | 45頭（id 1〜15 ニシタカ／16〜30 Kサカ／31〜45 Kイデ） |
| JRAリンク | 26頭登録済み・**19頭未登録**（デビュー前） |

### 登録済みレース（第32回）
| id | 馬名 | 指名者 | 日付 | レース | 着順 |
|----|------|--------|------|--------|------|
| 9 | アセンシオン | Kサカ | 6/13 | 2歳新馬 | 5着 |
| 10 | ユタライト | Kイデ | 6/13 | 2歳新馬 | 6着 |
| 11 | インカルナータ | ニシタカ | 6/14 | 2歳新馬 | 1着 |
| 12 | ダイナマイク | ニシタカ | 6/14 | 2歳新馬 | 4着 |
| 13 | スノーウィスパー | Kサカ | 6/20 | 2歳新馬 | 6着 |
| 14 | ラガリーガ | ニシタカ | 6/21 | 2歳新馬 | 8着 |
| 15 | ステラトップガン | Kサカ | 6/27 | 2歳新馬 | 2着 |
| 16 | ラッキースパークル | Kサカ | 6/28 | 2歳新馬 | 3着 |

### JRA未登録の19頭（デビュー後にJRA検索して追記する）
- ニシタカ: ノイエルング(1)、レジューノワール(10)、ラニアケア(11)、アステリアドンナ(12)、ククイプカ(13)、ジーティーギャング(14)
- Kサカ: サルティリア(20)、レヴェレーター(21)、スターリーフライト(22)、ラトナジャーラ(25)、ハートノート(26)、オルトルゴ(28)
- Kイデ: リーベスグルース(33)、エクアトーレ(37)、ベルオナチュレル(38)、ソウマムソウ(39)、シャムリベルク(40)、オディオペルデレ(44)、ウインヴァンガード(45)

※2026-07-07にJRA馬名検索で全頭確認済み→上記は全てヒットせず。ステラトップガン(30)のみ見つかり登録済み。

### JRA馬名検索のやり方（Claude in Chrome）
1. `https://www.jra.go.jp/keiba/` に移動し「競走馬検索」リンクをJSでclick()（直接URLはパラメータエラーになる）
2. 検索フォーム `input[name="iv_h_name"]` に馬名を入れ、フォーム内の「検索」アンカーをclick()
3. 結果ページの `a[href*="accessU"]` から馬ページURL（CNAME）を取得
4. **結果ページにも検索フォームが残っている**ので、「結果読み取り＋次の馬の検索実行」を1回のJS実行にまとめると速い
5. 馬ページから父・母・母父・調教師・毛色・馬主・生産牧場をパースしてFirestoreのhorsesにPATCH

### アーカイブ
- `pog_s31`：第31回（レース148件・馬45頭・優勝 髙坂171pt）

---

## POGアプリの重要な仕様（pog.html）
- **ポイント表**（`S.ptTable`）・**特別ポイント**（`S.spPts`）はFirestoreにシーズンごと保存
- アーカイブ参照時はそのシーズン当時の設定で計算される
- `winnerId` があるとランキング1位に🏆表示（現在は31回アーカイブのみ）
- 指名馬一覧：馬名横に「出」（出走済み・青）「勝」（1着あり・金）バッジ、オーナー見出しに「出走 n/15 勝利 n/15」カウント表示（2026-07-07追加）

---

## 直近の変更履歴（詳細はgit log参照）

### 2026-06-22〜07-07（第32回レース登録開始）
- レース結果の登録運用開始（id 9〜16の8件）
- **教訓**：racesのフィールド名を間違えて登録→画面に出ないバグ。正しい構造は上記参照（name/place/grade等。raceName/resultではない）
- **教訓**：netkeibaグループ112540は31回の馬なので32回には使えない
- 確認済みチェックボックスの行背景が黒くなるバグ修正（`#f0fdf4`/白に）
- 指名馬一覧に出走済み・勝利マークとオーナー別カウント追加
- ステラトップガンのJRA情報登録（他19頭は未デビューで未登録）

### それ以前
- 2026-06-10：第31回終了（優勝 髙坂171pt）→ `pog_s31` アーカイブ
- 2026-06-11〜17：第32回セットアップ（45頭登録・抽せん馬ボーナス廃止・集計期間設定）

---

## 残件
- **レース結果の定期登録**：毎週末後、ユーザーからスクリーンショットをもらって登録（次は7/5のジャンゴッド（Kサカ）・ショウナンガレオン（Kイデ）出走予定分から）
- **JRA未登録19頭の情報追記**：デビュー後にJRA検索→jraUrl・血統をFirestoreへ（手順は上記）

---

## ユーザー情報
- GitHubユーザー名：tknishi2438-ctrl
- 初心者なので丁寧に説明すること
- ターミナル操作はコマンドを1行ずつ教えず、私（Claude）が直接実行する
