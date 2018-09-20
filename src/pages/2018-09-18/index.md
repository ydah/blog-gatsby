---
title: 【Linux】デバッグに有用なシェルのオプション
date: "2018-09-18"
---

![SnowLeopard](./snow-leopard.jpg)  

---

## デバッグに有用なオプション

### set -C

 ```grep foo bar.log > filtered.log``` のようにリダイレクトを使うと、デフォルトでは出力先のファイルは問答無用で上書きされます。  
リダイレクト先のファイル名を打ち間違えるなどして意図せずファイルを上書きしていてもすぐには気付けません。  
-C オプションを有効にしておけば、リダイレクト先のファイルが既に存在している場合はエラーになるので、不用意な上書きを防げます。  

なお、ファイルを本当に上書きしたいときは ```>``` の代わりに ```>|``` と書くと上書きする事が出来ます。


### set -e

シェルスクリプトの途中でエラーが発生したときはそのシェルスクリプトの実行を中断すべき場合が多い。

例えば、プログラムをビルドしてそれを特定のディレクトリにインストールするというシェルスクリプトがあったとしたら、  
ビルドが失敗した場合にインストールを試みるのは無駄です。

大概のコマンドは、失敗したときに終了ステータス0(正常終了) 以外の終了ステータスを返すようになっているので、  
それを自動的に検知してシェルスクリプト自体も終了させる -e オプションを有効にしましょう。

なお、コマンドが失敗してもシェルスクリプトを終了させたくないときは、  
コマンドに ```&& true``` を付けるとシェルスクリプトは終了しません。

 *※ set -e は理解した上で入れてほしいというお話。*

これがあると、シェルが実行するコマンドで終了コード0以外で終了すると、シェルスクリプト自身が終了し、  
以降のシェルスクリプトが実行されない。

 ```set -e``` は入れずに、実行時の戻り値($?変数、IF 等)でチェックする方が確実ではある。  
おまじないの様にコピー&ペーストするのではなく、理解して使う必要がある。


### set -u

デフォルトでは、存在しない変数は空文字列に展開されます。  
これにより、しばしば悲惨な結果を招きます。  
例えば下のスクリプトでは変数の名前を打ち間違えているので ```~/foo.tmp``` ではなく ```~``` が丸ごと削除されてしまいます。

```sh
tmpdirname=foo.tmp
rm -fr~/"$tempdirname"
```

```-u``` オプションを有効にしておけば、存在しない変数はエラーになってスクリプトが終了するので安心です。