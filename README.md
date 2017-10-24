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

- Expect: `えgroup-policy-殺したってことでしょまずくない`
- Actual: `<h2 id="え？group-policy-殺したってことでしょ？まずくない？">え？Group Policy 殺したってことでしょ？まずくない？</h2>`

この例では **全角の？が消えていない**。おっかしーな、GitHub の TOC がレンダリングされる時は消えるんだけどな。やはりサービス次第でアルゴリズム違うんか。だるすぎる。。。
