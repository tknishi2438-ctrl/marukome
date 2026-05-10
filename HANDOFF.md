# マルコメ杯アプリ 引き継ぎメモ（2026-05-10）

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
  ├── top_2605.jpg           トップページ画像（5月版・現在使用中）
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

---

## 2026-05-10 完了した変更

### index.html
1. **トップページ画像を5月版に更新**：`marukomecup_top.png` → `top_2605.jpg`
2. **タイトルオーバーレイを削除**：画像にタイトルが入っているため不要

### POGレース登録（Firestore）
3. **5月分の未登録レース3件を追加**：Pythonスクリプトで登録
   - レース数 126→129件、nRId: 129→132

| id | 馬名 | 指名者 | 日付 | レース | 着順 |
|----|------|--------|------|--------|------|
| 129 | ヘイジュード | 西澤 | 2026-05-03 | プリンシパルS | 13着 |
| 130 | アスクブームキセキ | 髙坂 | 2026-05-03 | 3歳未勝利 | 11着 |
| 131 | ラヴズプレミアム | 小出 | 2026-05-02 | 3歳未勝利 | 11着 |

### santozanmai.html
4. **プレイヤー別内訳のデザイン改善**：
   - 濃紺背景 → 白背景＋グリーン系ボーダーでサイト全体に統一
   - 馬名・着順を改行して縦並び表示
   - 馬名列・着順列・ptラベル・pt値をグリッドで整列

### pog.html・santozanmai.html 共通
5. **競争除外・競争中止を着順選択肢に追加**：
   - 競争除外 → 0pt（`scratch`/`98`）
   - 競争中止 → −3pt（`stopped`/`99`、最下位と同じ扱い）
   - POG: 着順selectに追加、calcPts/表示/ドンジリ自動チェック対応
   - 三頭勝負: 着順selectに追加、placementPt関数・表示・編集モーダル対応

---

## 残件
**特になし。** 現在は運用しながら気になる点を随時修正していく段階。

---

## ユーザー情報
- GitHubユーザー名：tknishi2438-ctrl
- 初心者なので丁寧に説明すること
- ターミナル操作はコマンドを1行ずつ教えず、私（Claude）が直接実行する
