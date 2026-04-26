# マルコメ杯アプリ 引き継ぎメモ（2026-04-26）

## プロジェクト概要
競馬仲間3人（西澤・髙坂・小出）で使うG1競馬ゲームアプリ。
- **POG**（指名馬育成ゲーム）
- **G1三頭勝負**（毎週G1で3頭を選んでポイント対決）

---

## ファイル構成
```
/Users/nishitk_apple/marukome/
  ├── index.html             トップページ（レトロ競馬イラスト入り）
  ├── pog.html               POGアプリ
  ├── santozanmai.html       G1三頭勝負アプリ
  ├── pog_race_history.csv   POG出走レース記録（ローカル管理用）
  └── marukomecup_top.png    トップページのイラスト画像
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

---

## 2026-04-26 完了した変更

### pog.html
1. **登録済みレース結果テーブルのはみ出し修正**：`width:100%`・`overflow-x:auto`
2. **登録馬一覧トグル改善**：`▶`を押すと行の下にJRA/成績/情報/編集が展開、`▼`で閉じると成績・情報パネルも連動して閉じる
3. **削除ボタンを編集モーダル内に移動**：登録馬・レース結果・参加者、すべて編集モーダルの左下に「🗑 削除」ボタンを配置
4. **POGランキングを派手に改装**：金・銀・銅バッジ、左ボーダー、背景色付き
5. **内訳列を修正**：左寄り・縦並び・折り返し防止
6. **指名者名の縦書き防止**：`white-space:nowrap`
7. **頭数列を削除**
8. **シーズン管理機能を追加**：
   - ランキングタブ上部にシーズン選択ドロップダウン追加
   - データ管理タブに「新シーズンを開始する」ボタン追加
   - 新シーズン開始時：現在データを`pog_s31`にアーカイブ、`pog_meta`を更新、馬・レースをリセット（参加者は引き継ぎ）
   - 過去シーズン選択時：ランキング＋レース履歴（読み取り専用）を表示

### santozanmai.html
9. **G1レース登録を手動入力に変更**：Wikipedia取得を廃止、テキスト入力＋定番リストボタンに変更
10. **ランキングをシーズン単位に変更**：シーズン絞り込みドロップダウン追加、全期間選択時は過去シーズンのランキングが並ぶ
11. **特別レース（宝塚記念・有馬記念）を単独表示**：通常レースとは別に「⭐ シーズン決戦」カードで表示

### index.html・santozanmai.html
12. **フォント統一**：POGと同じゴシック体（Hiragino Sans）に統一

---

## 残件
**特になし。** 現在は運用しながら気になる点を随時修正していく段階。

---

## ユーザー情報
- GitHubユーザー名：tknishi2438-ctrl
- 初心者なので丁寧に説明すること
- ターミナル操作はコマンドを1行ずつ教えず、私（Claude）が直接実行する
