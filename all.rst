
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
   スワップなしに、どれくらいメモリが新しいアプリケーションを開始するために必要かの概算量(todo)


オプション
~~~~~~~~~~

まとめて解説します。 バイトで表示したい場合は、 `-b` または `--bytes` そのあとは `-k` 、 `-m` 、 `-g` 、 `--tera` と続きます。
 `-h` または `--human` でひゅーまんりりーだぶるです。

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
                 total        used        free      shared  buff/cache   available
   Mem:            488         331           6          28         150         102
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


kill
----------
.. index:: kill

- PIDを基にしたプロセスにシグナルを送ります



解説
~~~~~~

コマンドの書き方は下記です

::

   kill [options] <pid> [...]

オプション
~~~~~~~~~~

<pid> [...]
   リストされたすべての<pid>にシグナルを送ります。

-<signal>, -s <signal>, --signal <signal>
   規定のシグナルが送られる。このシグナルは名前または数字によって定義される。シグナルの挙動はsignal(7)に説明されています。 `man 7 signal` で見てみましょう。

-l, --list [signal]
   シグナルの一覧を表示します

-L, --table
   シグナルの一覧を良い感じに表で示します。が実装されてないぽい。おい！！Linux互換性のためにあるんですって奥さん（誰だよ

メモ書きとして、ビルドインコマンドのkillがあるかもしれない。そのコマンドが必要なときはコンフリクトを解決するために `/bin/kill` と打ちましょう、って書いてあるんですけどなんだかなーという感じです。あれ？そういえばこのコマンド、Coreutilsにもありますね？どういうことでしょうかねえ？どうやったら実行できるんでしょうか！考えてみよう！

例
~~~~
kill -9 -1
   killできるすべてのプロセスをkillする。うひ～

kill -l 11
   11番のシグナルをシグナル名に変換

kill 123 543 2341 3453
   デフォルトシグナルである SIGTERM を指定されているプロセスに送る


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
   定義されたシグナルを名前が一致したプロセスに送ります。番号でもシンボル(SIGTERMとか)でもOK。pkillのみのオプション

-c, --count
   通常の出力を抑制します。代わりにマッチしたプロセスのカウントを表示します。無ければ0

-d, --delimiter delimiter
   デリミタを指定します。通常は改行です。あとはわかるな

-f, --full
   patternはプロセス名にマッチするが、-fにするとフルコマンドでマッチするようになります

-g, --pgroup pgrp,...
   マッチしたpatternのグループIDのリストが表示される

-G, --group gid
   マッチした本当のgroup idが表示される

-i, --ignore-case
   ignore-caseです

-l, --list-name
   プロセス名とともに、プロセスidも表示される

-a, --list-full
   フルコマンドラインも表示

-n, --newest
    最も新しいプロセスが選択される

-o, --oldest
    省略

-p, --parent ppid
   親プロセスのidが表示される

-s, --session sid
   プロセスのセッションidが表示される。セッションidが0の場合はpgrepまたはpkill自身のセッションidである

-t, --terminal term
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
   与えられた名前空間にのみマッチする。有効な名前空間は、 ipc, mnt, net, pid, user , utsである

-V, --version
   バージョン情報を表示

-h, --help
   ヘルプを表示

pmap
----------
.. index:: pmap

- プロセスのメモリマップを表示

解説
~~~~~~

コマンドの書き方は下記です。patternは必須です。

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
   拡張したフォーマットを示します

-d, --device
   デバイスフォーマットを示します

-q, --quiet
   ヘッダとフッタを表示しない

-A, --range low,high
   lowとhighのアドレスレンジを指定して制限をかける。数字の間にカンマを間に書くのじゃ

-X
   -xオプションよりもさらに詳細に示す。 フォーマット変更は `/proc/PID/smaps` による

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


ps
----------
.. index:: ps

- プロセスの情報を表示します


解説
~~~~~~

コマンドの書き方は下記です。

::

   ps [options]


`ps` は現在動作中のプロセスの選択についての情報を表示する。選択や情報を表示を繰り返しアップデートするのがおこのみであれば、top(1)を使え。

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

pwdx
----------
.. index:: pwx

- 特定のプロセスの今いるディレクトリを表示

解説
~~~~~~

コマンドの書き方は下記です。

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

あのプロセスってどこのディレクトリで起動しているんだろう？というのが分かります。 `-V` オプションはバージョン情報を表示します。おしまいです。


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

デフォルトの `snice` の優先度は +4です。優先度の範囲は +20(最も遅い) から -20 (最も早い) です。優先度を上げるときはroot的なユーザ権限が必要です。


解説
~~~~~~

-f, --fast
   ファストモードです。未実装。まじかよ。

-i, --interactive
   対話的に使えます。数字を打ち込んで優先度を変更できます

-l, --list
   すべてのシグナル名を表示します

-L, --table
   すべてのシグナル名を良い感じに表で表示します

-n, --no-action
   なにもしません。シュミレーションをするだけでシステムを変更しません。

-v, --verbose
   詳細になります。説明がなされます。なんのこっちゃ。

-w, --warnings
   警告が有効になります。未実装。なんでや！

-h, --help
    ヘルプを表示します

-V, --version
   バージョンを表示します


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
   リフレッシュ間隔を秒で設定します。デフォルトは３秒です。qでプログラムを終了します

-s, --sort=S
   Sで並べ替えます。一文字で指定します。デフォルトは o でオブジェクトの番号でソートします

-o, --once
   一度表示してプログラムを終了します

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
     9333   8744   0%    0.08K    183       51       732K selinux_inode_security
     8385   8366   0%    0.10K    215       39       860K buffer_head


sysctl
----------
.. index:: sysctl

- 起動しているときにカーネルのパラメータを変更する

解説
~~~~~~

コマンドの書き方は下記です。

::

   sysctl [options] [variable[=value]] [...]
   sysctl -p [file or regexp] [...]

Linux触っている人にはお馴染みのコマンドではないでしょうか。え？さわったことない？起動してるLinuxのカーネルのパラメータを変更できるコマンドです。何が嬉しいかって、port range広げるんですよ。当たり前田のクラッカー。分からないひとはぐぐってね。

このコマンドのパラメータは、 `/proc/sys` のディレクトリ下に対して有効です。依存関係で Procfsが必要です。sysctlのデータを読んだり書き込みしたりできます。


オプション
~~~~~~~~~~~

variable
   キーの名前。例えば kernel.ostype。セパレータである / は . に置換されます

variable=value
   キーを設定します。-wパラメータをつけて、ダブルクオートでくくってね！shellがパースするからね！

-n, --values
   キー名を表示しません

-e, --ignore
   知らないキーがあってもエラーを出しません

-N, --names
   名前だけを表示します。シェルでプログラミングするとき便利でしょう

-q, --quiet
   値を表示しません

-w, --write
   sysctlの設定を変更するときに使うオプションです

-p[FILE], --load[=FILE]
   FILEが無いとき、設定されているファイルや `/etc/sysctl.conf` ファイルから設定をロードします。ファイル名は標準入力からデータを読み取ることを意味しています。
   FILEは正規表現として与えられることができます。

-a, --all
   すべての設定を表示します。キーなんかいちいち覚えていないときに使います

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

エイリアスとして、 -A は -a 、-dは-h、-fは-p 、-Xは-a で、-x -o はBSDの互換性のために用意

例
~~~

::

   /sbin/sysctl -a
   /sbin/sysctl -n kernel.hostname
   /sbin/sysctl -w kernel.domainname="example.com"
   /sbin/sysctl -p/etc/sysctl.conf
   /sbin/sysctl -a --pattern 'net.ipv4.conf.(eth|wlan)0.arp'
   /sbin/sysctl --system --pattern '^net.ipv6'


tload
----------
.. index:: tload

- ロードアベレージをグラフィカルに表示

実行すると、画面一面にロードアベレージのグラフが表示されます。静かなサーバは何も表示されないと思います。

top
----------
.. index:: top

- リアルタイムで動作しているプログラムを表示します

みなさんお馴染み `top` コマンドです。奥が深いので詳細は割愛します。製本版にはきちんと説明を入れたい。manページだけで1500行くらいあって辛い。

uptime
----------
.. index:: uptime

- システムの起動時間を問い合わせます

個人的には `w` でいいんじゃないかなーと思いつつオプションを解説します。

-p  uptimeを短い形式で表示

-h  ヘルプを表示

-s  yyyy-mm-dd HH:MM:SS の形式でシステムが起動した時間を表示。だいたい `who -b` と同じ

-V  バージョン情報

vmstat
----------

.. index:: vmstat

- 仮想メモリの状況を知らせてくれる

１秒ごとに３回まで表示するならこんな感じです。各単語の意味はおわかりですね。

::

  $ vmstat 1 3
   procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
   r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
   2  0      0  11328  20492 145324    0    0    99    10   68   61  0  0 100  0  0
   0  0      0  11312  20492 145324    0    0     0     0   94  169  0  0 100  0  0
   1  0      0  11312  20492 145324    0    0     0    12   87  164  0  0 100  0  0


w
----------
.. index:: w

- 誰がログインしてて何をしているのか表示してくれる

個人的に短くて好きなコマンドです。いつから誰がどこのサーバからログインしているかわかります。TTYが分かるので `write` コマンドでメッセージが飛ばせます。 -i というオプションでIPアドレスのまま表示してくれます。下記は本書のビルドサーバで試したもの。さくらのVPSからsshを繋いでいることがわかります。

::

   $ w
   17:20:27 up 7 days,  1:16,  3 users,  load average: 0.00, 0.01, 0.05
   USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT
   root     pts/0    ik1-302-11037.vs 19Apr17  5days  0.07s  0.07s -bash
   root     pts/1    ik1-302-11037.vs 19Apr17 25:26m  0.12s  0.12s -bash
   root     pts/2    ik1-302-11037.vs Tue15    3.00s  0.55s  0.54s -bash


watch
----------
.. index:: watch

- 定期的にプログラムを実行してフルスクリーンで表示してくれる

時々書き換わってしまうファイルを定期的に見るときとかに使います。

.. code-block:: sh

   $ watch -d -n 1 cat /tmp/tmpfile

とすると /tmp/tmpfile を１秒ごとに監視して、変化があったところを白抜きで表示してくれます。fileじゃなくてもコマンドの結果にも有効なので

.. code-block:: sh

   $ watch -d -n 1 date

とかするとよいでしょう。
マニュアルの実行例に下記のような記述があり楽しい。

::

  管理者による最新のカーネルのインストール状況を監視する:
  watch uname -r
  (ただの冗談です)
