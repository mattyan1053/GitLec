目次

<!-- TOC -->

- [前回の続き](#前回の続き)
- [リポジトリに記憶された指定のコミットに移動する](#リポジトリに記憶された指定のコミットに移動する)
- [コミットの分岐](#コミットの分岐)
    - [ブランチを扱う](#ブランチを扱う)
        - [ブランチって何](#ブランチって何)
        - [実際にブランチをつくる](#実際にブランチをつくる)
        - [ブランチをマージする](#ブランチをマージする)
            - [マージの競合](#マージの競合)
- [まとめ](#まとめ)

<!-- /TOC -->

# 前回の続き
前回は変更したファイルを「変更したリスト」に追加（ステージ）して、それを記憶（コミット）するまでの流れをやった。実はこのコミットしたことで「記憶のかたまり」がつくられる。  
今回はこのコミットしたものたちをどう扱っていくか説明していく。  
今後、この「コミットすることでできた記憶のかたまり」を「コミット」と呼んでいく。

# リポジトリに記憶された指定のコミットに移動する
前回、作ったファイルをコミットする（記憶する）ことでバックアップのようなものをとることができた。今回は、それをどのように利用していくか考える。  
まず、準備として追加で２回ほど前回の「sample.c」を編集してステージ（変更リストに追加）してコミット（記憶）する。(例ではVimを使用）  
sample.cを編集して
```sh
$ vim sample.c
```
```c
#include<stdio.h>
int main(){
    printf("Gitコマンド一覧\n");
    printf("git init : リポジトリを作成\n");
    printf("git status : Gitの状態を確認\n");
    printf("git add : ファイルのステージ\n");
    printf("git commit : コミットする\n");
    return 0;
}
```
コミットしたあと、もう一度sample.cを編集する
```sh
$ git add sample.c
$ git commit -m "Second commit"
$ vim sample.c
```
```c
#include<stdio.h>
int main(){
    printf("Gitコマンド一覧\n");
    printf("git init : リポジトリを作成\n");
    printf("git status : Gitの状態を確認\n");
    printf("git add : ファイルのステージ\n");
    printf("git commit : コミットする\n");
    printf("git log : コミット履歴を見る\n");
    return 0;
}
```
コミットする。
```sh
$ git add sample.c
$ git commit -m "Third commit"
```
こうしてコミット（記憶）を続けていくと、次の図のようになる。四角形一つ一つがコミットであると捉えてほしい。  
![commit](image/part3/commit.bmp)  
コマンドで見るとこんな感じ
```sh
$ git log --oneline --graph
* 0d5bf59 (HEAD -> master) Third commit
* 393f3a1 Second commit
* f0c1e0e first commit
```
HEADというものが最新のコミット(ver3)のところに書いてあるが、詳しいことはおいておいて今は現在どの状態かを指し示しているものだと考えよう（正確には正しくないが今はこの考え方で進める）。  
このヘッドの位置をコントロールすることで現在の作業ディレクトリの状態をコントロールすることができる。  
現在の状態でsample.cの中身を見てみると（`cat`コマンドで中身が見られる）
```sh
$ cat sample.c
#include<stdio.h>
int main(){
    printf("Gitコマンド一覧\n");
    printf("git init : リポジトリを作成\n");
    printf("git status : Gitの状態を確認\n");
    printf("git add : ファイルのステージ\n");
    printf("git commit : コミットする\n");
    printf("git log : コミット履歴を見る\n");
    return 0;
}
```
当然こうなっているはずである。しかしここで
```sh
$ git reset --hard HEAD~
```
とすると（HEADの後ろについているのはチルダ「~」）
```sh
$ cat sample.c
#include<stdio.h>
int main(){
    printf("Gitコマンド一覧\n");
    printf("git init : リポジトリを作成\n");
    printf("git status : Gitの状態を確認\n");
    printf("git add : ファイルのステージ\n");
    printf("git commit : コミットする\n");
    return 0;
}
```
となり、Third commitする前の、ver2のsample.cになっているはずだ。つまり何が起きているかと言うと  

![commit2](image/part3/reset.bmp)  
HEADが一つ前のものに移動しているということになる。一つ前のところに戻せるというわけだ。  
ちなみに、「HEAD」のところをうまいことやれば他の場所に移動もできる。「HEAD」にすればver3の部分を指し示すことになるので、最後にコミットしたときの状態に一致することになる。また「HEAD~」の「~」の数を増やしたり、「~2」としてみると戻る数を指定できる。たとえば
```sh
$ git reset --hard HEAD~2
```
とすればHEADはver3からver1のところに移動する。  
この`git reset`の注意しなければならないところはHEADを交代させるとその先のver3のところは`git log`では表示できず、現在位置からの相対位置（HEADをいくつずらすか）ではもとに戻ることができない（理由はあとで詳しく説明する）。もし一度resetしたものを取り消してもとのresetする前の状態に戻したいときは
```sh
$ git reset --hard ORIG_HEAD
```
とすると一度前のresetの操作を取り消せる。  
他に、直接ある指定のコミットへHEADを移動する方法があり、HEADの移動ログをたどる
```sh
$ git reflog
```
などからHEADの移動履歴を見ることができ、コミットのハッシュ値（先頭のほうに表示されるごちゃごちゃの英数字）を直接入力すれば移動できる。ハッシュ値とは、どのコミットを指し示すかわかるようにするための名前のようなものと捉えて良い。（下図参照）
![head_move](image/part3/head_move.bmp)  
ここまでの操作をしてみると、Gitがバージョン管理ソフトとして機能する過程の片鱗を見ることができたのではないだろうか。`git log`で見たコミットメッセージ（変更内容）をみて、戻りたい位置に任意で戻ることができるわけだ。  
これでコミットさえしておけばその時点まで巻き戻したりもとに戻したりすることが容易にできるようになった。

# コミットの分岐
先程までのコミットは一直線型に変更を続けていった。  
ところが実際に開発を進めていると、バグの修正だったり、他の人と協力してやるためにバージョンが少し分岐したりすることがあるだろう。そういうときの対処法について学ぶ。  

先程と同様に、コミットを３回した状態を考える。(ヘッドはVer3を指している状態）  
![branch](image/part3/branch0.bmp)  
現在それぞれのコミット（記憶したもの）は一直線上に並んでいるが、実はこれを分岐させることができる。そのために必要な考え方が「ブランチ」というものだ。
## ブランチを扱う
いつもどおりブランチがなんなのかということはさておき、「branch」という文字は見たことがあるかもしれない。そう、`git status`と入力したときに一番上に出てきたアレだ。
```sh
$ git status
On branch master
nothing to commit, working tree clean
```
一番上の「On branch master」である。  
とりあえず`git branch`と入力してみてほしい。すると次のようになるだろう。
```sh
$ git branch
* master
```
`git status`でみた「master」という文字が表示されたはずだ。これは現在いる「ブランチ」が「master」であることを示している。
### ブランチって何
それでは後回しにしていたブランチについて説明する。コミット（記憶したもの）を分岐させる、ということは次の図のようになる。  
![branch1](image/part3/branch1.bmp)  
さて、コミットを枝分かれさせたはいいものの、自分が今どの枝にいるのかわからないと困ってしまう。これを解決するのが「ブランチ」である。それぞれの枝に次のように名前をつけてみる。  
![branch2](image/part3/branch2.bmp)  
これで枝を区別できるようになった。この枝の名前を「ブランチ」と呼び、細かく言えば枝全体でなく、枝の先端を指し示すものとなる。ちなみにGitのデフォルトのブランチ名（真ん中の枝といってよい）は「master」である。サーヴァントは召喚しません。
### 実際にブランチをつくる
ブランチがなんとなくイメージできたところで実際に操作を行ってみよう。  
ヘッドの移動をやったときの状態  
![commit](image/part3/commit.bmp)  
からスタートしてみる。今回はVer3のところから分岐したいとしよう。まずヘッドをVer２のところに移動する。`git log`でコミット履歴を確認してみると、Ver3のところがなかったことのようになっているだろう。
```sh
$ git reset --hard HEAD~
```
![reset](image/part3/reset.bmp)  
ここから分岐させていく。ちなみにこの時、ブランチ（枝）「master」の指し示す位置はVer2であることに注意したい（ヘッドと一緒に移動する）。まずは新しくブランチ（枝）を作る。  
```sh
$ git branch test-branch
```
これで新しく枝を生やすことができた。図で表すと  
![branch3](image/part3/branch3.bmp)  
コマンドで実際に今ある枝を確認してみると
```sh
$ git branch
* master
  test-branch
```
と表示されるだろう。ここで、「*」がついているブランチ（枝）は今伸ばそうとしている（今現在いる）ブランチである。分岐させて「test-branch」を伸ばすには伸ばす枝を変更せねばならない。そこで次のようにコマンドを入力する。
```sh
$ git checkout test-branch
```
これで現在いるブランチ（枝）を確認すると
```sh
$ git branch
  master
* test-branch
```
と表示されたことだろう。次に、分岐させる前に作業ディレクトリに何かしら変化がなければ分岐させていく意味がないので新しくファイルを作ってみる。
```sh
$ vim branch.c
```
```c
#include<stdio.h>

int branch_name(){
    char branch[] = {"test-branch"};
    printf("追加したブランチ : %s\n", branch);
    return 0;
}
```
と追加したブランチも表示するコードを書いた。  
この結果をコミット（記憶していく）。このとき「add」するのは「branch.c」のみで問題ない。なぜなら「sample.c」には変更を加えていないからだ。  
```sh
$ git add branch.c
$ git commit -m "add new file to print branch name"
```
現在の状態を図で表すと
![branch4](image/part3/branch4.bmp)  
これで枝分かれすることができた。  
次に、分岐することができてももとのブランチ（枝）に戻ることができなければ意味がないので戻ってみる。操作はブランチを「test-branch」に切り替えたときと同様に「master」に切り替えるので
```sh
$ git checkout master
```
とすれば「master」ブランチに移動できる。なので実際にワークツリー（作業ディレクトリ）の中身を見てみると

- Windowsの場合
```dosbatch
> dir
（略）
2018/7/20 23:05                     .gitignore
2018/7/21 18:13                     sample.c
（略）
```
- Mac, Linuxの場合
```sh
$ ls
sample.c     .gitignore
```

というようにVer２のときのワークツリー（作業ディレクトリ）の内容になっているだろう。更にVer３に移動したいので`git reflog`でVer３のハッシュ値を確認する。
```sh
$ git reflog
<ハッシュ値> (HEAD-> master) HEAD@{0}: checkout moving from test-branch to master
 （略）  
<ハッシュ値>HEAD@[n]: commit Third commit   
 （略）
```
このときnは何回前のヘッド移動かを表す。  
これでVer３のコミットのハッシュ値が確認できたので移動してみる。
```sh
$ git reset --hard <３回目のハッシュ値>
HEAD is now at <３回目のハッシュ値> Third commit
```
と表示されるだろう。当然ワークツリーの中身はsample.cと.gitignoreのままであり、sample.cの中身は
```sh
$ cat sample.c
#include<stdio.h>

int main(){
    printf("Gitコマンド一覧\n");
    printf("git init : リポジトリを作成\n");
    printf("git status : Gitの状態を確認\n");
    printf("git add : ファイルをステージ\n");
    printf("git commit : コミットする\n");
    printf("git log : コミット履歴を見る\n");
    return 0;
}
```
となっている。図であらわすと  
![branch5](image/part3/branch5.bmp)  
となっている。  
逆にブランチ「test-branch」に戻れば、ワークツリー（作業ディレクトリ）にはbranch.cが現れて  
```sh
$ git checkout test-branch
$ cat branch.c
#include<stdio.h>

int branch_name(){
    char branch[] = {"test-branch"};
    printf("現在のブランチ : %s\n", branch);
    return 0;
}
```
となる。
つまり、ちょっと分岐して作業したい（バグ修正や機能追加など）ときには`git checkout <ブランチ名>`で別の枝に移動して作業していけばもとの方(master)には影響が出ないわけだ。
### ブランチをマージする
さてバグ修正など分岐して作業を進めていて、その作業が完了したとする。この完了した分岐データを「master」のほうに統合していかなければ意味がない。この修正内容を大元の「master」でも反映するということだ。このブランチ（枝）を合流させることを「マージ(merge)する」という。  
図で言うと  
![merge](image/part3/merge0.bmp)  
「master」のほうで変更した「sample.c」を保持したまま「test-branch」の「branch.c」を追加したver4を作るという流れである。  
「test-branch」のほうの「sample.c」はもともと「master」のver2の「sample.c」と同じであるので、「変更なし」ということになり、ver3のほうの「sample.c」がそのまま残る（「master」と「test-branch」の両方で「sample.c」に異なる変更を行った場合「競合」が起きてしまうがこれはあとで説明する）。  
さて実際にマージをしてみよう。「master」のほうに「test-branch」を合わせる形なので、まず現在のブランチ（枝）を「master」に変更する（「master」に「test-branch」を統合するのと、「test-branch」に「master」を統合するのでは意味合いが異なる。どちら側に合流させるのか気をつけよう）。
```sh
$ git checkout master
```
次に、「master」に「test-branch」を合流（マージ）させる。
```sh
$ git merge test-branch
```
これで`dir`や`ls`をして実際のファイルを見てみると、「branch.c」が追加されていることがわかるだろう。ログを見てみると
```sh
$ git log --oneline
```
「Merge branch 'test-branch'」というコミットが追加されているのがわかる（上の図で言うver4）。更に面白いことができて、「--graph」オプションをつけてみると、  
```sh
$ git log --oneline --graph
```
としてみると、図を頑張って文字と棒線使って表したようなものを見ることができる。  
![merge1](image/part3/merge1.png)  
一応GitのGUI（グラフィカルな感じのやつ）もあるので
```sh
$ gitk
```
と起動してみてみると  
![merge2](image/part3/merge2.png)  
とうまく合流している図を見ることができる。これで分岐させて作業していたものを本流に合流させて反映することができるようになった。  
#### マージの競合
さて、これで実は基本的なバージョン管理はできるようになったのだが、いくつか問題が発生する。その一つが「マージの競合」だ。実例を見てみよう。現在「test-branch」で作業中だ。
```sh
$ git checkout test-branch
```
先程マージしてみる前の状態を見てみる。  
![branch5](image/part3/branch5.bmp)  
このとき「test-branch」にあるsample.cを更に書き換えたとしよう。  
```sh
vim sample.c
```
```c
#include<stdio.h>

int main(){
    printf("Gitコマンド一覧\n");
    printf("git init : リポジトリを作成\n");
    printf("git status : Gitの状態を確認\n");
    printf("git add : ファイルをステージ\n");
    branch_name();
    return 0;
}
```
これをコミットする。
```sh
$ git add sample.c
$ git commit -m "add branch_name() in sample.c"
```
このとき、「master」にある「sample.c」と「test-branch」にある「sample.c」では内容が異なる。  
この状態で「master」に「test-branch」をマージしてみる。  
```sh
$ git checkout master
$ git merge test-branch
Auto-merging sample.c
CONFLICT (content): Merge conflict in sample.c
Automatic merge failed; fix conflicts and then commit the result.
```
受験でみたような気がする英単語「conflict」。音ゲーでみたような気がする「conflict」。要は競合あったので失敗しましたという意。修正してコミットし直してねって言われるので修正する。
```sh
$ vim sample.c
```
お好みのテキストエディタで「sample.c」を開く。すると  
![conflict](image/part3/conflict.png)  
というように差分（変化した部分）を挿入してくれている。後はうまい具合になるように修正。今回はどちらの内容も残したいので単純に
>&lt;&lt;&lt;&lt;&lt;&lt;&lt; HEAD  
&#61;&#61;&#61;&#61;&#61;&#61;&#61;  
&gt;&gt;&gt;&gt;&gt;&gt;&gt; test-branch  

の部分を削除して
```c
#include<stdio.h>

int main(){
    printf("Gitコマンド一覧\n");
    printf("git init : リポジトリを作成\n");
    printf("git status : Gitの状態を確認\n");
    printf("git add : ファイルをステージ\n");
    printf("git commit : コミットする\n");
    printf("git log : コミット履歴を見る\n");
    branch_name();
    return 0;
}
```
というコードに直す。修正がおわったらいつもコミットするように
```sh
$ git add sample.c
$ git commit -m "Merged sample.c"
```
とすればOK。つまりどういうことかというと、コミットは作らずに、「master」の「sample.c」を書き換えた状態にしてくれるので、あとは適宜書き換えた後普通にコミット（記憶）すればマージ（合流）完了、という流れである。  
実質、「git merge test-branch」を実行したことによって「ver３のコミットの「sample.c」を書き換えて、新しく「branch.c」がステージ（変更リスト登録）された状態にした」ということがわかる。ためしに「git merge test-branch」で警告が出された後に`git status`してみると
```sh
$ git merge test-branch
Auto-merging sample.c
CONFLICT (content): Merge conflict in sample.c
Automatic merge failed; fix conflicts and then commit the result.

$ git status
On branch master
You have unmerged paths.
  (fix conflicts and run "git commit")
  (use "git merge --abort" to abort the merge)

Changes to be committed:

        new file:   branch.c

Unmerged paths:
  (use "git add <file>..." to mark resolution)

        both modified:   sample.c
```
と表示され、「branch.c」がすでにステージ（変更リストに登録）されていて、「sample.c」が書き換わったけどまだステージされていない状態であることが確認できる。

# まとめ
今回はブランチについて詳しく説明した。  
流れとしては  

1. ブランチをつくる
1. 作業を進める
1. マージする

の３ステップを踏むことになる。  
これでGitでバージョン管理を柔軟にすることができるようになった。  
次回はこれまでやってきたことをもう少し詳しく見ていく。具体的にはHEADやブランチの詳しい話をしつつこれらとコミットやステージとの結びつきを解説していくことになる。    