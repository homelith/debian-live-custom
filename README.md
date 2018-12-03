# debian-live-custom

## usage

- apt install

```
live-build
```

- amd64 もしくは x86 ディレクトリ以下で `sudo lb build`

live-image-{amd64/i386}.hybrid.iso が生成される
USB メモリに dd で焼く場合は終わった後に fdisk で残り領域を再利用可能

- 起動時

tab キーを押してパラメータに toram を追加するとオンメモリブートする

- console で使用

fbterm -> uim-fep と打つと日本語が使えるようになる（IMEオンオフはctrl+space）

- console keymap の変更

LANG=C dpkg-reconfigure console-data

- X起動

$ startx で起動

- 備忘

sshd_config の password auth は hook より後で無効化されている..

## customize

- 本家マニュアル

https://debian-live.alioth.debian.org/live-manual/stable/manual/html/live-manual.en.html

- 参考ページ

http://d.hatena.ne.jp/MIZUNO/20120721/1342844487

- 必要に応じて wgetrc に proxy 設定

- スケルトンをコピー
		mkdir -p live-custom/auto
		cp /usr/share/doc/live-build/examples/auto/* live-custom/auto/*

- auto/config 編集
```
	#!/bin/sh
	lb config noauto \
	     --apt-http-proxy http://proxy.hogehoge.com:8080/ \
	     --apt-recommends false \
	     --architectures i386 \
	     --linux-flavours 686-pae \
	     --archive-areas "main contrib non-free" \
	     --bootappend-live "\
	         boot=live components \
	         nox11autologin \
	         locales=ja_JP.UTF-8,en_US.UTF-8 \
	         utc=no \
	         timezone=Asia/Tokyo \
	         keyboard-model=pc105 \
	         keyboard-layouts=jp,us \
	         "\
	     "${@}"
```

- config/package-lists/custom.list.chroot に追加パッケージを記述

- chroot 中 hook

config/hooks/live/0099-custom.hook.chroot を作成して編集
/usr/share/doc/live-build/examples/hooks/minimal.hook.chroot などを参考に

- binary local hook

config/hooks/live/0099-custom.hook.binary を作成して編集
install メニューの無効化などを行っている

- カスタム配置ファイル
  + .vimrc -> config/includes.chroot/etc/skel/ , config/includes.chroot/root/ 以下に
  + x11vnc-stream -> config/includes.chroot/etc/xinetd.d/ 以下に

