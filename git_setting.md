# git
***
githubの使用を前提としています．

## gitのインストールと初期設定
***
まず，以下のコマンドからgitをインストールする．
```
	$ sudo apt-get install git
```
インストールしたgitに対して，自分のユーザ名とメールアドレスを登録します．
これは、リポジトリに対してコミットした人の情報として履歴などに表示されます。
```
	$ git config --global user.name "<ユーザ名>"
	$ git config --global user.email "<メールアドレス>"
```
次のコマンドで、gitの出力をカラーリングすることができます。
```
	$ git config --global color.ui auto
```
gitのコマンドにはエイリアスを設定することが出来ます。  
次のコマンドでは、「checkout」を「co」と省略しても実行できるようになります。
```
	$ git config --global alias.co checkout
```

次に[github](https://github.com/)の初期設定をしていくので，
アカウントを作成してください．作成したら次に，公開鍵を作成します．
デフォルトでは，各ユーザーのSSH鍵はそのユーザーの`~/.ssh`ディレクトリに置かれています．
ない場合は，`ssh-keygen`により作成します．

```
	$ ssh-keygen -t rsa -C "your_email@example.com"
```
メールアドレスはgithubで登録したアドレスにします．実行すると
```
	Enter file in which to save the key (/Users/schacon/.ssh/id_rsa):
	Enter passphrase (empty for no passphrase):
	Enter same passphrase again:
```
と聞かれるので，何も入力せずEnterを三回タンタンターン！してください．これで，鍵を使用
する際にパスフレーズを入力しないでよくなります．また，githubと端末でやりとりする際に，
ユーザ名とパスワードを毎回聞かれるので，煩わしいと思う人は以下で回避できます．
```
	$ ssh-add ~/.ssh/id_rsa
```