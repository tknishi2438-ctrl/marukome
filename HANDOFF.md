# マルコメ杯アプリ 引き継ぎメモ（2026-04-20）

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

## 本日（2026-04-20）の作業内容

### ✅ 完了した変更
1. **GitHub + GitHub Pages 初期セットアップ**（Homebrew, gh CLI含む）
2. **トップページ(index.html)リデザイン**
   - 競馬イラスト（marukomecup_top.png）をヒーロー表示
   - レトロ・マンガ風ダークブラウンデザイン
   - キャッチコピー「週末は馬と生きる。」
   - サブタイトル削除
3. **フォント統一**（3ページとも Noto Serif JP に統一）
4. **三頭勝負ランキング改善**
   - メダル絵文字 → 1st/2nd/3rd バッジに変更
   - ヘッダーに「← TOP」ボタン追加
5. **POGページ改善**
   - 登録馬一覧：JRA URLボタン・成績ボタン・情報ボタンに分離
   - 馬別獲得ポイント：色修正・成績ボタン追加
   - 登録済みレース結果：色修正・区分列を削除しレース名横に統合
   - 大会設定：「リステッド競走は〜」削除・特別ポイント設定欄追加
   - G1複数特別pt：ダービー時は適用しないルールに修正
   - TOPボタン追加

---

## 次にやること（未完了）

### POGのUI改善（途中）
- [ ] **馬別獲得ポイント**：JRA URL列・出走数列を削除、馬名折り返し防止
- [ ] **指名馬登録・指名馬一覧**を上に表示（タブ順変更）
- [ ] **指名馬一覧**：馬名が縦長にならないように改善、トグルで成績・情報ボタン表示
- [ ] **Notion同期方法の記載**を削除


---

## Firebase設定（コード内に埋め込み済み）
```
apiKey: "AIzaSyCxtkpZ-jVcjk7713SwvfGLn6XN5OE81Hs"
projectId: "marukome-cup"
authDomain: "marukome-cup.firebaseapp.com"
```

---

## ユーザー情報
- GitHubユーザー名：tknishi2438-ctrl
- 初心者なので丁寧に説明すること
- ターミナル操作はコマンドを1行ずつ教える
