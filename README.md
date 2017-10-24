# test_gitbook_from_one_mdfile
- [GitBook サンプル](https://stakiran.gitbooks.io/gitbook_from_one_mdfile/)
- [GitBook サンプル](https://stakiran.gitbooks.io/gitbook_from_one_mdfile/content/) ← 上が開けないならこっち
  - 違いはURL末尾の `/content/` なんだけど、これどっちが正しいんだ？いまいちわからん……
- [GitHub リポジトリ](https://github.com/stakiran/test_gitbook_one_mdfile)
- [ビルドステータス](https://www.gitbook.com/book/stakiran/gitbook_from_one_mdfile/activity) 要ログイン

## SUMMARY の作り方
- [intoc](https://github.com/stakiran/intoc) で TOC つくる
- TOC の `-` を `*` に置換した上で、SUMMARY.md にコピペ

## これで日本語見出しは gitbook 上で正しくリンクされるん？
A: されない :sob: :sob: :sob: :sob:

### 問題1: GitHub と除外文字が違う
- Expect: `えgroup-policy-殺したってことでしょまずくない`
- Actual: `<h2 id="え？group-policy-殺したってことでしょ？まずくない？">え？Group Policy 殺したってことでしょ？まずくない？</h2>`

この例では **全角の？が消えていない**。おっかしーな、GitHub の TOC がレンダリングされる時は消えるんだけどな。やはりサービス次第でアルゴリズム違うんか。だるすぎる。。。

### 問題2: 同名アンカーについて同名回避がされてない
- Expect: `問題-1`
- Actual: `<h2 id="問題">問題</h2>`

GitHub だと「問題」という名前のアンカーが複数存在する場合、後続は `問題-1` とか `問題-2` みたいに数字が振られてかぶらないようにしてくれるのよ。でも gitbook はしてくれないみたいだねー。。。

## 結論
**1つのmdファイルでbook作るのは無理そう**
