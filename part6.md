目次  

<!-- TOC -->

- [ローカルリポジトリにリモートリポジトリを設定する](#ローカルリポジトリにリモートリポジトリを設定する)
- [プッシュする](#プッシュする)
- [クローンする](#クローンする)
- [プルリクエスト](#プルリクエスト)
    - [修正してもらう](#修正してもらう)
    - [マージする](#マージする)
- [プルする](#プルする)
    - [プルの正体](#プルの正体)
- [プルリクエストの競合](#プルリクエストの競合)
- [まとめ](#まとめ)

<!-- /TOC -->

前回はGitHubでリモートリポジトリを作成した。今回はこれを操作していく。

# ローカルリポジトリにリモートリポジトリを設定する
GitHubで作ったリポジトリのページを開いてCodeのところを開くと次のように出るだろう。  
![remote0](image/part6/remote0.png)  
SSHのところをクリックしたときに横に表示される「git@github.com:<username>/git-test.git」の部分を表示する。  
できたら、次はコマンドでローカルリポジトリのところに移動（今回は「git-test」）して、次のように入力してほしい。
```sh
$ git remote add origin git@github.com:<username>/git-test.git
```
originの後ろは先程表示したものをそのまま入力する。これでGitHubのリモートリポジトリと自分のローカルリポジトリを結びつけることが成功した（間違えて入力してしまった人は`git remote remove <リポジトリの名前(うち間違えてなければorigin)>`としたあとやり直せばOK）。このコマンドについて解説しておくと、現在いるローカルリポジトリを保存するリモートリポジトリを追加するコマンドである。「add」の後ろはリモートリポジトリの名前（デフォルトは「origin」）、その後ろがリポジトリの場所である。  

# プッシュする
次に、今の自分のリポジトリ（ローカルリポジトリ）の「master」ブランチの内容をリモートリポジトリにアップロードしよう。このアップロードすることを「プッシュ」と呼び、
```sh
$ git push origin master
```
とすると、リモートリポジトリにローカルリポジトリの「master」に現在のブランチの内容をプッシュ（アップロード）できる。  
実際、ここでGitHubのほうのリポジトリを開いてCodeのところをクリックすると  
![remote1](image/part6/remote1.png)  
ちゃんと同期がとれていることがわかる。今回は更に「test-branch」も同期しておこう。  
```sh
$ git checkout test-branch
$ git push origin test-branch
```
今回のpush先は「test-branch」なのでこちらを指定。かならずpush元に`git checkout`してからプッシュする。これでブランチが２つとも同期できた。コミットログなどもちゃんと同期されていることをGitHub上で確認できる。  
![remote2](image/part6/remote2.png)  
ここで更に「local/test-branch」上で変更を行って、再度プッシュしてみよう。まず「master」が変更されているかもしれないので、「master」の内容を「test-branch」にマージする（今回は自分しか作業していないので変更されているわけもないが･･････）。  
```sh
$ git merge master
Fast-forward
  sample.c | 2 ++
  1 file changed, 2 insertion(+)
```
となっただろう。Fast-forwardについてはあとで説明するのでまだわからなくても良いが、これが表示されたときは特にmasterが変更されてないときである。  
さて準備が整ったので作業をしていこう。  
まずsample.cを書き換える。  
```sh
$ vim sample.c
```
```c
#include<stdio.h>

int main(){
    printf("Gitコマンド一覧\n");
    printf("git init : リポジトリを作成\n");
    printf("git status : Gitの状態を確認\n");
    printf("git add : ファイルをステージ\n");
    printf("git commit : コミットする\n");
    printf("git log : コミット履歴を見る\n");
    printf("git push : プッシュする\n");
    branch_name();
    return 0;
}
```
一行追加した。これをコミットする。  
```sh
$ git add sample.c
$ git commit -m "add description of push"
```
これをプッシュ（アップロード）する。  
```sh
$ git push origin test-branch
```
実際にGitHubでコミット履歴でブランチを「test-branch」に設定すると  
![remote3](image/part6/remote3.png)  
ちゃんとコミット履歴に追加されていることがわかる。  
プッシュは先程からアップロードと説明していたが、実は少し違う。アップロードときくとディレクトリ全体をリポジトリに保存するようだが、プッシュしてるのはあくまで「コミット」なので、リモートリポジトリにもやはりコミットが追加されて、かつリモートリポジトリのブランチの指す位置が変化する。あくまで指定したブランチ上のコミットをローカルリポジトリとリモートリポジトリで同期しているに過ぎないということである。

# クローンする
今の所作業しているのは一人だが、新たに作業員が加わったとしよう。その人は自分のPC上にこのリモートリポジトリの内容をもったローカルリポジトリを作る必要がある。けれども。「git init」して、このリモートリポジトリのコミットをたどるように自分のPC上にローカルリポジトリを作成するのはとてもバカバカしい作業である。そこで、このリモートリポジトリの内容をコピーしてリモートリポジトリに反映するのが「クローン」だ。クローンという言葉は聞き覚えのある人もいるだろう、同じ遺伝子の子を作り出すアレと同じ意味である。  
今回は仮想的にもうひとりの作業員を用意して、自分がそれになりきることにしよう。まずはGitHubのほうでクローンを作るのに必要なローカルリポジトリの場所を確認しなければならない。GitHubでコードのところを見ると、右の方に「Clone or download」というものがある。ここをクリックして表示される場所をコマンドで入力することになる。  
![clone](image/part6/clone0.png)  
新しくローカルリポジトリを作りたい場所に移動し、次のように入力する。  
```sh
$ git clone <先ほど表示したリポジトリの場所> <クローンをいれるディレクトリ名>
```
今回は
```sh
$ git clone git@github.com:mattyan1053/git-test.git git-test-clone
```
とした。ただし、クローンでできたローカルリポジトリに存在するブランチはデフォルトブランチであるmasterのみである。あとは今までと同様に作業できるので、とりあえず自分用のブランチを作成して編集しておこう。  
```sh
$ git branch clone-branch
$ git checkout clone-branch
$ vim sample.c
```
```c
#include<stdio.h>

int main(){
    printf("Gitコマンド一覧\n");
    printf("git init : リポジトリを作成\n");
    printf("git status : Gitの状態を確認\n");
    printf("git add : ファイルをステージ\n");
    printf("git commit : コミットする\n");
    printf("git log : コミット履歴を見る\n");
    printf("git clone : クローンをつくる\n");
    branch_name();
    return 0;
}
```
クローンを作るコマンドを追加した「sample.c」を作成した。こちらからも当然pushすることができ  
```sh
$ git add sample.c
$ git commit -m "add description of clone"
$ git push origin clone-branch
```
とすれば「sample.c」の変更がプッシュ（アップロード）できる。

# プルリクエスト
一旦ここで現在のリモートリポジトリの状況を図で確認してみよう。  
![pull-req](image/part6/pull_request.bmp)  
ここでtest-branchで作業していた人が「master」にマージしたいとする。通常、ローカルリポジトリで
```sh
$ git checkout master
$ git merge test-branch
```
とすればマージできるのだが、複数人で開発しているときは、勝手に本流である「master」ブランチにマージするのは好ましくない。このリモートリポジトリの管理者に頼んで、チェックしてもらってからマージをする必要がある。そこで登場するのが「プルリクエスト」だ。  
ここでGitHubでCodeを確認してみると右の方に「Compare & pull request」と出ているのが見つかるだろう。  
![pull-req1](image/part6/pull_request1.png)  
今回は「test-branch」のほうをプルリクエストしてみる。  
すると  
![pull-req2](image/part6/pull_request2.png)  
のように表示されるだろう。赤枠で囲った部分はどこからどこへマージするのか、下はリクエストのタイトルとコメントを書く場所である。必要なことが記入できたら「Create pull request」をクリックしよう。  
すると、プルリクエストが作成される。GitHubのリポジトリページのタブの「Pull requests」の部分が「１」と表示され、クリックすればリクエストが表示されるようになる。管理者は、ココからプルリクエストをチェックし、マージするか、修正させるかを決めることができる。
## 修正してもらう
内容を確認するには「File changed」の部分をクリックする。  
![pull-req3](image/part6/pull_request3.png)  
するとファイルの中身が表示される。そこでファイルの内容にカーソルをあわせると「＋」ボタンが表示されるので、クリックすると、メッセージが残せる。必要なことを書いたら「Start a review」をクリックすれば良い。すると会話の履歴が残るので、また「test-branch」で作業している人が修正してプッシュ、同じようにコメントしてくれるのを待つ。  
![pull-req4](image/part6/pull_request4.png)  

開発者側はレビューに従って修正して再度同じブランチにプッシュ。これを問題がなくなるまで繰り返す。
## マージする
すべての問題がなくなったら、「Conversation」タブの下の方にある「Merge pull request」をクリックする。問題なければ緑色のボタンが出ているはずである。  
![pull-req5](image/part6/pull_request5.png)  
確認のように「Confirm Merge」とでるのでこれもクリック。これで無事「master」にマージすることができた。上の方のプルリクエストタイトルの左下に「Merged」と表示されているはずだ。  
![pull-req6](image/part6/pull_request6.png)  
さて、これで管理者は開発者の作ったコミットを本流にマージすることができるようになった。しかし当然マージしようとしたら競合してしまうこともありうる。そこで、競合したときの対処について話たいところだが、「プル」という操作を知らなければ対処できないので、先にプルについて説明する。  

# プルする
現在のリモートリポジトリの状況はこう。  
![pull](image/part6/pull_request7.bmp)  
masterが書き換わっている。このように、寝ている間とか、自分が作業していなくても他の人が作業を進めてリモートリポジトリに変更が加えられている場合がある。この変更を次作業するときはじめにローカルリポジトリのほうにも反映しておきたい。そこで「プル」というものを行う。プルはプッシュとは逆の操作で、ローカルリポジトリに行われた変更をローカルリポジトリにも反映するということだ。ローカルリポジトリに追加されたコミットをローカルリポジトリに追加する操作とも取ることができる。プルするには次のようにする。  
今回は「remote/master」ブランチをそのままローカルの「local/master」ブランチに反映したいので、まず現在のブランチを「master」に変更する。その後プルを行う。  
```sh
$ git checkout master
$ git pull origin master
```
これで「remote/master」の内容が「local/master」にプル（適用）された。このように、プルは、  
```sh
$ git pull <リモートリポジトリの名前> <リモートのブランチ名>
```
というふうに使う。これで実際にログを確認すると  
```sh
$ git log --oneline --graph
```  
![pull](image/part6/pull.png)  
とプルリクエストでマージされた「master」のコミットをローカルリポジトリに反映できていることがわかる。  

## プルの正体
実は、プルというのは「remote/&lt;branchname&gt;」から「local/&lt;branchname&gt;」へのマージである。基本的には同じブランチをマージしていることになるので、競合は起きないが、リモートのブランチからローカルの異なるブランチへ（例えばmasterからclone-branchへ）プルすると競合が起きることがある。

# プルリクエストの競合
現在のリモートリポジトリの状態はこんな感じ  
![pull-request7](image/part6/pull_request7.bmp)  
この絵でわかるかもしれないが、ここで「clone-branch」がプルリクエストを作成し、「master」にマージしようとすると競合が発生する（sample.cが同時に書き換わってしまっている）。  
そこで、管理者側でこの競合をどう解決するかという話だ。  
先ほどと同様の手順で「clone-branch」のプルリクエストを作成する。  
途中、どこからどこにマージするか表示される部分の右に「Can't automatically merge.」と表示されるが気にしないで良い。  
さて管理者になりきってみてみる。Pull requestを開くと次のように表示される。  
![pull-req8](image/part6/pull_request8.png)  
当然「Resolve conflicts」をクリックといきたいところだが（こちらでも解決できる）、ブラウザ上で解決するよりローカルリポジトリで作業したほうが怖くないので今回はこちらでやってみる。  
まずは現在リモートリポジトリに存在する「remote/master」を、「local/clone-branch」にプルしてみると、自動的に「remote/master」から「local/clone-branch」へのマージを試みる。当然、前のプルリクエストによってリモートの「master」は書き換わっているので、競合が発生する。  
```sh
$ git checkout clone-branch
$ git pull origin master
remote: Counting objects: 1, done,
remote: Total 1 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (1/1), done.
From github.com:mattyan1053/git-test
 * branch            master     -> FETCH_HEAD
   5463036..b39b03e  master     -> origin/master
Auto-merging sample.c
CONFLICT (content): Merge conflict in sample.c
Automatic merge failed; fix conflicts and then commit the result.
```
プルのところで正体はマージであるという話をしたが、ここでよくわかるだろう。マージの競合を解決するのと同じ手順で競合を解決する。  
```sh
$ vim sample.c
```
```c
#include<stdio.h>

int main(){
    printf("Gitコマンド一覧\n");
    printf("git init : リポジトリを作成\n");
    printf("git status : Gitの状態を確認\n");
    printf("git add : ファイルをステージ\n");
    printf("git commit : コミットする\n");
    printf("git log : コミット履歴を見る\n");
//<<<<<<< HEAD
    printf("git clone : クローンをつくる\n");
//=======
    printf("git push : プッシュする\n");
//>>>>>>> b39b03e8e0d5e0908abe4475a679aa8d33723bcf
    branch_name();
    return 0;
}
```
消すべき部分の文頭を「//」としてみた。この部分を削除すれば良い。  
これで競合が解決したので、「add」「commit」していく。  
```sh
$ git add sample.c
$ git commit -m "Merged master to clone-branch"
```
あとは再度プッシュする。
```sh
$ git push origin clone-branch
```
これでGitHubに戻ってみてみると  
![pull-req9](image/part6/pull_request9.png)  
競合が解決したので、マージができるようになっている。  
プルリクエストの競合を解決するために行った操作を図に表すと次のようになる。  
![pull-req10](image/part6/pull_request10.bmp)  
これでプルリクエストの競合は解決された。

# まとめ
今回はローカルリポジトリとリモートリポジトリのデータを同期する様々な方法について触れたが、結局のとこ概要は次のとおりである。  

- プッシュ : ローカルリポジトリの変更をリモートリポジトリにも適用
- プル : リモートリポジトリの変更をローカルリポジトリにも適用
- クローン : リモートリポジトリから新しくローカルリポジトリをつくる
- プルリクエスト : 他人のマージと被ってデータを壊さないためにワンクッション置く手法

これらのコマンドを用いてチーム開発をより便利にすすめていくことができる。  
次回はラスト。履歴をコントロールしてみたりマージの仕様だったりといくつか後回しにしていたところを考える。
