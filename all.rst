
解説procps-ng
================

ここからprocps-ngが提供しているコマンドを解説していきます。

free
----------
.. index:: free

- システム上の解放されているメモリと使われているメモリの量を表示します

解説
~~~~~~

コマンドの書き方は下記です

::

   free [options]

`free` コマンドはシステム上のスワップメモリと物理的に使われている/使われていないメモリの量を表示します。カーネルに使われているバッファとキャッシュも表示します。
この情報は `/proc/meminfo` から作られます。表示されるものは

total
   すべてのメモリ量 (`/proc/meminfo` の MemTotal とSwapTotal です)

used
   使われているメモリ量 (total - free - buffers - cacheから計算される)

free
   使われていないメモリ量 (`/proc/meminfo` の MemFree SwapFree です)

shared
   tmpfsによって使われているメモリ量 (カーネル 2.6.32上では　`/proc/meminfo`　の Shmem。0ならば有効になっていない)

buffers
   カーネルバッファによって使われているメモリ量 ( `/proc/meminfo` の Buffers)

cache
   ページキャッシュとslabsによって使われているメモリ量 (`/proc/meminfo` のCached とSlab)

buff/cache
   buffersとcacheの合計

available
   スワップなしに、どれくらいメモリが新しいアプリケーションを開始するために必要かの概算量


オプション
~~~~~~~~~~

まとめて解説します。
バイトで表示したい場合は、 -b または --bytes そのあとは -k,-m,-g,--tera と続きます。

-h, --human
  ひゅーまんりりーだぶるです。Bはbytes KはKibibyte MはMebibyteのように続いていきます

-w, --wide
   ワイドモードです。あんまり効果ない

-c, --count count
   カウントします。デフォルト1秒です。-sで変更可能

-l, --lohi
   low と high なメモリの状況を詳細に示します。totalにHighが表示されてもなーという感じです

--si
   1024じゃなくて1000の累乗で表します

-t, --total
   一番下にTotalを示します

--help
   ヘルプを表示します

-V, --version
   バージョンを示します

参考
~~~~~
参考に、`free` のコマンド結果です。

.. code-block:: sh

   $ free -m
                 total        used        free      shared  buff/cache   avai
   Mem:            488         331           6          28         150
   Swap:             0           0           0

参考に、 `/proc/meminfo` の中身です。

.. code-block:: sh

   $ cat /proc/meminfo
   MemTotal:         500452 kB
   MemFree:            6440 kB
   MemAvailable:     104864 kB
   Buffers:            1656 kB
   Cached:           115852 kB
   SwapCached:            0 kB
   Active:           366456 kB
   Inactive:          61988 kB
   Active(anon):     321484 kB
   Inactive(anon):    18600 kB
   Active(file):      44972 kB
   Inactive(file):    43388 kB
   Unevictable:           0 kB
   Mlocked:               0 kB
   SwapTotal:             0 kB
   SwapFree:              0 kB
   Dirty:                16 kB
   Writeback:             0 kB
   AnonPages:        310980 kB
   Mapped:            17788 kB
   Shmem:             29148 kB
   Slab:              37128 kB
   SReclaimable:      23216 kB
   SUnreclaim:        13912 kB
   KernelStack:        2768 kB
   PageTables:         4884 kB
   NFS_Unstable:          0 kB
   Bounce:                0 kB
   WritebackTmp:          0 kB
   CommitLimit:      250224 kB
   Committed_AS:     813224 kB
   VmallocTotal:   34359738367 kB
   VmallocUsed:       93496 kB
   VmallocChunk:   34359537660 kB
   HardwareCorrupted:     0 kB
   AnonHugePages:         0 kB
   HugePages_Total:       0
   HugePages_Free:        0
   HugePages_Rsvd:        0
   HugePages_Surp:        0
   Hugepagesize:       2048 kB
   DirectMap4k:       63480 kB
   DirectMap2M:      460800 kB
   DirectMap1G:           0 kB


.. raw:: latex

    \clearpage


kill
----------
.. index:: kill

- プロセスのPIDにシグナルを送ります

解説
~~~~~~

コマンドの書き方は下記です

::

   kill [options] <pid> [...]

オプション
~~~~~~~~~~

<pid> [...]
  リストされたすべての<pid>にシグナルを送る

-<signal>, -s <signal>, --signal <signal>
  規定のシグナルが送られる。このシグナルは名前または数字によって定義される。シグナルの挙動はsignal(7)に説明がある。 `man 7 signal` で見れる

-l, --list [signal]
  シグナルの一覧を表示

-L, --table
  シグナルの一覧を良い感じに表で示す。が、実装されてないぽい。おい！！Linux互換性のためにあるんですって奥さん（誰だよ

留意することとして、ビルドインコマンドのkillがあるかもしれない。そのコマンドが必要なときはコンフリクトを解決するために `/bin/kill` と実行する。って書いてあるんですけどなんだかなーという感じです。あれ？そういえばこのコマンド、Coreutilsにもありますね？どういうことでしょうかねえ？

例
~~~~
kill -9 -1
   killできるすべてのプロセスをkillする。うひ～

kill -l 11
   11番のシグナルをシグナル名に変換

kill 123 543 2341 3453
   デフォルトシグナルである SIGTERM を指定されているプロセスIDに送る

.. raw:: latex

    \clearpage

pgrep, pkill
-------------
.. index:: pgrep
.. index:: pkill

- プロセス名や他の属性を基にプロセスを探したりシグナルを送ったりする

マニュアル上は一緒に解説されているのでここでも一緒に解説します。

解説
~~~~~~

コマンドの書き方は下記です。patternは必須です。

::

   pgrep [options] pattern
   pkill [options] pattern

例
~~~

先に例を見ましょう。rootユーザでsshプロセスのpid一覧を表示します

.. code-block:: sh

   pgrep -u root ssh
   707
   1923
   2310

syslogをリロードしてみます

.. code-block:: sh

   pkill -HUP syslogd

netscape プロセスの優先度を変更します

.. code-block:: sh

   renice +4 $(pgrep netscape)

オプション
~~~~~~~~~~

-signal, --signal signal
   定義されたシグナルを名前が一致したプロセスに送る。あるいは番号でもシンボル(SIGTERMとか)も使える。pkillのみのオプション

-c, --count
   通常の出力を抑制。代わりにマッチしたプロセスのカウントを表示。無ければ終了コードに0が返る

-d, --delimiter delimiter
   デリミタを指定。デフォルトは改行。pgrepのみのオプション

-f, --full
   patternはプロセス名にマッチするが、-fにするとフルコマンドでマッチする

-g, --pgroup pgrp,...
   マッチしたpatternのグループIDのリストが表示される。0は実行ユーザのプロセスグループを意味する

-G, --group gid
   マッチした本当のgroup idが表示される。あるいは、数字やシンボル数値も使える

-i, --ignore-case
   大文字小文字区別しない

-l, --list-name
   プロセス名とともに、プロセスidも表示される。pgrepのみのオプション

-a, --list-full
   フルコマンドラインも表示。pgrepのみのオプション

-n, --newest, -o, --oldest
    最も新しい/古いプロセスだけを選択する

-P, --parent ppid,...
   親プロセスのidが表示される

-s, --session sid,...
   プロセスのセッションidが表示される。セッションidが0の場合はpgrepまたはpkill自身のセッションidである

-t, --terminal term,...
   操作しているターミナルのプロセスが表示される。ターミナル名は /dev がない状態であるべきである

-u, --euid euid,...
   effective ユーザidが表示される。なんのことだろう・・・？

-U, --uid uid,...
   リアルユーザidが表示される

-v, --inverse
   マッチしなかったプロセスidを表示。適当なpatternを入れればすべてのpidが・・・？

-w, --lightweight
   pgrepでpidの代わりにスレッドidが表示される。pkillでは無効になる

-x, --exact
   プロセス名が正確にマッチする

-F, --pidfile file
   PIDの書かれたファイルを読む。pgrepよりもpkillを便利に使うためにあるかもしれない

-L, --logpidfile
   上記のpidfileがロックされていないとき失敗する

--ns pid
   同じ名前空間に所属しているプロセスにマッチする。他のユーザからマッチしたプロセスまでroot権限が必要である。マッチするための名前空間の制限については--nslistを見よ

--nslist name,...
   与えられた名前空間にのみマッチする。有効な名前空間は、 ipc, mnt, net, pid, user, utsである

-V, --version
   バージョン情報を表示

-h, --help
   ヘルプを表示

.. raw:: latex

    \clearpage

pmap
----------
.. index:: pmap

- プロセスのメモリマップを表示

解説
~~~~~~

コマンドの書き方は下記です。pidは必須です。

::

   pmap [options] pid [...]

例
~~~

先に例を見ていきましょう。

.. code-block:: sh

   $ pmap 1 | head
   1:   /usr/lib/systemd/systemd --switched-root --system --deserialize 21
   00007ff972b1e000     16K r-x-- libuuid.so.1.3.0
   00007ff972b22000   2044K ----- libuuid.so.1.3.0
   00007ff972d21000      4K r---- libuuid.so.1.3.0
   00007ff972d22000      4K rw--- libuuid.so.1.3.0
   00007ff972d23000    228K r-x-- libblkid.so.1.1.0
   00007ff972d5c000   2048K ----- libblkid.so.1.1.0
   00007ff972f5c000     12K r---- libblkid.so.1.1.0
   00007ff972f5f000      4K rw--- libblkid.so.1.1.0
   00007ff972f60000      4K rw---   [ anon ]

オプション
~~~~~~~~~~

-x, --extended
   拡張したフォーマットを示す

-d, --device
   デバイスフォーマットを示す

-q, --quiet
   ヘッダとフッタを表示しない

-A, --range low,high
   lowとhighのアドレスレンジを指定して制限をかける。数字の間にカンマを間に書くのじゃ

-X
   -xオプションよりもさらに詳細に示す。 注意：フォーマット変更は `/proc/PID/smaps` による

-XX
  　カーネルが提供しているすべてを表示

-p, --show-path
    マッピングコラムにおいてフルパスを表示

-c, --read-rc
    デフォルトの設定を読み込む

-C, --read-rc-from file
    設定ファイル名を指定して読み込み

-n, --create-rc
    新規デフォルト設定を作成

-N, --create-rc-file file
    新規設定ファイルをfileに作る

-h, --help
    ヘルプを表示

-V, --version
    バージョンを表示

戻り値
~~~~~~~

0
   成功

1
   失敗

42
   探したすべてのプロセスが見つからなかった。42の意味はググれば出てくる


.. raw:: latex

    \clearpage

ps
----------
.. index:: ps

- プロセスの情報を表示します


解説
~~~~~~

コマンドの書き方は下記です。

::

   ps [options]


`ps` は現在動作中のプロセスの選択についての情報を表示する。選択や情報を表示を繰り返しアップデートするのが好きなら、top(1)を使ってね。

えーとですね若い人はいいんですけどpsコマンドすごーくいろいろな諸事情によってオプションの指定の仕方に流儀があってそれが統合したもんだから非常に面倒なことになっています。
ダッシュ(-)原理主義とかダッシュ不要主義の人とかいて非常にめんどくさいんだわ。若い人においてはきちんとマニュアルを読んでから使って欲しい。あとは頑張れ！

psはいくつかのオプションの指定の方法があります。

#. UNIX方式。シングルダッシュ(例 ps -f)
#. BSD方式。ダッシュ不要(例 ps aux)
#. GNU long方式。ダッシュが二回(--)

これらを混ぜて使ってはいけません。ps auxをps -auxと書くと、 ps -a -u -x になって意味が違います。どちらかに統一しましょう。 `ps` のマニュアルは量が多いので、コマンドの例を示して終わります。ではどうぞ。

例
~~~~

- 標準のオプションを使ってシステムのすべてのプロセスを見るには

.. code-block:: sh

   ps -e
   ps -ef
   ps -eF
   ps -ely

- BSDのオプションを使ってシステムのすべてのプロセスを見るには

.. code-block:: sh

   ps ax
   ps axu

- プロセスツリーを表示

.. code-block:: sh

   ps -ejH
   ps axjf

- スレッドについての情報は

.. code-block:: sh

   ps -eLf
   ps axms

- 指定した形式でrootで動作しているすべてのプロセスを見るには

.. code-block:: sh

   ps -U root -u root u

- syslogdのプロセスIDだけを表示

.. code-block:: sh

   ps -C syslogd -o pid=

- 42のPIDだけを表示 [#yonjuuni]_

.. code-block:: sh

   ps -q 42 -o comm=

.. [#yonjuuni] このマニュアルというかソフトウエア、42が好きみたいですねえ

.. raw:: latex

    \clearpage

pwdx
----------
.. index:: pwx

- 特定のプロセスが起動しているディレクトリ(current working directory)を表示する

解説
~~~~~~

コマンドの書き方は下記です。PIDが必須です。

::

   pwdx [options] pid [...]


例
~~~~~~~~~~~

なんですかこれは？と言われるので適当に打ってみましょう

.. code-block:: sh

   $ sudo pwdx 1 2 3
   1: /
   2: /
   3: /

あのプロセスってどこのディレクトリで起動しているんだろう？というのが分かります。唯一のオプション `-V` はバージョン情報を表示します。おしまいです。

.. raw:: latex

    \clearpage

skill, snice
--------------
.. index:: skill

- シグナルを送ったり、プロセスの状態を教えてくれます

解説
~~~~~~

コマンドの書き方は下記です。

::

       skill [signal] [options] expression
       snice [new priority] [options] expression

これらのコマンドは古くて移植できない。 `killall` や `pkill` 、 `pgrep` を使ったほうが良い、ってマニュアルにかかれてます。

`skill` はデフォルトで TERM を送ります。 `-l` や `-L` で有効なシグナルの一覧を表示します。 HUPとかINTとかKILLとかSTOPとか0を含みます。
他の方法としては３種類定義されており、 `-9` `-SIGKILL` `-KILL` です。

デフォルトの `snice` の優先度は +4です。優先度の範囲は +20(最も遅い) から -20 (最も早い) です。優先度を上げる(マイナス値を設定する)ときはroot的なユーザ権限が必要です。


解説
~~~~~~

-f, --fast
   ファストモード。未実装

-i, --interactive
   対話的に使う。数字を打ち込んで優先度を変更できる

-l, --list
   すべてのシグナル名を表示する

-L, --table
   すべてのシグナル名を良い感じに表で表示する

-n, --no-action
   なにもしない。シュミレーションをするだけでシステムを変更しない

-v, --verbose
   詳細。説明がなされる（本当にこんな感じで書いてある。なんのこっちゃ

-w, --warnings
   警告が有効にする。未実装

-h, --help
    ヘルプを表示

-V, --version
   バージョンを表示

.. raw:: latex

    \clearpage

slabtop
----------
.. index:: slabtop

- リアルタイムでカーネルのslab cache情報を表示します


解説
~~~~~~

コマンドの書き方は下記です。

::

   slabtop [options]


オプション
~~~~~~~~~~
　
-d, --delay=N
   リフレッシュ間隔を秒で設定する。デフォルトは3秒。qでプログラムを終了

-s, --sort=S
   Sで表示の並べ替えを行う。一文字で指定する。デフォルトは o でオブジェクトの番号でソートする。例えば c でCACHE SIZEでソート、 n は NAME でソート、a はACTIVE でソート。

-o, --once
   一度表示してプログラムを終了する

-V, --version
   バージョンを表示

-h, --help
   ヘルプを表示

::

   # slabtop -o
    Active / Total Objects (% used)    : 127062 / 138452 (91.8%)
    Active / Total Slabs (% used)      : 5830 / 5830 (100.0%)
    Active / Total Caches (% used)     : 83 / 110 (75.5%)
    Active / Total Size (% used)       : 34359.12K / 39188.70K (87.7%)
    Minimum / Average / Maximum Object : 0.01K / 0.28K / 16.19K

     OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME
    21483  17204   0%    0.19K   1023       21      4092K dentry
    13416  13092   0%    0.58K   1032       13      8256K inode_cache
    13356  13356 100%    0.11K    371       36      1484K sysfs_dir_cache
    11904  11507   0%    0.06K    186       64       744K kmalloc-64
     9352   8959   0%    0.57K    669       14      5352K radix_tree_node
     9333   8744   0%    0.08K    183       51       732K selinux_inode_secur
     8385   8366   0%    0.10K    215       39       860K buffer_head

.. raw:: latex

    \clearpage

sysctl
----------
.. index:: sysctl

- カーネルのパラメータを変更する

解説
~~~~~~

コマンドの書き方は下記です。

::

   sysctl [options] [variable[=value]] [...]
   sysctl -p [file or regexp] [...]

Linux触っている人にはお馴染みのコマンドではないでしょうか。え？さわったことない？起動してるLinuxのカーネルのパラメータを変更できるコマンドです。
何が嬉しいかって、port range広げたり、ip_forwardを1にしてルータを作ったりするんですよ。当たり前田のクラッカー。分からないひとはぐぐってね。

このコマンドのパラメータは、 `/proc/sys` のディレクトリ下に対して有効です。依存関係で Procfsが必要です。sysctlのデータを読んだり書き込みしたりできます。


オプション
~~~~~~~~~~~

variable
  キーの名前。例えば kernel.ostype。セパレータである / は . に置換される

variable=value
  キーを設定する。-wパラメータをつけて、ダブルクオートでくくってね！shellがパースするからね！

-n, --values
  キー名を表示しない

-e, --ignore
  知らないキーがあってもエラーを出さない

-N, --names
  名前だけを表示する。シェルでプログラミングするとき便利かもしれない

-q, --quiet
  値を表示しない

-w, --write
  sysctlの設定を変更するときに使うオプション

-p[FILE],--load[=FILE]
  FILEが無いとき、設定されているファイルや `/etc/sysctl.conf` ファイルから設定をロードする。
  ファイル名は標準入力からデータを読み取ることを意味している。FILEは正規表現として与えられることができる

-a, --all
  すべての設定を表示します。キーなんかいちいち覚えていないときに使う

--deprecated
  --allと一緒に使うことによって廃止される予定のあるパラメータも表示

-b, --binary
  改行しないで表示

--system
  下記のすべての設定をロードする

::

    /run/sysctl.d/*.conf
    /etc/sysctl.d/*.conf
    /usr/local/lib/sysctl.d/*.conf
    /usr/lib/sysctl.d/*.conf
    /lib/sysctl.d/*.conf
    /etc/sysctl.conf

-r, --pattern pattern
  パターンを指定して検索。拡張正規表現を使う

エイリアスとして、 -A は -a 、-dは-h、-fは-p 、-Xは-a で、-x , -o はBSDの互換性のために用意。

例
~~~

::

   /sbin/sysctl -a
   /sbin/sysctl -n kernel.hostname
   /sbin/sysctl -w kernel.domainname="example.com"
   /sbin/sysctl -p/etc/sysctl.conf
   /sbin/sysctl -a --pattern 'net.ipv4.conf.(eth|wlan)0.arp'
   /sbin/sysctl --system --pattern '^net.ipv6'

.. raw:: latex

    \clearpage

tload
----------
.. index:: tload

- ロードアベレージをグラフィカルに表示

解説
~~~~~
`tload` は特定の tty の現在のシステムロードアベレージのグラフを表示します。 tty が指定されていない場合は、実行ユーザになります。
コマンドの書き方は下記です。

::

    tload [options] [tty]

オプション
~~~~~~~~~~
-s, --scale number
  画面の縦のスケールを設定する

-d, --delay seconds
  グラフをアップデートする秒数を指定 [#tload]_

.. [#tload] 指定した秒数は alarm(2) のための引数にセットされるため、0を指定したときは SIGALRM が送られないので表示が更新されない

-h, --help, -V, --version
  ここまでしっかり読んできた素晴らしい読者のために敢えて解説しない

関連するファイル
~~~~~~~~~~~~~~~

/proc/loadavg にロードアベレージの情報がある。ただし初見殺し。

.. raw:: latex

    \clearpage
top
----------
.. index:: top

- リアルタイムで動作しているプログラムを表示します [#topmac]_

.. [#topmac] Macの人はどうしたらいいかって？Macのtopコマンドは、劣化したtopだと思っていただければ、遠からず近からず。近からず遠からず。関係ないけどmacのtopのデフォルトの表示がいかついですね（雑な感想

やってまいりました、procps-ngの花形 `top` です。manコマンドを打つと1500行くらいあるんですねー渋いですねー。
ガッツリ解説していきます。manpageを愚直に訳すと冗長なるので、起動時の画面とコマンドラインオプションを軽く説明してから、起動後のキーバインドを説明します。
さて、 `top` を実行するとこんな感じです。

.. figure:: ./top1.eps
   :alt: top実行画面
   :scale: 80%


お馴染みのサマリー・フィールドとコラムのヘッダ・タスクエリアがあります。へー最新のtopってこんなになってるんだ・・・という感想はおいといて、topコマンドを打った時の画面の説明です。ターミナルの大きさに合わせてよろしく表示してくれます。画面の外に隠れているプロセス名なんかは、左右矢印キーを押すと見ることができます。マジで、本当だ、知らなかった。ページャーもちゃんとあって、EndやHomeやPageUp、PageDownキーも押せます。本家procpsは非対応なのでご注意。topのmanpageは大きく分けて、コマンドラインオプション、サマリー、フィールド・コラム表示、インタラクティブコマンド、ウインドウを行ったきり来たり、ファイルの仕様、裏技があります。本書では、 `top` 起動後のキーバインドを中心に解説していきます。

ちなみにtopの終了は c か ctrl-c (kill) です。


コマンドラインオプション
~~~~~~~~~~~~~~~~~~~~~~~

起動時のオプションについて説明します。こうです。「|」は、「または」という意味です。

::

     -hv|-bcEHiOSs1 -d secs -n max -u|U user -p pid -o fld -w [cols]


-h または -v
   helpという名のコマンドラインオプションを表示する。上のオプションが出る。バージョンも表示される

-b
   バッチモード。topの表示を他のプログラムやファイルに送る時に便利。無限に走り続けるので、終了するときは -n オプションで回数を指定するか、killする

-c
   topを打った後cを打った状態で起動する。通常、実行しているプロセスのコマンドラインが表示さるが、この場合はプログラム名のみが表示される

-d 秒数
   更新間隔を指定する。0.1秒単位で指定できる。 -d 12.3 とか。起動後、d や s で更新間隔を再指定できる。0も打ててしまう。ブラクラかよ

-E
   サマリーエリアのメモリの単位を指定する。キビバイトで表示するなら `top -E k` とする。k以外は k,m,g,t,p,eが指定可能。それぞれキビ、メビ、ギビ、テビ、ペビ、エビ(exbi)。top起動後、Eで再指定可能

-H
   個別のスレッドを表示。それぞれのプロセスのスレッドが表示されるようになる。インタラクティブコマンドHで切り替え

-i
   更新間隔までにCPUを使ってないタスクを非表示。つまり活動しているプロセスだけを表示。インタラクティブコマンドiで切り替え

-n 数字
   表示する行数を制限する

-o fieldname
   fieldnameでソート。実行例は `top -o +PID` とか `top -o -PID` とか。プラスマイナスを付けてオーダーを指定。両方つけるとどうなるの？シンタックスエラーになるんじゃないかなぁ？

-O
   fieldnameを表示する。それだけ。上のコマンドの filedname を調べるときに使う

-p
   PIDを指定して表示。たとえばこんな感じ： `top -p1 -p2 -p3` または `top -p1,2,3` 。PIDは、20個まで指定可能。PIDには、0も指定できる。何が起こるかやってみよう。解除するには、Uかuを打ってenterを押す

-s
   設定ファイルを読み込まないセキュアモードで起動 [#securemode]_ [#securemode2]_ 。topに設定ファイルってあるんですねー。あとで解説します

.. [#securemode] 書いてて思ったんですけど、セキュアラモードってかわいくないですか（急に何を言い出すんだこいつ
.. [#securemode2] ガード固そうなんですけどどうですかね（そんなこと言われてもナァ

-S
   累積モードで表示。あとで説明するかも

-u または -U
   ユーザ名を指定すると、そのユーザだけのプロセスを表示する。よく使うかも。uまたはUで切り替え可能。大文字(U)小文字(u)の違いがあるけど省略

-w [number]
   表示する幅を指定。バッチモードで使う

-1
   コアが複数あるメモリでそれぞれの使用率を見ることができる。1で切り替え。コア数が多すぎるとそれだけで画面が埋まる


サマリー表示
~~~~~~~~~~~~~

topコマンドを打った後、上の方はこんな感じになっていると思います。このあたりをサマリーと呼びます。

::

   top - 17:33:28 up  2:28,  1 user,  load average: 0.00, 0.01, 0.05
   Tasks:  66 total,   2 running,  64 sleeping,   0 stopped,   0 zombie
   %Cpu0  :   0.0/11.8   12[||||||||||||                                ]
   GiB Mem : 20.0/1.0      [                                            ]
   GiB Swap:  0.0/0.0      [                                            ]


上から、現在の時間、UPTIME（起動してからの時間、この場合は2時間28分、1人のユーザがログイン、そのあとは現在から1,5,15分のロードアベレージです。
次の行は、タスクの数を表しています。タスクの状態(running, sleeping, stopped, zombie)については各自調べて下さい。ここでは起動(running)しているプロセスが2つある、くらいの認識でよいです。
CPUのところは、最後に画面が更新されてからの間隔に基づくCPUの状態のパーセンテージが表示されます。

::

              a    b     c    d
   %Cpu(s):  75.0/25.0  100[ ...

説明のため、a,b,c,dという記号を付けました。a は us + ni を足したパーセンテージ。bは sy のパーセンテージ。cは合計。dはグラフです。us ってなんだよ、って感じですが、procpsのときはこんな感じで表示されることを思い出しましょう。知らなくても知っていたことにしましょう。

::

   Cpu(s): 0.0%us, 0.0%sy, 0.0%ni, 99.9%id, 0.0%wa, 0.0%hi, 0.0%si,  0.0%st

us
   user : un-nicedなユーザプロセス(un-niced user process)の起動時間

sy
   system : カーネルプロセスの起動時間

ni
   nice : nicedユーザプロセスの起動時間

id
   idle : カーネルアイドルハンドラー(the kernel idle handler)

wa
   IO-wait : I/Oの完了を待っている時間

hi
   ハードウエアの割り込みにかかった時間

si
   ソフトウエアの割り込みにかかった時間

st
   ハイパーバイザーによってこのバーチャルマシーンから奪われた時間


この辺を説明しているとカーネルの話になってくるので、詳しく知りたい方はLinuxカーネルの本やwebをあたって下さい。

次はメモリ使用量です。たとえばこんな表示だったとします。

::

              a    b          c
   GiB Mem : 18.7/15.738   [ ...
   GiB Swap:  0.0/7.999    [ ...

説明のため、a,b,c と記号を振りました。aは使われているパーセンテージです。bはトータルのメモリ量、cはグラフです。気をつけて欲しいのは、 procps の時代は容量で表されていたところがパーセンテージになっています。ご注意。さらに詳細な情報は、mでトグルすることが可能で４種類のモードがあります。実際にmを押してみよう！


フィールド・コラム
~~~~~~~~~~~~~~~~~~~~

フィールドの話です。フィールドとは下記です。

::

      PID USER      PR  NI    VIRT    RES %CPU %MEM     TIME+ S COMMAND


もうお分かりですね。実は、フィールドは実は58個もあります。fキーを押すと、どのフィールドを表示するか決める画面に移ります。全部説明すると大変なことになるので、操作の方法だけ。fキーを押すと「Fields Management for window」と出てくるので解説の通りに操作します。キーバインドをご紹介。

d または <space>
   表示・非表示の選択

s
   ソート順の決定

q または <Esc>
   メニューの終了


インタラクティブコマンド
~~~~~~~~~~~~~~~~~~~~~~~~

インタラクティブコマンドは、topのコマンドを打ってから打つキーです。上でも紹介したじゃないかって？でもfだけ特殊なんで別項としてあります。それ以外のキーを一気に紹介します。キーを打って自分だけの表示を楽しもう（原文にこんなこと書いてないけど一応

記号、数字、アルファベット(AbBbCc...)の順番で解説していきます。アルファベットが抜けているのは、機能への割り当てがないか、fの後に使うキーです。

<Enter> または <space>
   画面をリフレッシュする。新たな気持で

? または h
   ヘルプを表示する。困ったらまずこれ

=
   タスクリミットを解除。詳しくはnを参照のこと

0
   値が0のところを消したり表示したりする

1
   複数コアがあるCPUの場合はそれぞれの使用量を表示する。コア数が多いときはまあそのとき考えよう・・・

2
   1の後に使う。NUMA(Non-Uniform Memory Access)の状態が表示される。詳しくはググって

3
   2の後に使う。NUMAの状態を展開する

A
   プロセスリストのところが4段になって、メモリ使用率順、CPU使用率順、PID順みたいのがそれぞれ表示される。-,+で増やしたり減らしたり。_でタスクエリア消去。Gでウインドウ名を変更。gでウインドウを選択。aとwでウインドウを行ったり来たり。この辺は自分で打ってみて体感したほうが早い

B
   表示上の太字が普通の書体になったりもとに戻ったりする

b
   動きのあったタスクを反転表示（しているように見える）

C
   スクロールの座標を表示。打ってみれば分かる

c
   コマンドラインを表示するか、プログラム名を表示するか切り替える。具体的には `/usr/lib/systemd/systemd --switched-root --system --deserialize 21` が `systemd`になる

d
   画面の更新間隔を更新する時間を指定。デフォルトは3秒。sでも可能

E
   サマリエリアのメモリの容量のスケールを変更する。何度も押すと順次変わっていく。キビバイトからエクシビバイトまで切り替える

e
   タスクエリアのメモリの容量のスケールを変更する。何度も押すと順次変わっていく。キロバイトからペビバイトまで切り替える

f または F
   前項のフィールドコラムをご覧ください。自分でフィールドのカスタマイズができる機能

g
   aキー(複数ウインドウ)のときの４段でてきたそれぞれを選択するモード。1から4の数字を打ち込んでやってください

H
   スレッド表示モードに切り替える。全てのプロセスが見えるようになる

h
   ヘルプ(キーバインド)を表示。ついでに現在のウインドウ名、指定されているモードを表示

I
   Irix/Soraris-mode toggleだそうです。沢山CPUがあるときに分けて表示されるそうです

i
   アイドルモードのタスクを表示するかの切り替え。何も表示されなかったらenterでも連打すればいいんじゃないですかね。どうなるか察しがつきましたか？

J
   文字データの部分を右詰めにする。じゃすてぃふぁーい！のJ

j
   数字データの部分を左詰めにする。じゃすてぃふぁーい!のj

k
   タスクにシグナルを送ることができる。押したら使い方が出る。killのk

L
   タスクエリアから文字列を検索。ハイライトされます。次の文字列をタスクエリアの先頭に持ってくるには & を押す。LocateのL

l
   一番最初の行を表示したり消したりする。topの一番最初の行ってなんでしたっけ？思い出してみよう！

M
   メモリ使用順にソートする

m
   タスクエリアのメモリ・swapのところを表示したり消したりする

n
   タスクの表示行数を制限する。数字を打ち込む。解除は=

N
   PID順にソートする

o または O
   フィルタ(f)中に他のフィルタリングを行う。結構複雑なんでマニュアル見て。ちなみにctrl+oでフィルタしている条件を表示

P
   CPU使用順にソートする

q
   topを終了する

R
   タスクの部分がreverseする

r
   タスクをreniceする。PIDを指定して優先度をセット。-20(優先度高い)から+20(優先度低い)を指定

S
   時間累積モードが有効になる。子プロセスのCPU使用時間も合計して表示される（が、効果がよくわからなかった

s
   画面の更新間隔を更新する時間を指定。デフォルトは3秒。dでも可能。

t
   taskとCPUのところを表示したり消したりする。トグルで4種類ある

T
   CPU Time順でソート

u または U
   ユーザ名またはUIDのユーザを表示する。コマンドラインオプションの -u は effectiveユーザにマッチし、-Uはすべて(any)のユーザ(eal, effective, saved, or filesystem)にマッチする。ユーザ名の先頭にビックリマーク(!)をつけると、そのユーザだけが除外された状態で表示される。つまり!rootなどができる。ユーザのフィルタを解除するには、ユーザ名を入力せず、enterのみを打つべし

V
   フォレストビューモードを有効にする。子プロセスまで表示するモード。便利といえば便利

W
   Write-the-Configuration-File。読んで字のごとく、現在のtopの設定をファイルに保存。次に起動したときに設定が再現される。カレントユーザの~/.config/procps/toprcにファイルが保存される

X
   タスクエリアの数値や文字列の幅を増やす。0でデフォルト。普通は使わない

x
   動きのあったタスクを太文字にする

Y
   指定したPIDのタスクを調査する。内容を表示した出力のページャはviライク。へーこんなことできるの。でも使わないかなー？

y
   行のハイライト表示を行う。だいたいbと同じ

Z
   カラーマッピングを変更する

z
   画面のカラーリングがモノクロになる

< または >
   ソートするフィールドを左右に移動させる。例えばN(PID順にソート)して > すると PIDの隣のUSERの部分がソート対象になる。便利・・・便利？


ファイル
~~~~~~~~~~~~~~~~~~~~

インタラクティブコマンドでWというキーが出てきました。これは、topの設定をファイルに書き出すことができます。細かい話は原文のセクション6をご覧ください。


裏技
~~~~~~~~~~~~~~~~

などと訳してますが、実際は「STUPID TRICK」というセクションです。topで大喜利をやってます。

例えばこんな感じ： `nice -n -10 top -d.09` 。怒られたら [#toperror]_ rootでやる。この状態でWを押すなよ！押すなよ！実行は自己責任で。ほかにはウインドウがはねたりします。おそらくいちばん効果がわかるTRICKはこんなかんじ：

topを起動して、必要ならcを押してそのあとVを押します。右矢印キーでコマンドラインを空にします。最後にjで右寄せします。最後に左矢印キーでコマンドコラムに届きます。押し続けていると左側も出てきます。書いていることは大層な感じですが、やってみるとそういうことねーよく考えたわこれ、ってなります。

.. [#toperror] 怒られる、というのはエラーが出る、みたいな意味です。慣用句なので慣れて下さい。本書読んでる方は解説不要な気がする

.. figure:: ./top2.eps
   :alt: 最終的な画面
   :scale: 100%

.. raw:: latex

    \clearpage

uptime
----------
.. index:: uptime

- システムの起動時間を問い合わせます

解説
~~~~~
原文をベタに訳していきます。
`uptime` は次の情報を一行で表示します。現在の時間、システムの起動時間、何人のユーザがログインしているか、システムの過去1,5,15分のロードアベレージの平均です。

ヘッダの１行は `w` コマンドと同じです。

システムのロードアベレージは起動(runnable)や割り込みされていない(uninterruptable)状態においてどちらか一方プロセスの数の平均です。起動している状態のプロセスはCPUを使っているかCPUの使用を待っているかどちらかです。割り込みされていない状態のプロセスはI/Oアクセスを待っています。例えばディスクのためです。この平均は３回のインターバルに引き継がれます。ロードアベレージはシステムのCPUの数のために正常化されていません。そのため、シングルCPUシステムの1というロードアベレージは、4CPUシステムにおいて75%のアイドルタイムになります。

オプション
~~~~~~~~~~

-p, --pretty
  uptimeを短い形式で表示

-h, --help
  ヘルプを表示

-s, --since
  yyyy-mm-dd HH:MM:SS の形式でシステムが起動した時間を表示。だいたい `who -b` と同じ

-V, --version
  バージョン情報

.. raw:: latex

    \clearpage

vmstat
----------

.. index:: vmstat

- 仮想メモリの状況を知らせてくれる

例
~~~~
１秒ごとに３回まで表示するならこんな感じです。各単語の意味はおわかりですね。

::

  $ vmstat 1 3
   procs -----------memory---------- ---swap-- -----io---- -system-- ------c
   r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id
   2  0      0  11328  20492 145324    0    0    99    10   68   61  0  0 10
   0  0      0  11312  20492 145324    0    0     0     0   94  169  0  0 10
   1  0      0  11312  20492 145324    0    0     0    12   87  164  0  0 10

解説
~~~~~

書き方は下記。countを書くならdelay必須ということを表しています。

::

   vmstat [options] [delay [count]]

vmstatはプロセスとかメモリとかページングとかブロックIOとかトラップとかディスクとかCPUの活動についての情報を表示してくれるコマンドです [#vmstat1]_ 。

.. [#vmstat1] このへん原文を訳してますけど、原文の方が意味がとおるというか日本語にすると逆にわかりにくくなるんですよーなんだかなー

オプション
~~~~~~~~~~

delay
  アップデートする間隔を秒で指定。指定されていなければマシンが起動してからの平均を表示する

count
  アップデートの回数。delayがあってcountがない場合は無限ループします

-a, --active
  アクティブなメモリとインアクティブなメモリを表示します。カーネル2.5.41以降で対応

-f, --forks
  マシンが起動してからのフォーク(forks)の数を表示します。fork, vfork, clone system calls と タスクが作られた数の合計と同等なものが含まれます

-m, --slabs
  slabinfoがを表示します

-n, --one-header
  ヘッダを一回だけ出します。本来は忘れた頃に表示されます

-s, --stats
  さまざまなイベントカウンターとメモリの統計値が表示されます。繰り返しはありません。例：

::

  [root@procps-ng-build ~]# vmstat -s
        1016504 K total memory
         337392 K used memory
         574300 K active memory
         274780 K inactive memory
          77192 K free memory
         120156 K buffer memory
         481764 K swap cache
              0 K total swap
              0 K used swap
              0 K free swap
         111730 non-nice user cpu ticks
            199 nice user cpu ticks
          93800 system cpu ticks
       65580229 idle cpu ticks
           5563 IO-wait cpu ticks
              0 IRQ cpu ticks
            542 softirq cpu ticks
          81874 stolen cpu ticks
         592949 pages paged in
        2488043 pages paged out
              0 pages swapped in
              0 pages swapped out
       17775496 interrupts
       36384343 CPU context switches
     1507129500 boot time
          50242 forks

-d, --disk
  ディスクの統計値を表示。カーネル2.5.70以上が必要。例：

::

  [root@procps-ng-build ~]# vmstat -d
  disk- ------------reads------------ ------------writes----------- -----IO-
         total merged sectors      ms  total merged sectors      ms    cur
  vda    13965      1  837626   13026 169496 134638 3711136  256580      0
  loop0      0      0       0       0      0      0       0       0      0
  loop1      0      0       0       0      0      0       0       0      0
  dm-0    7844      0  342280    7350  29496      0 1265943   35529      0
  dm-1    7819      0  340872    8882  21685      0 1265047   36955      0


-D, --disk-sum
  ディスクの活動についての統計値。例：

::

    [root@procps-ng-build ~]# vmstat -D
              5 disks
              2 partitions
          29628 total reads
              1 merged reads
        1520778 read sectors
          29258 milli reading
         221850 writes
         135179 merged writes
        6277000 written sectors
         330572 milli writing
              0 inprogress IO
            189 milli spent IO


-p, --partition device
  device名を引数にとって、パーテションの詳細な統計値を表示。カーネル2.5.70以上が必要。例：

::

   [root@procps-ng-build ~]# vmstat -p /dev/vda1
   vda1            reads      read sectors      writes  requested writes
                  13816            835706      170425           3731608

-S, --unit character
  出力の切り替え。1000  (k), 1024 (K), 1000000 (m), or 1048576 (M) バイト。swap,blockフィールドには影響を与えない

-t, --timestamp
   毎行タイムスタンプを付加。こんな感じ

::

    [root@procps-ng-build ~]# vmstat -t
    procs -----------memory---------- ------cpu----- -----timestamp-----
     r  b   swpd   free   buff  cache   sy id wa st          UTC
     2  0      0  76608 120464 482176 0  0 100  0  0 2017-10-12 06:54:36


-w, --wide
  ワイドモード。特にメモリの部分が広くなる。幅は80文字くらいあるとよい

-V, --version, -h, --help
  説明不要

VM MODEのためのフィールドの説明
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Procs
^^^^^^

r
  起動中(runnable)のプロセス数(起動時間における起動・待機)
b
  割り込み不可のスリープのプロセスの数(The number of processes in uninterruptible sleep)

Memory
^^^^^^^

swpd
  バーチャルメモリがつかている量
free
  アイドルメモリの量
buff
  バッファとして使っているメモリの量
cache
  キャッシュとして使っているメモリの量
inact
  インアクティブメモリの量(-aオプション)
active
  アクティブメモリの量(-aオプション)

Swap
^^^^^^
si
  ディスクからのスワップインするメモリ量(毎秒)
so
  ディスクにスワップするメモリ量(毎秒)

IO
^^^^
bi
  あるブロックデバイスから受け取るBlocks(毎秒)
bo
  あるブロックデバイスに送るBlocks(毎秒)

System
^^^^^^^
in
  クロックを含めた毎秒の割り込み数
cs
  毎秒のコンテキストスイッチ数

CPU
^^^^^

下記は、合計CPU時間のパーセンテージである

us
  カーネルじゃないコードの実行に費やした時間(user time。nice timeも含む)
sy
  カールコードの実行に費やした時間(system time)
id
  idle時間。カーネル2.5.41以前はIO-wait timeも含めていた
wa
  IOにかかった時間。カーネル2.5.41以前はidle時間も含めていた
st
  バーチャルマシーンに奪われた時間。カーネル2.6.11以前は不明(unknown)

DISK MODEのためのフィールドの説明
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

Reads:

total
  完全に成功した読み込み(reads)の合計
merged
  読み込みを集めた(ひとつのI/Oの結果)
sectors
  成功したセクタの読み込み
ms
  読み取りに費やしたミリセカンド

Writes:

total
  完全に成功した書き込み(writes)の合計
merged
  書き込みをを集めた(ひとつのI/Oの結果)
sectors
  成功したセクタの書き込み
ms
  書き込みに費やしたミリセカンド

IO:

cur
  I/Oの進捗
s
  I/Oに費やした時間

ディスクパーティションモードのためのフィールドの説明
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

reads
  このパーティションについて読まれた総数
read sectors
  パーティションにために読まれたセクタの量
writes
  このパーティションについて書かれた総量
requested writes
  パーティションのために作られた書き込み要求の総数

SLAB MODEのためのフィールドの説明
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
cache
  キャッシュ名
num
  現在のアクティブオブジェクトの数
total
  有効なオブジェクトの数
size
  それぞれのオブジェクトのサイズ
pages
  少なくとも一つのアクティブオブジェクトのページ数

その他
~~~~~~~

vmstat は特別な権限を必要としません。vmstatの結果は、システムのボトルネックを見つけることを手助けしてくれます。Linuxのvmstatは起動プロセスに自分自身をカウントしません。
すべてのリナックスのブロック(Linux blocks)は現在のところ1024バイトです。古いカーネルだと512や2048,4096バイトで表示しているかもしれません。
Procps 3.1.9からvmstatはユニット(k,K,m,M)を選べます。デフォルトはK(1024Bytes)です。
vmstatに関連するコマンドは、free, iostat [#sysstet]_ , mpstat [#sysstet]_ , ps, sar [#sysstet]_ , topがあります。

..  [#sysstet] sysstatパッケージに含まれています。http://sebastien.godard.pagesperso-orange.fr/

.. raw:: latex

    \clearpage

w
----------
.. index:: w

- 誰がログインしてて何をしているのか表示してくれる

個人的に短くて好きなコマンドです。いつから誰がどこのサーバからログインしているかわかります。TTYが分かるので `write` コマンドでメッセージが飛ばせます。下記は本書のビルドサーバで試したもの。

::

  [root@procps-ng-build ~]# w
  12:12:09 up 6 days, 21:07,  2 users,  load average: 0.03, 0.03, 0.05
  USER     TTY        LOGIN@   IDLE   JCPU   PCPU WHAT
  root     pts/0     11:13    4:49   0.05s  0.05s -bash
  root     pts/1     11:21    1.00s  0.23s  0.00s w

オプション
~~~~~~~~~~

-h, --no-header
  ヘッダ非表示

-u, --no-current
  現在のプロセスとCPU時間を出しにくい時、ユーザ名を無視する。実際にやってみるなら `su` して `w` と `w -u` とコマンドを打つ。実際に打ったけど違いがわからなかった

-s, --short
  短いフォーマットを使う

-f, --from
  from(リモートホスト名)を表示したり非表示にしたりする。procps-ngではデフォルトでfromを表示しない。procpsではデフォルトで表示する

--help
  ヘルプを表示

-i, --ip-addr
  fromフィールドにホスト名を表示する代わりにIPアドレスを表示する

-V, --version
  バージョン情報を表示

-o, --old-style
  オールドスタイルを適用。1分未満アイドル状態だとブランクスペースを表示

環境変数
~~~~~~~~~

PROCPS_USERLEN
  ユーザ名のコラム幅のデフォルトを上書き。デフォルト8

PROCPS_FROMLEN
  fromコラムの幅のデフォルトを上書き。デフォルト16。オプションにしちゃえばいいような気もするけどどうなんだろ。実際に使うにはこんな感じで

.. code-block:: sh

   PROCPS_FROMLEN=40 w -f

.. raw:: latex

    \clearpage

watch
----------
.. index:: watch

- 定期的にプログラムを実行してフルスクリーンで表示してくれる

解説
~~~~~~

時々書き換わるファイルや標準出力を定期的に見るときに使います。書き方：

::

   watch [options] command


簡単に使ってみましょう。

.. code-block:: sh

   $ watch -d -n 1 cat /tmp/tmpfile

とすると /tmp/tmpfile を１秒ごとに監視して、変化があったところを白抜きで表示してくれます。時々刻々と変化するコマンド例えば：

.. code-block:: sh

   $ watch -d -n 1 date

とかするとよいでしょう。

オプション
~~~~~~~~~~

-d, --differences [permanent]
  アップデートしたときに違いがあればハイライト表示

-n, --interval seconds
  更新秒数を指定。0.1秒単位まで指定可能

-p, --precise
  クロックと（できるだけ）同期して指定間隔でコマンドを実行する

-t, --no-title
  ヘッダを消す

-b, --beep
  コマンドの終了時のステータスコードが0で無ければビープを鳴らす

-e, --errexit
  コマンドでエラーが発生したら止まって、何かキーを押したらwatchを終了

-g, --chgexit
  コマンドに変化があったらwatchを終了。便利・・・？

-c, --color
  ANSIカラーやスタイルシーケンスを解釈する

-x, --exec
  コマンド実行時に `sh -c` で実行する

-h, --help, -v, --version
  いつもの

実行例
~~~~~~~

クオートの効果を見るためにこんなコマンドを実行してみましょう。

.. code-block:: sh

   $ watch echo $$
   $ watch echo '$$'
   $ watch echo "'"'$$'"'"

-pオプションの効果を見るためにこんなコマンドもあります。

.. code-block:: sh

   $ watch -n 10 sleep 1
   $ watch -p -n 10 sleep 1

管理者のために最新のカーネルがインストールされていることを見ることができます。

.. code-block:: sh

   $ watch uname -r


おわりに
========

あとがき
---------
ここまで読んでいただきありがとうございました。拙書「解説Coreutils」と同じノリで今回の解説Procps-ng、いかがだったでしょうか。Coreutilsよりもコマンド数が少ないので、薄い本に仕上がりました。topコマンド書くの疲れた・・・ってのが、おおざっぱな印象です。
間違いを発見したり、もっと内容を良くできると思った方、筆者 [#hissha]_ または https://github.com/nanaka-inside/kaisetsu-procps-ng までPRをいただけると頂けると大変ありがたいです。第2版が出るその日までさようなら。なんでこんなにあっさりしたあとがきなのかって？それはまだ本文が書き上がってないからだよ！

.. [#hissha] twitter: @tboffice


参考文献
--------
- 「procps-ng / procps · GitLab」,<https://gitlab.com/procps-ng/procps>
- 「英語学習・TOEIC対策・英辞郎 on the WEB | アルク」,<http://www.alc.co.jp/>
- 「参考文献の書き方」,<http://web.ydu.edu.tw/~uchiyama/ron/ron_04.html#web_a>
