# debian-live-custom

## ビルド環境

- debian 11 bullseye (amd64)

## ビルド & 導入

- apt するもの

```
live-build
```

- {distribution name}/{architecture} に移動して下記実行

```
# auto ディレクトリの設定と現環境を合わせて再コンフィグする (make oldconfig 相当)
lb config
# ビルド
sudo lb build
```

- USB メモリ等に焼き込み

```
# 焼き込み
sudo dd if=live-image-amd64.hybrid.iso of={書き込み先デバイス} bs=128k
# 必要に応じて残り領域をパーティショニング
sudo gdisk /dev/{書き込み先デバイス}
```

- クリーンアップ

```
sudo lb clean
```

## 使い方

- 起動時

tab キーを押してパラメータに toram を追加するとオンメモリブートする（ブート完了次第 USB メモリ抜去可）

- console で使用

fbterm -> uim-fep と打つと日本語が使えるようになる（IMEオンオフはctrl+space）

- console keymap の変更（デフォルトは jp）

LANG=C dpkg-reconfigure console-data

- X 起動

$ startx で起動

- ユーザ名がいる場合の対応

```
username : user
password : live
```

## カスタマイズのメモ

- 本家マニュアル

https://live-team.pages.debian.net/live-manual/html/live-manual/index.en.html


- 参考ページ

http://d.hatena.ne.jp/MIZUNO/20120721/1342844487

- ビルド中に wget が使用されるため、環境の必要に応じて wgetrc に proxy 設定

- スケルトンをコピーするか、まっさらから始める

```
# スケルトンのコピー
mkdir -p live-custom/auto
cp /usr/share/doc/live-build/examples/auto/* live-custom/auto/*
# 初期コンフィグ生成用コマンド
lb config --mode debian --architecture amd64 --distribution bullseye --archive-areas "main contrib non-free" --mirror-bootstrap "http://ftp.jp.debian.org/debian/" --mirror-chroot "http://ftp.jp.debian.org/debian/" --mirror-binary "http://ftp.jp.debian.org/debian/" --bootappend-live "boot=live components nox11autologin locales=ja_JP.UTF-8 keyboard-model=pc105 keyboard-layouts=jp,us timezone=Asia/Tokyo utc=no"
```
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

- chroot 中 hook の作成
  + config/hooks/live/0099-custom.hook.chroot を作成して編集
  + /usr/share/doc/live-build/examples/hooks/minimal.hook.chroot などを参考に

- binary local hook
  + config/hooks/live/0099-custom.hook.binary を作成して編集
  + install メニューの無効化などを行っている
  + sshd_config の password auth は hook より後で無効化されているが、仕組みが不明

- カスタム配置ファイル
  + .vimrc -> config/includes.chroot/etc/skel/ , config/includes.chroot/root/ 以下に
  + x11vnc-stream -> config/includes.chroot/etc/xinetd.d/ 以下に



