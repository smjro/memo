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
