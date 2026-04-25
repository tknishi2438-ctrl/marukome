# マルコメ杯アプリ 引き継ぎメモ（2026-04-25）

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
  ├── marukomecup_top.png    トップページのイラスト画像
  └── pog_backup_20260419.html  バックアップ（不要なら削除OK）
```

---

## インフラ
| 項目 | 内容 |
|------|------|
| ホスティング | GitHub Pages |
| URL | https://tknishi2438-ctrl.github.io/marukome/ |
| GitHub | https://github.com/tknishi2438-ctrl/marukome |
| データ保存 | Firebase Firestore（プロジェクト：marukome-cup） |

### デプロイ方法（毎回この4行）
```bash
cd "/Users/nishitk_apple/marukome"
git add .
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
- **POG**: `db.collection('g1app').doc('pog_data')`
- **三頭勝負**: `db.collection('g1app').doc('data')`

---

## 本日（2026-04-25）までの作業内容

### ✅ 前回（2026-04-20）完了済み
1. GitHub + GitHub Pages 初期セットアップ
2. トップページ(index.html)リデザイン
3. フォント統一（Noto Serif JP）
4. 三頭勝負ランキング改善（バッジ変更、TOPボタン）
5. POGページ改善（ボタン分離、色修正、TOPボタン等）

### ✅ 今回（2026-04-25）完了した変更

#### pog.html
1. **タブ順変更**：「指名馬登録」を「レース入力」より前に移動
2. **指名馬登録セクション**：登録フォームを一覧より上に移動
3. **Notion同期の記載を削除**：card-subのテキスト整理
4. **seedコード削除**：`(function seedRaces(){...})()` 約160行を削除（Firestoreに全データ移行済みのため不要）
5. **未登録レース4件を一括登録**：
   - ペルウィクトール 2026-04-18 3歳未勝利 6着
   - ダークマルス 2026-04-19 3歳1勝クラス 2着
   - クランズクラウン 2026-04-19 3歳未勝利 7着
   - ラヴズプレミアム 2026-04-18 3歳未勝利 9着

#### santozanmai.html
6. **特別レース機能を追加**：
   - レース情報カードに「⭐ 特別レース」チェックボックスを追加
   - チェックを入れると、春→「宝塚記念」、秋→「有馬記念」がレース名に自動入力される
   - 馬の選択は通常と同じ3頭ずつ
   - 結果・履歴カードに「⭐ 特別」バッジが表示される
   - 保存データに `isSpecial: true` が記録される
   - リセット時にチェックが外れる
   - 編集モーダルも特別レース対応済み

---

## pog_race_history.csvについて
- ローカルで管理している出走レースの記録ファイル
- Firestoreのデータと比較する際に使用
- 新しいレースが出たらこのCSVも更新すること
- 列：指名者, 馬名, 日付, レース名, 着順

---

## 次にやること（未完了）

### POGのUI改善（残り）
- [ ] **馬別獲得ポイント**：JRA URL列・出走数列を削除、馬名折り返し防止

---

## ユーザー情報
- GitHubユーザー名：tknishi2438-ctrl
- 初心者なので丁寧に説明すること
- ターミナル操作はコマンドを1行ずつ教える
