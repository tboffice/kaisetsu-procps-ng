
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
   探したすべてのプロセスが見つからなかった。詳しくは42でググる


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

これらを混ぜて使うといかんです。ps auxをps -auxと書くと、 ps -a -u -x になって意味が違います。どちらかに統一しましょう。


pwdx
----------
.. index:: pwx

Report current directory of a process


skill
----------
.. index:: skill
Obsolete version of pgrep/pkill


slabtop
----------
.. index:: slabtop

Display kernel slab cache information in real time


snice
----------
.. index:: snice

Renice a process


sysctl
----------
.. index:: sysctl

Read or Write kernel parameters at run-time


tload
----------
.. index:: tload

Graphical representation of system load average


top
----------
.. index:: top
Dynamic real-time view of running processes


uptime
----------
.. index:: uptime

 Display how long the system has been running


vmstat
----------
.. index:: vmstat

Report virtual memory statistics


w
----------
.. index:: w

Report logged in users and what they are doing


watch
----------
.. index:: watch

Execute a program periodically, showing output fullscreen

あとがき
=========

おつかれさまでした。プレビューでしたが楽しんでいただけましたでしょうか。多分表紙買いだったのでは？と疑っていますがまあなんとかなるでしょう（意味不明
