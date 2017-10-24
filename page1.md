お試しで今日ブログに書いた内容をば。

# 171024(Tue) Windows で急に画面がチカチカし始めた現象の犯人は GPSvcGroup だった

[:contents]

## 前提
- Windows7 64bit
- Group Policy でスクリーンセーバーを有効にされている Windows PC

## 現象
スクリーンセーバー設定を変えたくて、レジストリの `HKEY_CURRENT_USER\Control Panel\Desktop` にある `ScreenSaveActive` エントリの値を 1 → 0 にしてみた。

そうしたら直後、勝手に 1 に書き換わった。

かと思えば、その後も画面再描画が1-2秒に1回のペースで永久的に生じるように。チカチカチカチカチカチカしてウザイことこの上ない。

## 犯人は？
ProcessExplorer で調べてみたところ、こいつ。

- `C:\Windows\system32\svchost.exe -k GPSvcGroup`
- Group Policy Client サービス

## 対処
上記 GPSvcGroup プロセスを Kill した。そうしたら止んだ。

## え？Group Policy 殺したってことでしょ？まずくない？
特に問題は起きてない（と思う）。あと、そのうちサービスはまた立ち上がる。

なお services.msc から手動で開始したりは出来ない模様。

# 171024(Tue)AutoHotkey で Edit 時のデフォルトのエディタを notepad.exe から変更できない件

[:contents]

## 問題
今は ctrl + alt + e で ahk ファイルを愛用テキストエディタで開くようにしている。

```ahk
^!e::Edit
```

ところがつい最近導入した環境の AHK Version 1.1.26.01 だと notepad.exe で開かれてしまう。

## 対処1: レジストリいじる（失敗）
使用エディタはレジストリの `HKEY_CLASSES_ROOT\AutoHotkeyScript\Shell\Edit\Command` を見るようになっている（らしい）。

```reg
Windows Registry Editor Version 5.00

[HKEY_CLASSES_ROOT\AutoHotkeyScript\Shell\Edit\Command]
@="\"C:\\Program Files (x86)\\Hidemaru\\Hidemaru.exe\" \"%1\""

```

のだが、上記をレジストリに追加しても効果がない。AutoHotkey をいったん再起動しても同様。

## 対処2: エディタも ahk ファイルも明示的に指定する（成功）
こんな感じで明示的に指定してみたら成功。

```ahk
^!e::Run, "C:\Program Files (x86)\Hidemaru\HIDEMARU.EXE" "D:\work\github\stakiran\dotfiles\autohotkey\office2.ahk"
```

## で、原因は？
不明。

# 171024(Tue)リモートデスクトップに対してのみ StrokesPlus を無効にする

## 問題
リモート元 PC1 では StrokesPlus を使っていて、リモート先 PC2 ではかざぐるマウスを使っている。PC2 上でマウスジェスチャーを実行しようとすると、PC1 で動いてる StrokesPlus が働いてしまい、かざぐるマウスの分が働かない。かざぐるマウスが働いてほしい。

## 対処
StrokesPlus のトレイアイコンを右クリック > Ignored List > Filename Pattern に mstsc.exe を入れて新規する。

## ハマった点
最初はリモートデスクトップ用の App を作って、そこに何のアクションも入れない＆Globalは使わない、にすることで実現しようとしたんだけど、上手く動かなかった。

# 171024(Tue) Windows の at コマンドをラップしてリマインダー作ろうとしたけど諦めた
## 結論
リマインダーとしては使い物にならない。

Q: Why?
  - A1: at コマンドで指定したコマンドラインはバックグラウンドで起動されるため **ウィンドウが表示されない**
  - A2: 毎24日の10:00に実行するタスク1を、10/24 9:59 に登録したとしても、1分後にタスク1が起動しない（ **登録タスクの実施判定どうなってんだ？**）
  - A3: 10:00 に、10:00に実行するタスク1を登録すると、タスク1は明日実行されるタスクとして登録される（ **コマンドラインの動作確認のための即座起動はできない** ）

まずウィンドウが表示されないのが致命的すぎる。リマインドできないじゃん。でなくとも実施判定が信用できないし、動作確認もしずらくて、うーん。。。

## 作業ログ
```
$ at 9:07 /every:24 "notepad.exe"
```

2017/10/24 09:07:34 にこれを叩いても即座に notepad.exe が実行されないんだが。

```
$ at 9:08 "notepad.exe"
```

2017/10/24 09:07:45 にこれを叩いても、09:08:18 になっても実行されないんだが。以下のように、at コマンドの一覧にも残ったままだし……

```
$ at
状態 ID     日付                    時刻          コマンド ライン
-------------------------------------------------------------------------------
        1   毎 24                   9:07          notepad.exe
        2   今日                    9:08          notepad.exe
        
```

Task Scheduler が起動してない？services.msc で見てみた。してるしてる。

……あ、コマンドラインとして「関連付けられたファイル」のみ対応ってこと？

```
$ at 9:12 "D:\work\gist\stakiran\gistfile1.md"
```

09:12:42 になってるのに起動しない。。。mdファイルに関連付けられてるエディタが起動するはずなのだが。

でも at コマンドの一覧からは消えている（起動したってこと）みたいだが。意味わからん。

```
$ at 9:12 /interactive "D:\work\gist\stakiran\gistfile1.md"
```

interactive もダメ。画面に出ないじゃねえか。

……プロセスを見てみる。ProcessExplorerで。そしたら **notepad.exe が何個も立ち上がってやがる**。なるほど、**atコマンドはバックグラウンドで立ち上げやがるのか**。かつ、**立ち上げたプログラムが閉じられるまで at コマンド上でも表示され続ける** と。んー。。。

ちなみにタスクのクリアはこう。

指定IDだけ消す。

```
$ at 1 /delete
```

全部消す（`/yes`は確認プロンプト無しで一気に実行）。

```
$ at /delete /yes
```
