備忘録
=============
ubuntuの設定を初め，よく使うソフトの使用方法等をまとめています。
### scilab
***
scilabのディレクトリまで移動するのが面倒なので端末から起動できるように
設定する．

homeにある**.bashrc**にscilabまでのパスを次のように設定することで，端
末から起動可能となる．

```
export PATH=$PATH:/home/shimada/scilab-5.5.0/bin
```

scilabのサンプルコード
>-->t=linspace(0,8,100);    //←時間変数 t へ0～8秒を100分割した値の配列を設定  
-->s=poly(0,'s');          //←多項式の変数 s を定義  
-->G=1/(s^2+3*s+2);        //←伝達関数 G を定義  
-->sys=syslin('c',G);      //←連続時間線形システムへ伝達関数 G を登録(離散：d)  
-->y=csim('step',t,sys);   //←伝達関数 G へステップ入力を与えた場合の出力変化を y に得る  
-->plot(t,y)               //←グラフ表示  
-->xgrid()                 //←グリッドの表示  
-->xtitle('Step Respons','Time(sec)','Amplitude')  //←タイトル関連の表示



### gsl
***
gslのコンパイルの通し方

```
gcc -Wall -I/usr/lacal/include -c pro6_3.c
gcc -L/usr/local.lib pro6_3.o -lgsl -lgslcblas -lm
```

-lの直後はライブラリを示している．ただしlibgslなどの頭についているlib
 は無視して書くこと．
 

### CapsLockとCtrl
***
CapsLockキーとCtrlキーを入れ替える場合

```
$ dconf write /org/gnome/desktop/input-sources/xkb-options"['ctrl:swapcaps']"
```

CapsLockキーを追加のCtrlキーとして使う場合

```
$ dconf write /org/gnome/desktop/input-sources/xkb-options"['ctrl:nocaps']"
```

CapsLockキーの動作をデフォルトに戻す場合

```
$ dconf reset /org/gnome/desktop/input-sources/xkb-options
```
***
ubuntuのアップデートで上記の方法が使えなくなった模様なので
xkbでキーバインドを変更する方法を示す．

まず，homeに以下のフォルダを作成する
```
$ mkdir -p ~/.xkb/keymap ~/.xkb/symbols
```
symbolsのディレクトリが作成されていない場合は，改めてmkdirで作成してください．
次に，ベースとなる設定ファイル（mykbdファイル）を作成する．
```
$ setxkbmap -print > ~/.xkb/keymap/mykbd
```
次に，symbolsにmyswapファイルを作成し，以下の内容を記す．
```
partial modifier_keys
xkb_symbols "swapkeys" {
  replace key <ESC> {[ quoteleft, asciitilde ] };
  replace key <TLDE> {[ Escape ] };
  replace key <LCTL>  { [ Caps_Lock ] };
  replace key <CAPS> { [ Control_L ] };
};
```
myswapファイルの内容を，mykdbファイルに反映するように指定する．
xkb_symbolsの項目の最後に"+myswap(swapkeys)"を追加する．
以下に，一例を示す．
```
xkb_keymap {
    xkb_keycodes  { include "evdev+aliases(qwerty)" };
    xkb_types     { include "complete"  };
    xkb_compat    { include "complete"  };
    xkb_symbols   { include "pc+us+inet(evdev)+myswap(swapkeys)"    };
    xkb_geometry  { include "pc(pc105)" };
};
```
最後に，設定を起動時に反映させるように，.bashrcに以下を追加する．
```
xkbcomp -I$HOME/.xkb ~/.xkb/keymap/mykbd $DISPLAY 2> /dev/null
```

### コマンド集
***
- C-u M-x shellで複数のshellを同時に起動できる
- platex hoge.tex  （yatex環境下ではC-c C-t jで代用可）
- dvipdfmx hyu	（pdfの作成）
- gnuplot plot.gp	（グラフの出力）
- tar -cvzf last.tar.gz last  （ファイルの圧縮）
- make ○○  （.cなどは必要ない）
- ./ファイル名  （.cなどは必要ない）
- emacs ファイル名&  （emacsを使用しながらターミナルをいじることが可能）


### OpentCVのコンパイル
***

```
g++ -o a.out ファイル名.c `pkg-config --cflags opencv` `pkg-config --libs opencv`
g++ -o ファイル ファイル.c `pkg-config --cflags opencv` `pkg-config --libs opencv`
```

### pstoedit
***
epsをobjに変換

```
pstoedit -f tgif ___.eps ___.obj
```

### terminal
***
- ls : カレントディレクトリ下のファイルとディレクトリを表示
- ls -a : .で始まる隠しファイルも表示
- ls -al : 詳細を表示
- cd :  ホームディレクトリへ戻る
- cd.. : 一つ前のディレクトリに戻る
- pwd : ディレクトリ名 : 作業ディレクトリを表示
- mkdir : ディレクトリ名 : ディレクトリを作成
- control+shift+t : 
- control+ALT+t : 
- tabキーで補間
- Control+C : 走っているコマンドを終わらせる


### tgif
***
- cntl+s : 保存
- cntl+p : ファイルの排出
- tgif ファイル名  
ファイル名のあとに&をつけることで続けてコンソールで作業が可能
- tgif -print -color -eps ___.obj  
epsをカラーで出力


### gnuplot 
***
.gpファイルの書き方を示す．
まず，次のように書く

```
set terminal x11
```

次に，描画するグラフの軸の設定を行う．

```
set xrange [-10:10]
set yrange [-10:10]
set zrange [-10:10]
set xlabel "sampling time [sec]"
set ylabel "position [m]"
set zlabel "position [m]"
```

グラフを保存する際のカラーかモノクロの設定を行う．範囲やラベルを設定することができる．
```
カラー　 : set terminal postscript enhanced color
モノクロ : set terminal postscript eps
```

tgifで編集可能な.objファイルを生成するには

```
set terminal tgif
```

と記述し

```
set output '___.obj'
```

で出力するファイル名を指定する．グラフを描画するには

```
plot "./___.dat" u 1:2 w l, "" u 3:4 w l
```
のように記述すると良い．なお，""で囲まれている箇所にデータファイル名を入れる．

描画を点や線に変更したいときは以下のようにすると良い．  
>線で表示する場合 with lines (w l)  
>点(様々な形をした点から選べる)で表示する場合 with points (w p)  
>線と点で表示する場合 with linespoints (w lp)  
>点で表示する場合 with dots (w d)

コマンドeveryの使い方
>every 行刻み:ブロック刻み:初期行:初期ブロック:終了行:終了ブロック

- 行刻み  
データ行を何行ごとに読み出すか指定, 何も指定しなければ1になる (すべてのデータ行を読む)
- ブロック刻み  
データのブロックを何ブロックごとに読み出すか指定, 何も指定しなければ1になる (すべてのブロックを読む)
- 初期行  
データ行をどの行から読み始めるかの指定, 何も指定しなければ0になる(最初の行から読む)
- 初期ブロック  
データブロックをどのブロックから読み始めるかの指定, 何も指定しなければ0になる(最初のブロックから読む)
- 終了行  
データ行をどの行で読み終えるかの指定, 何も指定しなければデータファイルの最後の行まで読む
- 終了ブロック  
データブロックをどのブロックで読み終えるかの指定, 何も指定しなければデータファイルの最後の行まで読む

### pylearn2
***
pylearn2の使い方  
1. YAMLを作成する  
2. train.pyを実行する  
3. 実行結果を表示する

```
show_weights.py --out=weights.png xxx.pkl	//画像を作成 
print_monitor.py xxx.pkl | grep test_y_misclass	//イマイチわからん 
print_monitor.py xxx.pkl		//こっちのが表示されるかも? 
plot_monitor.py xxx.pkl			//結果のグラフ表示
```

train.pyとはYAMLファイルに記述されている内容にしたがって学習するスクリプト．
学習結果をpklファイルとして出力する．

### ubuntuにnvidiaのドライバをインストールする方法
***
何度も苦労したので備忘録として残しときます．

１．まずは，NVIDIAからドライバをダウンロードしてきてください．そして，
   ダウンロードしてきたファイルを分かりやすいディレクトリに移してお
   く．~/Downloadに置きっぱでも問題ないと思いますがディレクトリ名を日
   本語から英語にしておかないと色々と面倒になると思うので変えといてく
   ださい．
      
２．以下コマンドにて最新の状態にしておく．

```
$ sudo apt-get update
$ sudo apt-get upgrade
```

３． ubuntuのXサーバ関係の開発パッケージをインストールする．

```
$ sudo apt-get install xserver-xorg-dev
```

４．nouveauを使わないように/etc/default/grubを編集する．私は
   /etc/default/まで行きemacsでgrubを編集しました．
   GRUB_CMDLINE_LINUX_DEFAULT="%%%%"の%%%%のところをsplash
   nouveau.modeset=0とする．
   
５．/etc/default/grubの変更を適用させる．

```
$ sudo grub-mkconfig -o /boot/grub/grub.cfg
```

６．Ctl + Alt + F1でCUI（コンソールみたいなもの）をひらく．

７．ユーザー名とパスワードを入力した後，Xサーバを停止する．

```
$ sudo /etc/init.d/lightdm stop
```

８．NVIDIA関連のパッケージをアンインストールする．

```
$ dpkg -l | grep nvidia
```

　　色々と出てくると思うので

```
$ sudo apt-get --purge remove 検索にヒットしたパッケージ名
```

　　でアンインストールする．私の場合は

```
$ apt-get --purge remove xserver-xorg-video-nouveau
```

　　だけをアンインストールしました．アンインストールをした場合は再起動
をする．コマンドは，以下を実行すると再起動します．

```
$ shutdown -r now
```

９．再度Ctl + Alt + F1でCUI（コンソールみたいなもの）をひらき
　　Xサーバを停止する．

```
$ sudo /etc/init.d/lightdm stop
```

１０．ダウンロードしたドライバがあるディレクトリに移動し
　　以下コマンドにてインストールを開始する．

```
$ sudo sh NVIDIA-Linux-x86-340.46.run
```

　　※ダウンロードしたファイル名を打ち込んでください．「x86-340.46」
　　の部分が人によって変わってきます．
１１．あとは手順に従ってインストールしていく．

追記：アップデートを繰り返すと画面の解像度がおかしくなることがあります．
その場合は９．からやり直して再度ドライバをインストールすれば改善します．

### ubuntuのホームの日本語ディレクトリを英語表記に直す方法
***

１．端末を開く．  
２．次のコマンドを実行する．

```
$ LANG=C xdg-user-dirs-gtk-update
```

３．すると，警告のような画面が出ると思うので「Don't ask me this again」
にチェックを入れて「Update Names」をクリックする．  
４．再起動すれば変わっていると思います．

### TEDの文字化けの対処法
バージョン:ubuntu 14.10
***
以下の操作を行いchromeを起動し直すと
文字化けは解消された

```
sudo apt-get install fonts-arphic-uming  
fc-cache -fv  
sudo cp /usr/share/fonts/truetype/takao-gothic/TakaoPGothic.ttf /usr/share/fonts/truetype/arphic/uming.ttc
```

## emacsでスペルチェックを行う方法
Emacsでスペルチェックを行うには`aspell`を使うと良い．
aspsllは`ispell`の後継ソフトで，デフォルトで用意されている．
ここでは，`ispell`を`aspell`に置き換える方法と
文字を入力した瞬間にスペルチェックをしてくれるモードを
emacs起動時に有効にする方法を紹介する．

### aspellの設定
日本語と英語が混在する場合は，常に英語の辞書を使うように
`~/.aspell.conf`を作成し
```
lang en_US
```
と書き込んでおく．

### 使い方
バッファ全体をスペルチェック
```
M-x ispell-buffer
```
選択範囲をスペルチェック
```
M-x ispell-region
```

### flyspell
`flyspell-mode`という文字を入力した瞬間にスペルチェックを
してくれるモードがある．  
一時的にflyspell-modeを有効にするには
```
M-x flyspell-mode
```
とすればよい．

### 自動でflyspell-modeを有効にする
ここでは，yatexモードの時にflyspell-modeが自動起動するようにしている．
`~/.emacs.d/init.el`に以下を書く．
```
(mapc                                   ;; 以下flyspell-modeの設定
 (lambda (hook)
   (add-hook hook 'flyspell-prog-mode))
 '(
   c-mode-common-hook                 ;; ここに書いたモードではコメント領域のところだけ
   emacs-lisp-mode-hook                 ;; flyspell-mode が有効になる
   ))
(mapc
   (lambda (hook)
     (add-hook hook
                      '(lambda () (flyspell-mode 1))))
   '(
     yatex-mode-hook     ;; ここに書いたモードでは
                                    ;; flyspell-mode が有効になる
     ))
```
