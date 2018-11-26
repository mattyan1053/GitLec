目次 

<!-- TOC -->

- [コミットへのタグ付け](#コミットへのタグ付け)
    - [現在の最新のコミットにタグをつける](#現在の最新のコミットにタグをつける)
    - [注釈付きタグ](#注釈付きタグ)
    - [過去のコミットにタグ付けする](#過去のコミットにタグ付けする)
    - [タグを削除する](#タグを削除する)
    - [タグをリモートリポジトリでも共有する](#タグをリモートリポジトリでも共有する)
- [マージに関するあれこれ](#マージに関するあれこれ)
    - [fast-forward](#fast-forward)
    - [[--squash]オプション](#--squashオプション)
- [プルの仕様](#プルの仕様)
- [リベース](#リベース)
    - [git rebase](#git-rebase)
    - [git rebase -i](#git-rebase--i)
    - [リベースの怖いところとお約束](#リベースの怖いところとお約束)
    - [一度コミットしたものをリベースする](#一度コミットしたものをリベースする)
- [まとめ](#まとめ)
- [最後に](#最後に)

<!-- /TOC -->

ここまででリポジトリの様々な操作をできるようになった。ここではその操作の細かなところを見ていく。
# コミットへのタグ付け
作業していくとどんどんコミットが増えていく。その中で、重要なコミット（転換点とか）に目印として「タグ」をつけていくことができる。  
```sh
$ git show <tag>
```
とすれば、タグの示すコミットの詳細を見ることができるし、
```sh
$ git reset [option] <tagname>
```
とすれば任意のタグの示すコミットにHEADを移動することができる（ブランチも移動する)。  
```sh
$ git tag
```
とすれば現在存在するタグを見ることが可能。自分よりも子にあたるコミットのタグなどもすべて表示される。さらに
```sh
$ git tag -l v1.1.*
```
などとしてあげれば「v1.1」で始まるタグのみをすべてリストアップできる。

## 現在の最新のコミットにタグをつける
現在いるブランチの最新のコミットにタグをつける。
```sh
$ git tag <tagname>
```
とすれば最新のコミットにタグ付けできる。例えば、`git tag v1.0`とした場合、  
![tag](image/part7/tag.bmp)  
というように一番新しいところに「v1.0」というタグがつけられる。  
## 注釈付きタグ
タグに注釈をつけることができる。タグの説明を付与できるということだ。  
```sh
$ git tag -a <tagname> -m "message of tag"
```
とすれば良い。こうすると
```sh
$ git show tag
```
としたときに、タグの注釈まで表示してくれる。`-m`オプションを省略した場合はエディタが起動するはずなので、そこで注釈を入力する。    

## 過去のコミットにタグ付けする
現在のコミットのみでなく、過去のコミットにタグをつけることも可能だ。
```sh
$ git tag <tagname> <commit>
```
`<commit>`のところはコミットのハッシュ値やブランチ、HEAD~などコミットを示すものを入れれば良い。また、`-a`や`-m`と合わせて注釈付きタグを過去のコミットにつけることも可能。このときは  
```sh
$ git tag -a <tagname> -m "tag message" <commit>
```
とコミット名を最後につければOK。  

## タグを削除する
作ったタグを削除したいときは  
```sh
$ git tag -d <tagname>
```
でOK。

## タグをリモートリポジトリでも共有する
タグは通常ローカルのみのものであり、プッシュしてもリモートリポジトリには反映されない。リモートリポジトリにもタグを共有したいときは
```sh
$ git push origin <tagname>
```
とタグをリモートリポジトリ（ここでは「origin」）にプッシュしてあげる必要がある。

# マージに関するあれこれ
Gitの機能の中でも特徴的な機能がマージだ。通常バージョンを分岐させてしまうと合流させるのは容易ではないがGitはそれを簡単にしてくれる。  
マージは実は結構奥深いので、ここで少し掘り下げておく。
## fast-forward
プルを行ったときや、分岐したブランチから元のブランチにマージしようとしたとき、「fast-forward」と出たことがあるのを見覚えあるだろうか。まずはこの「fast-forward」がどのような状態なのか説明する。次の状態を考えてほしい。  
![fast-forward0](image/part7/fast-forward0.bmp)  
この状態でmasterにbranch-1をマージをしたとき、つまり
```sh
$ git checkout master
$ git merge branch-1
```
としたとき、何が起こるかと言うとmasterとbranch-1をマージした結果できあがるのはbranch-1と同じ状態なので、新しいコミットは作成されず、masterがbranch-1と同じ場所に移動する。つまり枝分かれしない。  
![fast-forward1](image/part7/fast-forward1.bmp)  
このような状態になる。  
どういうことか整理すると**「branch-1のコミットの履歴」に「masterのコミットの履歴」が完全に含まれている時、branch-1をmasterにマージするとmasterはbranch-1の指し示すコミットまでfast-forwardする**ということだ。  
ちなみにfast-forwardできないとき、つまり  
![no-ff](image/part7/no-ff.bmp)  
のように、branch-1のコミットの履歴にmasterのコミットの履歴が含まれない時（masterの指しているものが含まれていないため）は、新しくコミットが作成される。  
![no-ff1](image/part7/no-ff1.bmp)  
逆に、fast-forwardできるときでも、
```sh
$ git merge --no-ff branch-1
```
とすると、fast-forwardせずに新しくコミットをつくってくれるので、分岐があったことが履歴でわかりやすくなる。  
![no-ff2](image/part7/no-ff2.bmp)  
## [--squash]オプション
マージには`[--squash]`オプションというものがある。これは、ワークツリーとインデックスを別ブランチの根本からたどっていき書き換えるが、コミットまではしない状態にもっていく操作である。  
![squash](image/part7/squash.bmp)  
この状態ではまだコミットは作られていないので変更飲みされている状態である。  
```sh
$ git merge --squash branch-1
```
とするとワークツリーとインデックスが書き換えられ
```sh
$ git status
On branch master

Changes to be committed:
    modified : sample.c
```
のような状態になる。したがって、
```sh
$ git merge --squash branch-1
$ git commit -m "squash merged"
```
とすれば  
![squash1](image/part7/squash1.bmp)  
のような状態にすることができる（履歴がスッキリ）。  

# プルの仕様
プルでリモートリポジトリからローカルリポジトリに変更点を写すことができるが、実はこれは二段階の工程を踏んでいる。どういうことかというと
```sh
$ git pull origin master
```
と
```sh
$ git fetch origin master
$ git merge origin/master
```
は同義である。  
`git fetch` ではローカルリポジトリにまだないコミットデータをダウンロードしてくる。今回masterをリモートリポジトリからとってきたので「origin/master」というところに記憶される。  
![fetch](image/part7/fetch.bmp)  
次に`git merge`すると、origin/masterをmasterにマージする（この場合fast-forwardになる）。  
![fetch1](image/part7/fetch1.bmp)  
これで実質pullと同じ動きをすることになった。  
前回、プルリクエストの競合を直すときにpullで競合を起こした。これはorigin/masterブランチをfetchしたあとmergeする先のブランチをmasterではなくclone-branchにしていたため競合が発生したということである。  
![fetch2](image/part7/fetch2.bmp)  

# リベース
これまでやってきたGit操作の履歴を見てみると、分岐が多くて見るのが少し大変である。  
![rebase](image/part7/rebase0.png)  
これを少しきれいにするのに便利な操作がリベースである。リベースをすると、複数に分岐していたコミット履歴を直線的な履歴に書き換えることができる（これが良いか悪いかという話はおいておいて）。
## git rebase  
リベースの流れを図を用いて確認する。  
![rebase1](image/part7/rebase1.bmp)  
上記の状態を普通にマージすると   
親コミットを２つ持つ新しいコミットが生成される。これを履歴で見ると分岐して合流するというあまり見やすくはない履歴になるだろう。  
![rebase2](image/part7/rebase2.bmp)  
ところが
```sh
$ git checkout branch-1
$ git rebase master
```
とすると  
![rebase3](image/part7/rebase3.bmp)  
というふうに、branch-1で行った変更を追いかけるように順番に、masterの位置から変更していってくれる(branch-1も移動する）。競合するたびにとまるので、止まったときは競合を解決したあと、  
```sh
$ git add <filename>
$ git rebase --continue
```
とすればrebaseの続きから再開してくれる。
こうしてbranch-1の先端の変更と同じ変更まで追いつくと、masterブランチの先のほうにもbranch-1の変更と同じ変更が適用されたコミットが完成する。当然branch-1のコミット履歴を見れば一直線の見やすい履歴になっていることだろう。あとは「master」に「branch-1」をmergeしてあげれば良い（fast-forwardになる）。  
mergeとrebaseでそれぞれ分岐したブランチを統合したときの図は次のようになる。  
![rebase4](image/part7/rebase4.png)  
分岐していた部分がmasterの上にそのままくっついた形になったいるのがおわかりいただけるだろうか。  
## git rebase -i
このコマンドを使うと、複数のコミットを一つにまとめることができる。  
たとえば  
![rebase-i](image/part7/rebase-i.bmp)  
branch-1において２回コミットし、それぞれ別の言葉を追加していたとする。しかしこの２つのコミットは似たような作業をしただけなので、一つにまとめてしまいたい。こういうときに
```sh
$ git rebase -i HEAD~~
```
とすると、  
```sh
$ git rebase -i HEAD~~
```
```
pick eaa773d <commit message1>
pick fba24fd <commit message2>
（略）
```
とテキストエディタが起動して表示されたはずだ。ここで、二つ目の「pick」を「squash」または「s」に書き換えてエディタを終了してみる。  

```
pick eaa773d <commit message1>
s fba24fd <commit message2>
```

すると、再度エディタが起動し、今度はコミットメッセージの編集ができる。

```
# This is a combination of 2 commits.
# The first commit's message is;

add hello

#This is the 2nd commit message:

add good evening

（略）
```
これを書き換えて
```
add hello and good evening
```
とすれば、前２つのコミットをまとめることができる。  
まとめるコミットは２つでなくてもいいので、例えば
```sh
$ git rebase -i <branchname> HEAD~4
```
とすれば４つのコミットをまとめることもできたりする。当然ログも短くなっている。  
![rebase-i1](image/part7/rebase-i1.png)  
このように、ログが短くなりコミットメッセージは更新されている。  
また、`git rebase -i`はコミットをまとめるだけでなく編集も可能である。  
```sh
$ git rebase -i HEAD~~
```
としたあとに表示されるエディタで、「pick」のところを先ほどは「squash」に書き換えたが今度は「edit」に書き換えてみると  
```sh
Stopped at fba24fd...  add good evening
You can amend the commit now, with

  git commit --amend

Once you are satisfied with your changes, run

  git rebase --continue
```
と表示されるはずだ。これであとは好きなように編集して、満足したら`git commit --amend`する（`git commit`はしなくて良い、すでにあるコミットを書き換えるため）。まだrebaseは終わっていないので「git rebase --continue」で続きを促すと  
```sh
$ git add sample.c
$ git commit --amend
$git rebase --continue
```
これで指定の内容のコミットを修正することができた。ログで変更されていることを確認できる。ちなみに、rebaseを途中でやっぱりやめたくなった場合は
```sh
$ git rebase --abort
```
とすればそれまでの作業を取り消して中断できる。    
![rebase-edit](image/part7/rebase-edit.png)  
上図の場合はコミットメッセージが修正できていることが確認できる。

## リベースの怖いところとお約束
リベースは履歴をきれいにしたり、間違ってコミットしたものを修正したり余計にコミットしたものをまとめたりできる便利な道具である。しかし同時に、行っていることはコミット履歴の改ざんにほかならない。バグの生まれた箇所を見つけにくくなるし、誰がどこでどう分岐して変更したのかが正確にはわからなくなる（コミットメッセージなどでだいたいはわかるが･･････）。使い所をよく考える必要があるだろう。  
## 一度コミットしたものをリベースする
一度コミットしたものをリベースすると、すでにリモートリポジトリに保存されているコミットの履歴と内容があわなくなってしまう。したがって一度コミットしたものをリベースして書き換えた後再度pushすると、コミット履歴が途中まですら一致しなくなり失敗してしまう。ここで、強制的にpush（ローカルリポジトリのコミット履歴をリモートリポジトリに反映する）には  
```sh
$ git push -f <リモートリポジトリ> <ブランチ名>
```
とすること可能だ。  
とはいえ、一度コミットしたものを書き換えるのはあまり推奨されない。なぜなら、コミットを編集することでコミット名（ハッシュ値）が変わり、リモートリポジトリとローカルリポジトリのコミット履歴の間で大きな不一致が発生してしまうからだ。一度コミットしたものはrebaseせず、自分の作業ブランチを管理者が見やすくするために予めrebaseでコミットをまとめたり分岐をなくしたりしてからコミットする程度にrebaseの利用は留めるのがベターかもしれない（あるいはrebaseの利用基準をチームで明確に決めておく）。

# まとめ
今回はマージやリベースなど、コミット操作について少し詳しく説明した。  
これらの作業は更新履歴をいじる危険な作業であるから慎重に行うべきである。仕様をしっかり理解しておかしなバグを産まないようにしたい。

# 最後に
長かったGit入門もここまで。何分自分もGit初心者なのでいろいろ整理しながら書くことができてよかった。アウトプットの大切さがよくわかる。ここまでわかればGitで結構な操作ができるんじゃないだろうか。一応自分なりにジャンル分けしたコマンド一覧を作ったのでそちらのURLも貼っておく。  

[Qiita: 【Git】コマンドチートシート (https://qiita.com/mattyan1053/items/a3f9f7dce794d200c07d)](https://qiita.com/mattyan1053/items/a3f9f7dce794d200c07d)  

それでは皆さん良い開発ライフを。