# ubuntuの環境構築
***
## CapsLockとCtrlの入れ替え
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

## ubuntuにnvidiaのドライバをインストールする方法
***
何度も苦労したので備忘録として残しときます．

- まずは，NVIDIAからドライバをダウンロードしてきてください．そして，
  ダウンロードしてきたファイルを分かりやすいディレクトリに移してお
  く．~/Downloadに置きっぱでも問題ないと思いますがディレクトリ名を日
  本語から英語にしておかないと色々と面倒になると思うので変えといてく
  ださい．

- 以下コマンドにて最新の状態にしておく．

```
$ sudo apt-get update
$ sudo apt-get upgrade
```

- ubuntuのXサーバ関係の開発パッケージをインストールする．

```
$ sudo apt-get install xserver-xorg-dev
```

- nouveauを使わないように/etc/default/grubを編集する．私は
  /etc/default/まで行きemacsでgrubを編集しました．
  GRUB_CMDLINE_LINUX_DEFAULT="%%%%"の%%%%のところをsplash
  nouveau.modeset=0とする．

- /etc/default/grubの変更を適用させる．

```
$ sudo grub-mkconfig -o /boot/grub/grub.cfg
```

- Ctl + Alt + F1でCUI（コンソールみたいなもの）をひらく．

- ユーザー名とパスワードを入力した後，Xサーバを停止する．

```
$ sudo /etc/init.d/lightdm stop
```

- NVIDIA関連のパッケージをアンインストールする．

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

- 再度Ctl + Alt + F1でCUI（コンソールみたいなもの）をひらき
  Xサーバを停止する．

```
$ sudo /etc/init.d/lightdm stop
```

- ダウンロードしたドライバがあるディレクトリに移動し
  以下コマンドにてインストールを開始する．

```
$ sudo sh NVIDIA-Linux-x86-340.46.run
```

※ダウンロードしたファイル名を打ち込んでください．「x86-340.46」
の部分が人によって変わってきます．
- あとは手順に従ってインストールしていく．

追記：アップデートを繰り返すと画面の解像度がおかしくなることがあります．
その場合は９．からやり直して再度ドライバをインストールすれば改善します．

## ubuntuのホームの日本語ディレクトリを英語表記に直す方法
***

１．端末を開く．  
２．次のコマンドを実行する．

```
$ LANG=C xdg-user-dirs-gtk-update
```

３．すると，警告のような画面が出ると思うので「Don't ask me this again」
にチェックを入れて「Update Names」をクリックする．  
４．再起動すれば変わっていると思います．

## TEDの文字化けの対処法
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

