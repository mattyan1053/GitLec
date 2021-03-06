目次

<!-- TOC -->

- [この記事の概要](#この記事の概要)
- [本記事の対象](#本記事の対象)
- [本記事で使用する環境](#本記事で使用する環境)
- [前準備](#前準備)
    - [新規プロジェクトの作成](#新規プロジェクトの作成)
    - [リモートリポジトリの準備](#リモートリポジトリの準備)
    - [Git設定](#git設定)
- [プロジェクトにローカルリポジトリを設定する](#プロジェクトにローカルリポジトリを設定する)
- [変更をコミットする](#変更をコミットする)
- [ブランチの操作](#ブランチの操作)
    - [ブランチの新規作成](#ブランチの新規作成)
    - [ブランチの切り替え](#ブランチの切り替え)
- [ログの表示](#ログの表示)
- [ブランチのマージ](#ブランチのマージ)
    - [マージの競合を解決する](#マージの競合を解決する)
- [リセット](#リセット)
- [リモートリポジトリをリポジトリに設定する](#リモートリポジトリをリポジトリに設定する)
- [プッシュ](#プッシュ)
- [プル](#プル)
- [クローン](#クローン)
- [コンソールでGitを扱う](#コンソールでgitを扱う)
- [最後に](#最後に)

<!-- /TOC -->

# この記事の概要
Gitの基本的な昨日をVisual Studio Community 2017を使って利用する方法を、コンソールで操作するGitと比較しつつ説明する。「コミット」「マージ」などの言葉はすでに多少は理解している前提で進むので注意。仕組み概要図は「Git入門」のほうに図で説明してあるのでそちらを参照してほしい。  

# 本記事の対象
本記事ではGitの機能をVisual Studioで使いたい方が対象だ。Gitの大まかな仕組みを理解していて、かつVisual Studioの使い方がだいたいはわかるくらいであれば読めると思う（というよりそれでわかる記事を目指している）。  

Gitの理解度については、コンソールで実際に操作できなくても、「コミット」や「マージ」、「ブランチの切り替え」など、言葉をきいて何するかわかっていれば問題ない。何かの操作についてそれぞれ対応するコンソール操作も比較のため掲載するが、わからなければ読み飛ばしてOK。

# 本記事で使用する環境

1. Windows10 Home
1. Visual Studio Community 2017
1. Git for Windows version 2.18.0.windows.1
1. [Github](http://github.com)

OSについてはあまり問題ではない。Visual Studioがまともに動けばいいのではないだろうか。  
今回使用するVisual Studio（以下VS）はCommunity 2017だ。学生とか個人開発者であれば無償で使うことができる。これ以前のVSでも機能がついてたりするようだけど筆者がVisual Studio Express 2013とこの2017しか使ったこと無いので割愛。まあ新しい方で許して。 
Gitはまあメジャーなやつ。CygwinのやつとmsysGitのどちらを使うかということだけれどVSなら後者一択。まあGit for Windowsで問題ない。  
VSならリポジトリサービスはVisual Studio Onlineというのがあるのだけど、ここでは有名で利用者の多いGitHubを採用。まあみんながみんなVS使ってるとは限らないしね（当然VS以外からも利用できるけどVS以外からの利用がメジャーでない）。  

# 前準備
実際にVSでのGit操作を説明するにあたり、今回Git管理したいプロジェクトと、GitHubにリモートリポジトリを作成しておく。

## 新規プロジェクトの作成
普通に新規作成すればOK。途中、「新しいGitリポジトリの作成(G)」というのがあるけれど、今回はあとから作成する（途中まで作業を進めたプロジェクトでもあとからGit管理する方法もわかるため）。  
![prj](image/VSGit/mkprj.png)  
今回はC++で例を進めていく（自分の都合）。

## リモートリポジトリの準備
GitHubで新しくリポジトリを作成しておく。今回は「vs-git-test」という名前で作成した。  
詳しいやり方はここでは説明を省く。詳しくはここへ  

[リモートリポジトリをつくる](part5.html)  

## Git設定
VSを起動して、Gitの設定をしておく。具体的に言うとユーザー名とか。メニューバーの「表示」から「チームエクスプローラー」を開いて、「設定」から各情報を記入する。  
![setting](image/VSGit/setting.png)  
差分、マージツールはとりあえずVisual StudioでOK。もしほかのものを使いたい場合はGitの「.gitconfig」とかのほうから設定してみるとかしよう。

これで前準備は完了。

# プロジェクトにローカルリポジトリを設定する
まずはこのプロジェクトにローカルリポジトリを設定しなければならない。コンソール時は次のようにした。  
```sh
$ git init
```
VSでプロジェクトにローカルリポジトリを設定するには、「ソリューション名のところを右クリック」→「ソリューションをソース管理に追加」とする。  
![mkrpg](image/VSGit/mkrpg.png)  
これでローカルなGitリポジトリが作成され、更に最新の状態がコミットされた状態になる。ソリューションエクスプローラーにはファイルなどに鍵マークのようなものがついていることが確認できるだろう。実際にVSの作業ディレクトリを見てみると  
![mkrpg2](image/VSGit/mkrpg2.png)  
と、「.git」フォルダが作成されており、更に「.gitignore」や「.gitattributes」までもが追加されている。「.gitignore」の中身を見てみると、「Debug」フォルダや一時ファイルなど管理しなくて良いものは既に記入された状態になっている。とてもありがたい。  
更に、コミット履歴を見てみると（確認の仕方は後述）、  
![mkrpg3](image/VSGit/mkrpg3.png)  
と、既に二度コミットされており、最初は「.gitignore」、「.gitattributes」のコミットがされた後、プロジェクトがコミットされていることが確認できる。また、ブランチは当然「master」であることもわかる。  
>余談であるが、当然コマンドプロンプトなどから`git log`や`git status`として確認することも可能。

# 変更をコミットする
なにか変更を加えたとしよう。それが完了したらコミットをしてみる。  
チームエクスプローラーから「変更」をクリックするとコミットメッセージ入力画面などが表示される。  
![commit](image/VSGit/commit.png)  
必要事項を入力し、「すべてをコミット」をクリックすると、すべての変更をコミットすることができる。コミットするブランチはコミットメッセージの上に表示されている。実質これ。 
```sh
$ git add .
$ git commit
``` 
>ちなみに、一部だけをコミットしたい場合は、「すべてをコミット」の下部に表示されている「変更」のところにある変更されたファイルを右クリックして個別に「ステージ」することができ、ステージしたものだけをコミットすることも可能だ。逆に、「変更」の右部に表示されている「＋」をクリックすればすべての変更をステージするので、そのあと「ステージ済みのファイル」からコミットしたくないファイルを右クリックして「アンステージ」すればコミット内容から外すことができる。

# ブランチの操作
## ブランチの新規作成
Gitの強みであるブランチの操作について説明する。まずはチームエクスプローラーから「ブランチ」をクリック。そのあと左上の「新しいブランチ」をクリックする。  
![branch](image/VSGit/branch.png)  
ここで新規に作成したいブランチ名を入力し、下のプルダウンメニューでは派生元のブランチを選択する（ここではまだmasterブランチしかないので変更することはない）。その下の「ブランチのチェックアウト」にチェックをいれておけば新規ブランチを作成したあとそちらのブランチに移行してくれる。  
すると、次からチームエクスプローラーで「ブランチ」を開いたときは  
![branch2](image/VSGit/branch2.png)  
となっており、新しいブランチが反映されているはずだ。現在アクティブなブランチは、ソリューション名の横に表示されている。  
```sh
$ git checkout -b <newbranch>
```
とやってることは同じ。
## ブランチの切り替え
画像を貼るのも面倒なくらい簡単で、チームエクスプローラーから「ブランチ」を選択し、アクティブにしたいブランチ名のところをダブルクリックするだけである。  
```sh
$ git checkout <branchname>
```
と同じ。  

あと実は現在のブランチは画面の右下に表示されていて、ここから変更できたりもする。  
![right_bottom](image/VSGit/right_bottom.png)  

# ログの表示
ログの表示をするにあたって次のように変更し、コミットした。  

- masterブランチの「Source.cpp」
```cpp
#include <iostream>

int main() {
	std::cout << "Hello World!" << std::endl;
	std::cout << "Let's commit!" << std::endl;
	std::cout << "Now branch is master!" << std::endl;
	return 0;
}
```

- developブランチの「Source.cpp」
```cpp
#include <iostream>

int main() {
	std::cout << "Hello World!" << std::endl;
	std::cout << "Let's commit!" << std::endl;
	std::cout << "Now branch is develop!" << std::endl;
	return 0;
}
```
察しのいい人ならわかってしまうかもしれないが、次のマージの説明で競合させるための下準備だったりする。  
さて、まずはmasterブランチのログを表示してみる。チームエクスプローラーの「ブランチ」から「master」ブランチを選択して右クリックして、「履歴を表示」とすると、masterブランチの履歴のみが新しいウィンドウで表示される。  
![log](image/VSGit/log.png)  
同様に、他のブランチのログも表示することができる。  
```sh
$ git log --oneline --graph
```
に近い表示。  

# ブランチのマージ
VSを用いてマージを行ってみる。先ほどのmasterブランチにdevelopブランチをマージしてみよう。  
チームエクスプローラーから「ブランチ」をクリックして、「マージ」のところをクリックする。  
![merge](image/VSGit/merge.png)  
あとはマージ元とマージ先を選択しマージ。問題なくマージできればこれでマージは完了。ログを確認するとうまくいっているはずである。  
```sh
$ git merge <branch>
```
と同じ。
## マージの競合を解決する
今回の例では競合が発生する。これの解決方法を説明する。  
マージで競合が発生すると次のように表示される。  
![conflict](image/VSGit/conflict.png)  
「競合」と表示されているところをクリックすると、競合一覧が出てくるので、ひとつずつ解決していく。  
競合しているファイルをクリックして「マージ」をクリックすると差分が表示されるので  
![conflict2](image/VSGit/conflict2.png)  
![conflict3](image/VSGit/conflict3.png)  
各ファイルから残す部分、削除する部分を選択していく。完了したら左上の「マージを許可」をクリック。これを競合しているすべてのファイルに対して行っていく。それができたら  
![conflict4](image/VSGit/conflict4.png)  
チームエクスプローラの「マージをコミット」をクリックして、  
![conflict5](image/VSGit/conflict5.png)  
いつものコミットのようにメッセージを入力してコミット。これで競合を解決してマージが成功した。  
実際に、ログを確認してみると  
![mergelog](image/VSGit/mergelog.png)    
うまくいっていることがわかる。  
>リベースもほぼ同様の手順で可能である。

# リセット
リセットで指定のコミットに戻ることも可能である。方法は、コミットログを開いて戻りたいコミットで右クリック→「リセット」→「[option]」とするだけ。オプションは必要なものを選ぼう。  
![reset](image/VSGit/reset.png)  
裏では  
```sh
$ git reset [option] <commit>
```
をやってるだけ。

`--mixed`と`--hard`の違いは次を参照  

[ローカルリポジトリの操作３](part4.html)

# リモートリポジトリをリポジトリに設定する
チームエクスプローラから「設定」→「リポジトリの設定」→リモートにある「追加」でリモートリポジトリの設定を入力する。リポジトリ名は「origin」として、フェッチ、プッシュのところはGitHubに指定されたものを入力。今回の例ではSSHを使っている。  
![remote](image/VSGit/remote.png)  

グローバルなユーザー名を使用するかは任意。チェックを入れれば最初のほうで設定したものを利用することになる。リポジトリごとに個別に設定したい場合はここで。

```sh
$ git remote add <remotename> <adress>
```
と同じ。ユーザー名とかは`git config`の設定に`--global`をつけてるかどうかって感じ。  

# プッシュ
さてローカルで行った変更をプッシュしてみよう。チームエクスプローラーから「同期」を選択する。すると現在のブランチ名とプッシュ、フェッチなどが表示されるので「プッシュ」をクリック。するとリモートリポジトリに現在のブランチの内容がプッシュされる。  
![push](image/VSGit/push.png)  
他のブランチをプッシュしたいときは「ブランチ」でアクティブブランチを変更してからプッシュしよう。  
当然GitHubにいけば内容が確認できる。  
![github](image/VSGit/github.png)  

コンソールで言う
```sh
$ git push origin <branch>
```

# プル
もうお気づきだと思うがほとんどプッシュと同じでプッシュのかわりにプルをクリックするだけである。すると現在のブランチに当たる部分がプルされてくる。  
![pull](image/VSGit/pull.png)  
今回の例では何もせずそのままプルしたので最新ですって言われる。  
コンソールで言う
```sh
$ git pull origin <branch>
```
にあたる。
>**フェッチ**について  
フェッチの詳しい説明は割愛するが、もしかしたら使いたい時もあるかもしれない。この場合はフェッチをクリックしたあと、ブランチからフェッチしてきたコミットをマージすることでプルができる。  
![fetch](image/VSGit/fetch.png)  



# クローン
当然リモートリポジトリからリポジトリをクローンしてきて使いたいときもある。 
そのときは、上の方のコンセントのようなマーク（接続）から、ローカルリポジトリの「複製」のところをクリックし、クローンしたいリポジトリのURLを入力すればOK。  
![clone](image/VSGit/clone.png)  
URLはGitHubの場合、リポジトリのページの「Clone or Download」のところから取得できる。
```sh
$ git clone <url> <filename>
```
と同じ。

# コンソールでGitを扱う
これまでの操作は、あくまでGitの操作がGUI化してただけで当然コンソールからも行うことができる。このとき、操作したあとVSに戻ると  
![console](image/VSGit/console.png)  
と言われるかもしれないがおとなしくすべて適用すればOK。

# 最後に
以上、Visual StudioにおけるGitの使い方である。サークルのほうでC講座をやるときVisual Studio使ってるので需要あるかなとおもって書いてみた。  
そんなに複雑な操作はないと思う。細かい原理理解してなくても必要な機能使えるしね。
