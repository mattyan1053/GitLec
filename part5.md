目次

<!-- TOC -->

- [ローカルリポジトリとリモートリポジトリ](#ローカルリポジトリとリモートリポジトリ)
    - [ローカルリポジトリ](#ローカルリポジトリ)
    - [リモートリポジトリ](#リモートリポジトリ)
    - [ローカルリポジトリとリモートリポジトリの関係](#ローカルリポジトリとリモートリポジトリの関係)
    - [実際の開発の流れ](#実際の開発の流れ)
- [リモートリポジトリを作成する（GitHub）](#リモートリポジトリを作成するgithub)
    - [アカウントを作成する](#アカウントを作成する)
    - [公開鍵の設定](#公開鍵の設定)
    - [リモートリポジトリを作成する](#リモートリポジトリを作成する)
- [まとめ](#まとめ)

<!-- /TOC -->

前回までの内容では、自分のPC内で作業を行ってバージョン管理をしてきた。ここからはチームで管理をするためのリモートリポジトリについて考える。

# ローカルリポジトリとリモートリポジトリ
リモートリポジトリを説明するにあたり、まずは今までいじってきたリポジトリについて少し説明する。
## ローカルリポジトリ
今までGit管理してきたリポジトリは「ローカルリポジトリ」と呼ばれるものである。  
ある個人が自分のPC上につくるリポジトリで、使用するのは操作している自分だけである（複数人で使用しているPCの場合はその限りではないが･･････）。

## リモートリポジトリ
これから扱っていこうとしているリポジトリが「リモートリポジトリ」と呼ばれるものである。  
これはオンライン上に保存されているリポジトリで、複数人がそこにアクセスすることにより共有することができるものである。開発者は自分のローカルリポジトリでつくったコミットをここに加えていく（アップロードしていく）ことになる。    
これもリポジトリであるので、当然コミットやブランチなどを持っている。  
通常、みんなが好き勝手にリモートリポジトリの内容を変更していくと訳がわからないことになってしまうので、管理者を誰かしら一人または複数人決めておくことになるだろう。

## ローカルリポジトリとリモートリポジトリの関係
図で表してみよう。たとえば、ローカルリポジトリとリモートリポジトリの「master」ブランチだけが同じ状態だと次のようになる。  
![local-remote](image/part5/local-remote.bmp)  
ブランチがリモートリポジトリのものなのかローカルリポジトリのものなのか明確にするため、ブランチ名の手前に「remote/」や「local/」をつけてみた。ここで、「remote/master」の内容と「local/master」の内容は等しくなっている。  
このように、共有していくブランチを決めておいて、みんなでそこを書き換えるように変更点をアップロードしていけば、チームで開発を進めることができるということだ。  
## 実際の開発の流れ
メインとなるブランチ（ここでは「master」）を変更するというのは重要な行為である。したがって、次のようにリモートブランチを利用してみる。  
あるプロジェクトが始動したとしよう。初期状態は次のようになっている。  
![local-remote-blank](image/part5/local-remote-blank.bmp)  


ここで、Aさんがリモートリポジトリから「master」の内容をコピーし、「local/test-branch-A」で作業を始めたとしよう。このブランチを「Aさんのブランチ」として設定する。さらにAさんはここから分岐して「local/dev-A」ブランチをつくり、そこで作業を行った。最後にAさんは「local/dev-A」を「local/test-branch-A」にマージして、Aさんのブランチである「local/test-branch-A」を「remote/test-branch-A」にアップロードした。このとき、「local/dev-A」ブランチはアップロードされていないのでリモートリポジトリには存在していないことに注意したい。  
![local-remote1](image/part5/local-remote1.bmp)  
ここで、Aさんはリモートリポジトリの管理者に作業が終わったのでmasterにマージしてほしい旨を伝える。そこで、管理者は「remote/test-branch-A」を確認し、問題がなければマージする。  
![local-remote2](image/part5/local-remote2.bmp)  
更にAさんが開発を続ける場合はAさんが「remote/master」ブランチをダウンロードして、「local/test-branch-A」にマージ。そしてまたAさんのブランチである「local/test-branch-A」で作業を続けることができる。  
![local-remote3](image/part5/local-remote3.bmp)  
また、Bさんも開発に参加しているときは、Aさんのブランチ「remote/test-branch-A」と同様に「Bさんのブランチ」となる「remote/branch-B」を作成して、そちらで作業を進めていけば良い。「master」にマージしてほしいときは管理者に報告する。  
![local-remote4](image/part5/local-remote4.bmp)  
こうすることで他人のブランチ、作業にふれることなく別々の人物で並行して作業ができる。本流（ここでは「master」）に合流させるときだけ管理者がチェックするようにすればよい。

# リモートリポジトリを作成する（GitHub）
リモートリポジトリがなんなのかわかったところで実際にリモートリポジトリを作成しよう。オンライン上においておくものなのでサーバーが必要だ。今回は無料で利用することのできるリモートリポジトリサービス、GitHubを使ってみよう。英語になってしまうがなんとか耐えて。  
## アカウントを作成する
まずは[GitHub(github.com)](https://github.com)にアクセスする。  
次に右上のSign Upをクリックする。  
![gihub0](image/part5/github0.png)  
次に、ユーザー名、Eメールアドレス、パスワードを入力して「Create an account」をクリック。  
![gihub1](image/part5/github1.png)  
プランの選択だが今回はフリープランを使用するので上をチェック。下の２つのチェックボックスは任意。メール欲しい人は一番下にチェックを入れよう。「Continue」をクリック。  
![gihub2](image/part5/github2.png)  
最後にアンケートのようなものがある。面倒な人は下の方の「skip this step」。アンケートに答えてあげる優しい人はぜひ。上からプログラミング経験、GitHubの使用目的、あなたの立場、あなたの興味（自由記述）である。答え追えたら「Submit」。  
![gihub3](image/part5/github3.png)  
あとは記入したメールアドレスに登録確認メールがきてるはずなので、「Verify email address」をクリック。  
これでアカウント作成は完了だ。
## 公開鍵の設定
さて、アカウントが作成できたところでいくつか設定をしよう。  
![gihub4](image/part5/github4.png)  
画像とかBio、URLなどはお好みで。今回設定したいのはココ。  
![gihub5](image/part5/github5.png)  
ここで一旦コマンドに戻ろう。  
次のように入力してみる。  
```sh
$ ssh-keygen -t rsa
```
すると次のようになるだろう。  
```sh
$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/(username)/.ssh/id_rsa):
```
ここは何も記入しなくてOKなのでEnter（むしろ入力すると少し面倒な設定が増える）。すると、
```sh
$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/(username)/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
```
と出るはずなので好きなパスワードを入れる。パスワードを指定しない場合は何もしないでEnter。初回はなしで大丈夫だろう。すると  
```sh
$ ssh-keygen -t rsa
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/(username)/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase agein:
```
と言われるのでもう一度同じパスワードを入力（あるいは未入力）してEnter。
すると、上の「Enter file in which to save the key」の後ろに表示されたところにRSA暗号鍵が作成される。よくわからない人はGitHubと通信するのに必要なものだと思おう。「cd」で表示されたところに移動してディレクトリの中身を表示してみると、

- Windowsの場合

```dosbatch
> cd <表示された場所>
> dir
```

- Mac、Linuxの場合
```sh
$ cd <表示された場所>
$ ls
```
で「id_rsa」と「id_rsa.pub」の２つの鍵が生成できていることがわかる（デフォ設定ならユーザーのとこの「.ssh/」のはず？）。  
このうちGitHubには公開鍵のほうを登録したいので  

- Windowsの場合
```dosbatch
> clip < id_rsa.pub
```

- Mac、Lunuxの場合
```sh
$ pbcopy < ./id_rsa.pub
```
として鍵の内容をクリップボードにコピーしておき、GitHubの「New SSH key」のところから  
![gihub6](image/part5/github6.png)  
鍵の名前と内容を貼り付けして「Add SSH key」をクリックする。  
![gihub7](image/part5/github7.png)  
これで通信の準備は完了。`ssh -T git@github.com`と入力して
```sh
$ ssh -T git@github.com
Hi <username>! You've successfully authenticated, but GitHub does not provide shell access.
```
と表示されていればOK。  
## リモートリポジトリを作成する
ここまでできたら、右上の「＋」をクリックしてメニューを開く。そして「New repository」を選択。  
![gihub8](image/part5/github8.png)  
リポジトリーの名前を入力する。今回は「git-test」。下のチェックボックスは「Public」。フリープランではこれしか選択できない。最後に「Create repository」を選択すれば終わりだ。READMEはチェックしなくてOK。  
![gihub9](image/part5/github9.png)  
これでリモートリポジトリの作成は完了した。

# まとめ
今回はリモートリポジトリはなにかということと、GitHubでの作り方を説明した。  
リモートリポジトリはチームでプロジェクトを共有するためのリポジトリであり、ローカルリポジトリと適宜同期を取りながら作業を進める。  
GitHubではアカウントを取得後、鍵を指定、リポジトリを作成した。鍵は一度作っておけばそのアカウントのリポジトリはどれでも使えるので以後新しくリポジトリを作成するときは不要である（パスワードをつけるなど変更を加えたい場合は再度設定する必要がある）。  

次回は作ったリモートリポジトリにデータをアップロードしたりと操作を加えていくことになる。    