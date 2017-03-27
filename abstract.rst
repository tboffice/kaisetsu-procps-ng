.. raw:: latex

   \hbox{}\newpage
   \hbox{}\newpage

はじめに
========

まえがき
-------------

拙書は、解説CoreUtilsシリーズの第2弾として書いた本です。そろそろ次の本を出したくて、GNUのutils系を漁っていて見つけたのが今回の「procps-ng」でした。

procps-ngとは
---------------

/proc を見やすくするツールです。本当にそれだけです。

/proc とは
---------------

/proc は、カーネルが提供しているシステムの情報を読み書きできる疑似 [#pseudo]_ ファイルシステムです。ではちょっとだけ覗いてみましょう。

.. [#pseudo] 原文だと pseudo と書かれています。スード、と読みます。読めねーけどそう発音します

::

   [root@procps-ng-build ~]# ll -d /proc
   dr-xr-xr-x 79 root root 0 Sep 27 17:08 /proc
   [root@procps-ng-build ~]# ll /proc/version
   -r--r--r-- 1 root root 0 Sep 27 17:12 /proc/version
   [root@procps-ng-build ~]# cat /proc/version
   Linux version 3.10.0-514.21.1.el7.x86_64
   (builder@kbuilder.dev.centos.org) (gcc version 4.8.5 20150623
   (Red Hat 4.8.5-11) (GCC) ) #1 SMP Thu May 25 17:04:51 UTC 2017

おや、ディレクトリなのに0バイトですね。ファイルも0バイトなのに中身がありますね。 `/proc` 直下には、 cpuinfo や uptime といったファイル(のように見えるファイル)があります。
マシンの状態や、プロセスの状態がファイルとして読み出せるようになっています。これらはカーネルが提供しています。
読み出すことができるということは書き込めることもできると気づいた方は勘がいいですね。まーそんな人は /proc のことを知っているという読者だと思います。よくある例：

::

   [root@procps-ng-build ~]# cat /proc/sys/net/ipv4/ip_forward
   0
   [root@procps-ng-build ~]# echo 1 > /proc/sys/net/ipv4/ip_forward
   [root@procps-ng-build ~]# cat /proc/sys/net/ipv4/ip_forward
   1

カーネルに値を書き込むことができました。Linuxのマシンをルータとして使うときには必須の設定ですね。このへんまでは分かりやすいファイルを示したんですが例えば `systemd` のとあるファイルを見てみるとこうなるわけです。

::

   [root@procps-ng-build ~]# cat /proc/1/stat
   1 (systemd) S 0 1 1 0 -1 4202752 6572 603101 53 265 10 74 806 302 20 0 1 0

ここで「1」というのがプロセスID(pid)です。この場合はsystemdです。こうして、容易に人間が読めないファイルが出てきます。こんなこともあろうかと、これらのファイルを人間にわかりやすく表示してくれるツールを提供しているのが procps-ng です。含まれているコマンドは `free, kill, pgrep, pkill, pmap, ps, pwdx, skill, slabtop, snice, sysctl, tload, top, uptime, vmstat, w, watch` です。本書では全てのコマンドを解説します。

ngって何？
-----------

New Generation の略です。2010年に親玉である procps から fork(分岐) しました。当時のprocpsはディストリビューションごとにパッチが出ていて、各ディストリビューションで違うものになっていました。Debian,Fedora,openFUSEと独立した開発者によってforkが開始されました。procpsはバージョン3.2.9で保留になり、procps-ng-3.3.0と名前を変更し、現在まで続いています。本書執筆時点では、3.3.12(2016-07-19)が最新です。1年に数回、マイナーバージョンアップがあるという頻度です。最近は滞り気味ですがコミットは続けられています。

自分のディストリビューションではどうなってる？
-------------------------------------------

Digital Oceanが提供しているイメージのOSで調べたところ下記のようになりました。一昔まえのOSだと、procpsが入っています。
コマンドの結果などがprocps-ngと違うので注意しましょう。

.. table:: procps/-ngとディストリビューション
   :widths: auto

   ====================  ==================
   ディストリビューション  パッケージ名
   ====================  ==================
   CentOS  7.3.1611      procps-ng-3.3.10
   CentOS  6.9           procps-3.2.8
   Fedora 25             procps-ng-3.3.10
   Ubuntu 16.04.2        libprocps4 3.3.10
   Debian 8.7 jessie     libprocps3 3.3.9
   ====================  ==================

:CentOS  7.3.1611: procps-ng-3.3.10
:CentOS  6.9: procps-3.2.8
:Fedora 25: procps-ng-3.3.10
:Ubuntu 16.04.2: libprocps4 3.3.10
:Debian 8.7 jessie: libprocps3 3.3.9

対象読者
--------

Linuxのコンソールでお仕事をしている方に効きます。システムの状態を見るコマンドはいくつかあるので、パッケージが提供しているものを知りたいというときにぴったりです。

本書の構成
-----------

Procps-ng にあるコマンドを一つずつ解説していきます。解説とオプション、実行例を示します。
解説は、どんなコマンドなのか解説します。オプションは、コマンドのオプションを解説します。すべて解説すると重複が発生するため、よく使うオプションをメインで取り上げます。
実行例は、実行した例を示します。簡単な結果であれば省略することがあります。是非、お手元にLinuxのマシンを用意してコマンドを実行してみましょう。

凡例
-----

本文を読むためのおやくそくです。例を示します。

.. code-block:: sh

   $ man free

これは、一般ユーザのターミナルで `man free` というコマンドを打つことを表します。「$」でなく「#」であった場合は、rootユーザでコマンドを実行していることを表します。
`man free` は、freeコマンドのマニュアルを開くコマンドです。実際に実行すると `less` コマンドと同様のキーバインドになっています。
スペースを押すと１ページ下に移動、qで閉じます。hでキーバインドのマニュアルが出てきます。このマニュアルを閉じるときもqを押せば閉じます。
さて、 `man free` というコマンドを打つと、冒頭はこのようになっているはずです。

.. code-block:: sh

   NAME
   free - Display amount of free and used memory in the system

   SYNOPSIS
   free [options]

NAME はコマンドについての簡単な説明です。辞書によるとSYNOPSISは「〔論文や本などの〕梗概、大要」 [#SYNOPSIS]_ とあります。こちらはコマンドの書式を表します。本書での概要の部分になります。
大カッコ [] の中身は、書いても書かなくてもよいという意味です。 `free` コマンド単体で実行することができます。

.. [#SYNOPSIS] http://eow.alc.co.jp/search?q=SYNOPSIS&ref=sa

本書で登場する `pmap` の場合、

.. code-block:: sh

   SYNOPSIS
   pmap [options] pid [...]

となっています。pidが大カッコに囲まれていないので必ずpidを書いてね、という意味です。 [...] については何か文字が入りますが、あっても無くてもよいです。

本文中の「原文」とは procps-ng のマニュアルのことです。Procps-ngのマニュアルは、Coreutilsのようにwebサイトにまとまっていないので、今回は man コマンドを見ながら書きました。

以上の知識を頭に入れた上で、本書をお読み下さい。

ご注意
------
ソースを読んで実装部分などの話はありません。コマンド・オプションの使い方を説明しています。動作検証は CentOSで行っています。Mac・Debian・FreeBSDなどでは若干違うところがあるかもしれません。


ドキュメントはどこ？
--------------------

ソースどこだよ：
  gitlabにあります　https://gitlab.com/procps-ng/procps/

zipでくれ：
  https://gitlab.com/procps-ng/procps/tags

頻繁に寄せられる質問は：
  https://gitlab.com/procps-ng/procps/blob/master/Documentation/FAQ

マニュアルは：
  まとまったものはないので、manページを参照して下さい

バグレポートは：
  https://gitlab.com/procps-ng/procps/blob/master/Documentation/bugs.md

メーリングリストは：
  http://www.freelists.org/archive/procps/
