
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
バイトで表示したい場合は、 `-b` または `--bytes` そのあとは `-k` 、 `-m` 、 `-g` 、 `--tera` と続きます。 `-h` または `--human` でひゅーまんりりーだぶるです。

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

- プロセスのPIDにシグナルを送ります

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

留意することとして、ビルドインコマンドのkillがあるかもしれません。そのコマンドが必要なときはコンフリクトを解決するために `/bin/kill` と打ちましょう、って書いてあるんですけどなんだかなーという感じです。あれ？そういえばこのコマンド、Coreutilsにもありますね？どういうことでしょうかねえ？どうやったら実行できるんでしょうか！考えてみよう！

例
~~~~
kill -9 -1
   killできるすべてのプロセスをkillする。うひ～

kill -l 11
   11番のシグナルをシグナル名に変換

kill 123 543 2341 3453
   デフォルトシグナルである SIGTERM を指定されているプロセスIDに送る


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
    察して

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

あのプロセスってどこのディレクトリで起動しているんだろう？というのが分かります。唯一のオプション `-V` はバージョン情報を表示します。おしまいです。


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
   ファストモードです。未実装。まじかよ

-i, --interactive
   対話的に使えます。数字を打ち込んで優先度を変更できます

-l, --list
   すべてのシグナル名を表示します

-L, --table
   すべてのシグナル名を良い感じに表で表示します

-n, --no-action
   なにもしません。シュミレーションをするだけでシステムを変更しません

-v, --verbose
   詳細になります。説明がなされます。なんのこっちゃ

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

Linux触っている人にはお馴染みのコマンドではないでしょうか。え？さわったことない？起動してるLinuxのカーネルのパラメータを変更できるコマンドです。
何が嬉しいかって、port range広げたり、ip_forwardを1にしてルータを作ったりするんですよ。当たり前田のクラッカー。分からないひとはぐぐってね。

このコマンドのパラメータは、 `/proc/sys` のディレクトリ下に対して有効です。依存関係で Procfsが必要です。sysctlのデータを読んだり書き込みしたりできます。


オプション
~~~~~~~~~~~

variable
  キーの名前。例えば kernel.ostype。セパレータである / は . に置換されます

variable=value
  キーを設定します。-wパラメータをつけて、ダブルクオートでくくってね！shellがパースするからね！

-n, --values  キー名を表示しません

-e, --ignore  知らないキーがあってもエラーを出しません

-N, --names  名前だけを表示します。シェルでプログラミングするとき便利でしょう

-q, --quiet  値を表示しません

-w, --write  sysctlの設定を変更するときに使うオプションです

-p[FILE],--load[=FILE]
  FILEが無いとき、設定されているファイルや `/etc/sysctl.conf` ファイルから設定をロードします。
  ファイル名は標準入力からデータを読み取ることを意味しています。
  FILEは正規表現として与えられることができます。

-a, --all  すべての設定を表示します。キーなんかいちいち覚えていないときに使います

--deprecated  --allと一緒に使うことによって廃止される予定のあるパラメータも表示

-b, --binary  改行しないで表示

--system  下記のすべての設定をロードする

::

    /run/sysctl.d/*.conf
    /etc/sysctl.d/*.conf
    /usr/local/lib/sysctl.d/*.conf
    /usr/lib/sysctl.d/*.conf
    /lib/sysctl.d/*.conf
    /etc/sysctl.conf

-r, --pattern pattern  パターンを指定して検索。拡張正規表現を使う

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


tload
----------
.. index:: tload

- ロードアベレージをグラフィカルに表示

実行すると、画面一面にロードアベレージのグラフが表示されます。静かなサーバは何も表示されないと思います。どんな見た目かは自分で確かめてみて下さい。



top
----------
.. index:: top

- リアルタイムで動作しているプログラムを表示します [#topmac]_

.. [#topmac] Macの人はどうしたらいいかって？Macのtopコマンドは、劣化したProcng-ngのtopだと思っていただければ、遠からず近からず。近からず遠からず。関係ないけどmacのtopのデフォルトの表示がいかついですね（雑な感想

　やってまいりました、procps-ngの花形 `top` です。manコマンドを打つと1500行くらいあるんですねー渋いですねー。
ガッツリ解説していきます。manpageを愚直に訳すと冗長なるので、起動時の画面とコマンドラインオプションを軽く説明してから、起動後のキーバインドを説明します。
　さて、 `top` を実行するとこんな感じです。

.. image:: ./top1.png

　お馴染みのサマリー・フィールドとコラムのヘッダ・タスクエリアがあります。へー最新のtopってこんなになってるんだ・・・という感想はおいといて、topコマンドを打った時の画面の説明です。ターミナルの大きさに合わせてよろしく表示してくれます。画面の外に隠れているプロセス名なんかは、左右矢印キーを押すと見ることができます。マジで、本当だ、知らなかった。ページャーもちゃんとあって、EndやHomeやPageUp、PageDownキーも押せます。本家procpsは非対応なのでご注意。topのmanpageは大きく分けて、コマンドラインオプション、サマリー、フィールド・コラム表示、インタラクティブコマンド、ウインドウを行ったきり来たり、ファイルの仕様、裏技があります。本書では、 `top` 起動後のキーバインドを中心に解説していきます。
　ちなみに終了は c か ctrl-c (kill) です。一応。


コマンドラインオプション
~~~~~~~~~~~~~~~~~~~~~~~

　起動時のオプションについて説明します。こうです。「|」は、「または」という意味です。

::

     -hv|-bcEHiOSs1 -d secs -n max -u|U user -p pid -o fld -w [cols]


-h または -v
   helpという名のコマンドラインオプションを表示します。上のオプションがでてきます。バージョンも表示されます

-b
   バッチモードです。topの表示を他のプログラムやファイルに送る時に便利。無限に走り続けるので、終了するときは -n オプションで回数を指定するか、killしてください

-c
   topを打った後cを打った状態で起動します。通常、実行しているプロセスのコマンドラインが表示されますが、この場合はプログラム名のみが表示されます

-d 秒数
   更新間隔を指定します。0.1秒単位で指定できます。 -d 12.3 とかできます。起動後、d や s で更新間隔を再指定できます。0も打ててしまう。ブラクラかよ

-E
   サマリーエリアのメモリの単位を指定します。キビバイトで表示するなら `top -E k` とする。k以外は k,m,g,t,p,eが指定可能。それぞれキビ、メビ、ギビ、テビ、ペビ、エビ(exbi)。top起動後、Eで再指定可能

-H
   個別のスレッドを表示。それぞれのプロセスのスレッドが表示されるようになります。Hで切り替え

-i
   更新間隔までにCPUを使ってないタスクを非表示。つまり活動しているプロセスだけを表示。iで切り替え

-n 数字
   表示する行数を制限します

-o fieldname
   fieldnameでソート。実行例は `top -o +PID` とか `top -o -PID` とか。プラスマイナスを付けてオーダーを指定。両方つけるとどうなるの？シンタックスエラーになるんじゃないかなぁ？

-O
   fieldnameを表示します。それだけ。上のコマンドの filedname を調べるときに使う

-p
   PIDを指定して表示。たとえばこんな感じ： `top -p1 -p2 -p3` または `top -p1,2,3` 。PIDは、20個まで指定可能。PIDには、0も指定できます。何が起こるかやってみましょう。解除するには、Uかuを打ってenterを押す

-s
   設定ファイルを読み込まないセキュアモードで起動 [#securemode]_ 。topに設定ファイルってあるんですねーあとで解説します

.. [#securemode] 書いてて思ったんですけど、セキュアラモードってかわいくないですか（急に何を言い出すんだこいつ

-S
   累積モードで表示。あとで説明するかも

-u または -U
   ユーザ名を指定すると、そのユーザだけのプロセスを表示する。よく使うかも。uまたはUで切り替え可能。大文字(U)小文字(u)の違いがあるけど省略

-w [number]
   表示する幅を指定。バッチモードで使う

-1
   コアが複数あるメモリでそれぞれの使用率が見れる。1で切り替え。コア数が多すぎるとそれだけで画面が埋まる




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

説明のため、a,b,c,dという記号を付けました。a は us + ni を足したパーセンテージ。bは sy のパーセンテージ。cは合計。dはグラフです。us ってなんだよ、って感じですが、propsのときはこんな感じで表示されることを思い出しましょう。知らなくても知っていたことにしましょう。

::

   Cpu(s):  0.0%us,  0.0%sy,  0.0%ni, 99.9%id,  0.0%wa,  0.0%hi,  0.0%si,  0.0%st

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
次はメモリ使用量です。この辺も深みにはまるのでサクッと解説だけにとどめておきます。たとえばこんな表示だったとします。

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


もうお分かりですね。実は、フィールドは実は58個もあります。fキーを押すと、どのフィールドを表示するか決める画面に移ります。全部説明すると大変なことになるので、操作の方法だけ。
fキーを押すと「Fields Management for window」と出てくるので解説の通りに操作します。キーバインドをご紹介。

d または <space>
   表示・非表示の選択

s
   ソート順の決定

q または <Esc>
   メニューの終了


インタラクティブコマンド
~~~~~~~~~~~~~~~~~~~~~~~~

インタラクティブコマンドです。topのコマンドを打ってから打つキーです。上でも紹介したじゃないかって？fだけ特殊なんで別項としてあります。それ以外のキーを一気に紹介します。キーを打って自分だけの表示を楽しもう（原文にこんなこと書いてないけど一応
記号、数字、アルファベットの順番で解説していきます。


<Enter> または <space>
   画面をリフレッシュする。新たな気持で

? または h
   ヘルプを表示します。困ったらまずこれ

=
   タスクリミットを解除

0
   値が0のところを消したり表示したりする

1
   複数コアがあるCPUの場合はそれぞれの使用量を表示する。コア数が多いときはまあそのとき考えよう・・・

2
   1をおした後に使う。NUMA(Non-Uniform Memory Access)の状態が表示される。詳しくはググって

3
   2をおした後に使う。NUMAの状態を展開する

A
   プロセスリストのところが４段になって、メモリ使用率順、CPU使用率順、PID順みたいのがそれぞれ表示される。便利（個人の感想です）

B
   表示上の太字が普通の書体になったりもとに戻ったりする

C
   スクロールの座標を表示。打ってみれば分かる

d
   画面の更新間隔を更新する時間を指定。デフォルトは3秒。sでも可能。

E
   サマリエリアのメモリの容量のスケールを変更する。何度も押すと順次変わっていく。キビバイトからエクシビバイトまで切り替える

e
   タスクエリアのメモリの容量のスケールを変更する。何度も押すと順次変わっていく。キロバイトからペビバイトまで切り替える

f または F
   前項のフィールドコラムをご覧ください。自分でフィールドのカスタマイズができる機能です

g
   aキーのときの４段でてきたそれぞれを選択するモード。1から4の数字を打ち込んでやってください

H
   スレッド表示モードに切り替えます。全てのプロセスが見えるようになります

h
   割り当ては、ありません

i
   アイドルモードのタスクを表示するかの切り替え。何も表示されなかったらenterでも連打すればいいんじゃないですかね。どうなるか察しがつきましたか？

J
   文字データの部分を右詰めにする。じゃすてぃふぁーい！のJ

j
   数字データの部分を左詰めにする。じゃすてぃふぁーい!のj

k
   タスクにシグナルを送ることができる




s
   画面の更新間隔を更新する時間を指定。デフォルトは3秒。dでも可能。



H
   hoge

I
   hoge


q
   topを終了

r
   タスクをreniceする

W
   hoge

X
   hoge

Y
   hoge

Z
   カラーマッピングを変更する


l
   一番最初の行を表示したり消したりする。topの一番最初の行ってなんでしたっけ？思い出してみよう！

t
   taskとCPUのところを表示したり消したりする。トグルで4種類ある

m
   メモリ・swapのところを表示したり消したりする

b
   動きのあったタスクを反転表示（しているように見える）

x
   動きのあったタスクを太文字にする

y
   行のハイライト表示を行う。だいたいbと同じ

z
   画面のカラーリングがモノクロになる

c
   hoge

f または F

o または O

s

u または U

V

n または 数字


M

N

P

T

< または >


R



カラーマッピング
^^^^^^^^^^^^^^



ウインドウズのためのコマンド
^^^^^^^^^^^^^^^^^^^^^^^^


ウインドウのスクロール
^^^^^^^^^^^^^^^^^^^^

検索
^^^^^



ファイル
　システム設定ファイル
　個人設定ファイル
　インスペクト追加ファイル

裏技
　といっているが実際はSTUPID TRICK


uptime
----------
.. index:: uptime

- システムの起動時間を問い合わせます

個人的には w でいいんじゃないかなーと思いつつオプションを解説します。

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

とすると /tmp/tmpfile を１秒ごとに監視して、変化があったところを白抜きで表示してくれます。時々刻々と変化するコマンド例えば：

.. code-block:: sh

   $ watch -d -n 1 date

とかするとよいでしょう。
マニュアルの実行例に下記のような記述があり楽しい [#uname-r]_ 。

::

  管理者による最新のカーネルのインストール状況を監視する:
  watch uname -r
  (ただの冗談です)

.. [#uname-r] https://linuxjm.osdn.jp/html/procps/man1/watch.1.html
