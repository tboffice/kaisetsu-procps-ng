
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

Send a signal to a process based on PID


pgrep
----------
.. index:: pgrep

List processes based on name or other attributes


pkill
----------
.. index:: pkill

Send a signal to a process based on name or other attributes


pmap
----------
.. index:: pmap

Report memory map of a process


ps
----------
.. index:: ps

 Report information of processes


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
