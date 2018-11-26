# Gitコマンドチートシート

ググると大量に出てくるチートシートだけどいまいちしっくり来る分類のものがなかったので自作。
詳しい説明とかはブログに掲載した。 ->[【Git入門】Gitを使い始めたい人へ「Gitの導入」　～A Beginner to Beginners～ 【その１】](http://mattyan1053.hatenablog.com/entry/2018/07/20/015000)  

目次  

<!-- TOC -->

- [Gitコマンドチートシート](#gitコマンドチートシート)
    - [リポジトリの作成](#リポジトリの作成)
    - [Git設定](#git設定)
    - [作業ファイル関連](#作業ファイル関連)
    - [インデックスの操作](#インデックスの操作)
    - [コミット関連](#コミット関連)
    - [ブランチ関連](#ブランチ関連)
    - [マージ関連](#マージ関連)
    - [リベース関連](#リベース関連)
    - [タグ関連](#タグ関連)
    - [リモートリポジトリとの同期](#リモートリポジトリとの同期)
    - [状態確認](#状態確認)

<!-- /TOC -->

## リポジトリの作成
ローカルリポジトリの作成

```console
$ git init
```

リモートリポジトリのクローン

```console
$ git clone <repository_url> <directry>
```

## Git設定
ユーザ名、Eメール設定

```console
$ git config [--global] user.name "username"
$ git config [--global] user.email "email adress"
```

エディタ設定

```console
$ git config [--global] core.editor <adress of editor>
```

バージョン確認

```console
$ git --version
```

## 作業ファイル関連
ファイル削除＋削除をステージ

```console
$ git rm <filename>
```

ファイル名を変更してコミット

```console
$ git mv <file-original> <file-renamed>
```

## インデックスの操作
ステージする

```console
$ git add <filename>
$ git add . #全ファイルadd
```

ステージしたくないファイルは「.gitignore」ってファイルを作って中にステージしたくないファイル名を書き込めばいい。

```:.gitignore
*~
```
とすれば「~」で終わるファイル全部スルーできる。

一度addした新規ファイルをインデックスから除外

```console
$ git rm --cached <filename>
```

一度addした変更ファイルをインデックスから除外

```console
$ git reset HEAD <filename>
```

インデックスをすべて空にする

```console
$ git reset HEAD
```

インデックスにないファイルの差分を表示

```console
$ git diff
```

## コミット関連
インデックスの内容をコミットする

```console
$ git commit
```

コミットメッセージを簡易的に指定してコミットする

```console
$ git commit -m "commit message"
```

前のコミットを取り消したコミットを作成する

```console
$ git revert HEAD
```

ワークツリーとインデックスを維持して直前のコミットを取り消す

```console
$ git reset --soft HEAD~
```

ワークツリーを維持して直前のコミットを取り消す

```console
$ git reset HEAD~
```

直前のコミットの状態に完全に戻す

```console
$ git reset --hard HEAD~
```

resetでのHEADの移動を取り消す

```console
$ git reset --hard ORIG_HEAD
```

直前のコミットに上書きして修正する

```console
$ git commit --amend
```

## ブランチ関連
ローカルブランチを一覧表示

```console
$ git branch
```

リモートブランチも含めて一覧表示

```console
$ git branch -a
```

新規ブランチを作成

```console
$ git branch <new branchname>
```

新規ブランチを作成してそのブランチに切り替え

```console
$ git checkout -b <new branchname>
```

既存のブランチに切り替え

```console
$ git checkout <branchname>
```

ブランチを削除

```console
$ git branch -d <branchname>
```

## マージ関連
現在branchAにいて、branchAにbranchBをマージ。fast-forwardできるときはして、できないときは新規コミットをつくる

```console
$ git merge branchB
```

branchBをマージするがfast-forwardできてもしないで新規コミットをつくる

```console
$ git merge --no-ff
```

branchBを一つにまとめてbranchAにマージ

```console
$ git merge --squash branchB
```

## リベース関連
branchBの内容をbranchAにリベース

```console
$ git checkout branchB
$ git rebase branchA
```

HEADの前に連続するn個のコミットをまとめる

```console
$ git rebase -i HEAD~n
```
としたあと「pick」を「squash」に書き換え


HEADの前n個のコミット内容を修正する

```console
$ git rebase -i HEAD~n
```

としたあと「pick」を「edit」に書き換え  
その後書き換えたら

```console
$ git add <filename>
$ git commit --amend
$ git --continue
```

rebaseを中断する

```console
$ git rebase --abort
```

一つ前のrebaseを取り消す

```console
$ git reset--hard ORIG_HEAD
```

## タグ関連
タグ一覧を表示する

```console
$ git tag
```

タグを検索する(\*が使用可能 [ex: v1.1.\*]）

```console
$ git tag -l <word>
```

タグを現在の最新のコミットにつける

```console
$ git tag <tagname>
```

注釈付きタグを最新のコミットにつける（`-m`をつけなければエディタが起動）

```console
$ git tag -a <tagname> -m "tag message"
```

過去のコミットにタグをつける

```console
$ git tag <tagname> <commit>
```

## リモートリポジトリとの同期
ローカルリポジトリと同期するリモートリポジトリを登録

```console
$ git remote add <repository_name> <repository_url>
```

repositoryのbranchにプッシュする

```console
$ git push <repository_name> <branchname>
```

リモートリポジトリから履歴をダウンロード

```console
$ git fetch <repository_name> <branchname>
```

リモートリポジトリから履歴をダウンロードし統合

```console
$ git pull <repository_name> <branchname>
```

タグをリモートリポジトリでも共有する

```console
$ git push <repository> <tagname>
```

## 状態確認
現在の状態を確認

```console
$ git status
```

履歴を表示

```console
$ git log
```

logのオプション
[--follow] <filename> : 指定ファイルの履歴を表示
[--oneline] : 一行でコミット情報を表示
[--graph] : ツリー形式で表示

コミットの詳細を表示

```console
$ git show <commit>
```

こんなもんかな？随時他にも使いそうなのあったら追加していく。
