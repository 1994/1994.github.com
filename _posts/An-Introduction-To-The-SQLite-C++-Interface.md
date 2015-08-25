title: SQLite C/C++接口简介
date: 2015-05-16 18:30:00
categories: Develop
tags: [SQLite3,翻译,C/C++]
---

>翻译自[An Introduction To The SQLite C/C++ Interface][1],建议阅读原文

##摘要
接下来的两个对象和八个方法包含了SQLite接口的基本要素:

 - [sqlite3][2]	   数据库连接对象. 由sqlite3_open()创建，由sqlite3_close()销毁。
 - [sqlite3_stmt][3]		预编译语句对象. 由[sqlite3_prepare()][4]创建，由[sqlite3_finalize()][5]销毁。
 - [sqlite3_open()][6]		打开已存在或者新建数据库的连接。[sqlite3][7]的构造函数。
 - [sqlite3_prepare()][8]	将SQL文本编译成字节码，以便于执行查询或者更新数据库的操作 [sqlite3_stmt][9]的构造函数。
 - [sqlite3_bind()][10]		将应用程序的数据与原始SQL中的[参数][11]绑定
 - [sqlite3_step()][12]		执行[sqlite3_stmt][13]到下一个结果或者结束
 - [sqlite3_column()][14]		当前结果行所在的列值
 - [sqlite3_finalize()][15]       [sqlite3_stmt][16]的析构函数。
 - [sqlite3_close()][17]		    [sqlite3][18]的析构函数。
 - [sqlite3_exec()][19]		一个包装函数，为一条或多条SQL语句包装了[sqlite3_prepare()][20], [sqlite3_step()][21], [sqlite3_column()][22], 和[sqlite3_finalize()][23]。

<!-- more --> 
##简介
早期的SQLite只支持五个C/C++接口,所以非常容易学习。但是随着SQLite的不断壮大，新的C/C++接口不断加入，目前已有超过200个不同的接口。这能轻易压倒一个新手程序员。幸运的是，大部分SQLite的C/C++接口非常专业并且你不需要去关心。尽管有如此多的切入点，SQLite的核心API依然比较简单易于学习，易于编程。这篇文章旨在提供所有必需的背景信息以便于让你理解SQLite是如何工作的。

这份单独的文档，[SQLite C/C++接口][24]，提供了所有SQLite C/C++ 接口的详细说明。当读者理解了SQLite操作的基本原则，这份文档可以作为参考指南使用。本文章目的仅仅是介绍，而不是SQLite API 的完整或者权威的指南。


##核心对象和接口
SQL数据库引擎的主要任务是执行SQL语句。为了实现这个目的，开发者需要知道这两个对象：

 - [数据库连接][25]对象：sqlite3
 - [预编译语句][26]对象：sqlite3_stmt
 
严格得讲，有了便捷的包装接口预编译语句对象，sqlite3_exec 或者 sqlite3_get_table之后，[预编译语句][27]对象不再是必须的。因为可以使用这些便捷的包装器封装和隐藏[预编译语句][28]对象。但是要充分利用SQLite需要理解预编译语句对象。

[数据库连接][29]和[预编译语句][30]对象是由以下列出的一部分接口控制：

 - [sqlite3_open()][31]
 - [sqlite3_prepare()][32]
 - [sqlite3_step()][33]
 - [sqlite3_column()][34]
 - [sqlite3_finalize()][35]
 - [sqlite3_close()][36]

这六个C/C++接口程序和上述的两个对象来自于SQLite基础功能的核心。理解了它们，开发者将能更好得使用SQLite。

请注意上述的程序是概念性的而不是实际上的。这些程序大都存在多个版本。比如上面列表只显示了单个名为 [sqlite3_open()][37]的程序，但实际上有三个独立的程序以不同的方式完成同样的事情： [sqlite3_open()][38], [sqlite3_open16()][39] 和 [sqlite3_open_v2()][40]。另外列表里还提及了 [sqlite3_column()][41],但实际上并没有这样的程序存在。列表中显示的"sqlite3_column()"代表的是这个程序整个家族，它们被用于提取不同类型的列数据。


这里总结一下核心接口会做什么：


 - [sqlite3_open()][42]	用来打开一个数据库文件的连接并返回数据库连接对象。这通常是应用调用的第一个SQLite API，低于大多数其他的SQLite API这是先决条件。许多SQLite接口需要一个指向数据库连接的指针作为第一个参数，也可以认为是数据库连接对象的方法。sqlite3_open()是数据库连接对象的构造方法。


 - [sqlite3_prepare()][43]	将SQ文本转换为预编译语句对象并返回该对象的指针。这个接口需要一个由sqlite3_open()创建的指向数据库连接的指针和一段包含SQL语句的文本。这个API并非真正得执行SQL语句，仅仅准备需要执的SQL语句。
把每条SQL语句想象成一个小的计算机程序。sqlite3_prepare()的目的是将程序编译成目标代码。预编译语句是目标代码。接着sqlite3_step()接口运行目标代码并得到结果。

请注意对于新的应用不建议使用sqlite3_prepare()，而应使用架构更新的程序sqlite3_prepare_v2()代替。


 - [sqlite3_step()][44] 用来执行之前由sqlite3_prepare()接口创建的预编译语句。这语句执行到第一行可用的位置。若要执行结果的第二行，需要再次调用sqlite3_step()。继续调用 sqlite3_step()直到语句结束。对于那些不返回结果的语句（比如： INSERT, UPDATE, 或者DELETE），只需调用一次sqlite3_step()。


 - [sqlite3_column()][45]	从sqlite3_step()执行的准备语句得到的结果集的当前行中返回一个列。每次sqlite3_step()得到一个结果集的列后，这个过程就可以被多次调用去查询这个行的各列的值。正如上面提到的那样，在SQLite API中并没有这个一个"sqlite3_column()"函数。取而代之的是整个 "sqlite3_column()"家族，用于返回不同数据类型的结果。在这个家族里也有函数用来返回结果的大小（如果类型是string或者BLOB）和结果集列的数量。

             - sqlite3_column_blob()
             - sqlite3_column_bytes()
             - sqlite3_column_bytes16()
             - sqlite3_column_count()
             - sqlite3_column_double()
             - sqlite3_column_int()
             - sqlite3_column_int64()
             - sqlite3_column_text()
             - sqlite3_column_text16()
             - sqlite3_column_type()
             - sqlite3_column_value()

 - [sqlite3_finalize()][46]	销毁由sqlite3_prepare()创建的预编译语句。为了防止内存泄露，每条预编译语句必须调用这个方法进行销毁。
 - [sqlite3_close()][47]	关闭由sqlite3_open()创建的数据库连接。在关闭连接之前，所有的预编译语句都应该被finalize。

##核心程序和接口的典型用法
应用若要使用SQLite通常的做法是在初始化的时候通过sqlite3_open()创建单个数据库连接。注意sqlite3_open()可以用来打开存在的数据库文件也能创建新的数据库文件。尽管很多的应用仅仅使用单个数据库连接时，但也没有任何理由不能多次调用 sqlite3_open()创建多个数据库连接，同一个数据库或者不同的数据库。多线程的应有时会为每个线程创建不同的数据库连接。还要注意，没有必要为了访问两个或者多个数据库而创建不同的数据库连接。单个数据库连接可以使用 ATTACH SQL命令同时访问两个或者多个数据库。

大多数应用的做法是在关闭时调用sqlite3_close()销毁数据库连接。或者例如一个应用使用SQLite作为文件格式，在文件/打开菜单操作时打开数据库连接，在文件/关闭菜单时销毁相应的数据库连接。

若要运行SQL语句，请遵循以下步骤：

 - 使用 [sqlite3_prepare()][48].创建预编译语句对象。
 - 调用[sqlite3_step()][49]一次或者多次执行预编译语句对象
 - 对于查询来说，调用[sqlite3_column()][50]提取执行[sqlite3_step()][51]得到的结果集
 - 使用 [sqlite3_finalize()][52]来销毁预编译语句

为了更加有效得使用SQLite，以上是需要了解的。剩下的就只有细枝末节。

##方便的包装器
 [sqlite3_exec()][53]接口是一个方便的包装器，调用一个方法便可执行上面的四个步骤。传递到[sqlite3_exec()][54]中的回调函数将用于处理每行结果集。 [sqlite3_get_table()][55]是另一个方便的包装器，同样可以用上述的四个步骤。与 [sqlite3_exec()][56]不同的是，[sqlite3_get_table()][57]将结果存储在堆存储器里而非调用回调函数。

无论是 [sqlite3_exec()][58]还是 [sqlite3_get_table()][59]做的任何事情都可以由核心程序来完成，认识到这一点很重要。事实上，这些包装器仅仅实现了核心程序。

##绑定参数与重用预编译语句
在之前的讨论中，假设了每条SQL语句只准备，执行了一次然后销毁。但是SQLite允许相同的预编译语句执行多次。这由以下的程序完成：

 - [sqlite3_reset()][60]
 - [sqlite3_bind()][61]

在[预编译语句][62]被一次或多次调用 [sqlite3_step()][63]执行之后，它可以通过调用 [sqlite3_reset()][64]重置以便于再次执行。对已存在的预编译语句对象使用 [sqlite3_reset()][65]而不是创建一个新的预编译语句，避免不必要得调用 [sqlite3_prepare()][66]。对于很多SQL语句来说，执行sqlite3_prepare()的时间等于或者超过执行sqlite3_step()的时间。所以避免调用sqlite3_prepare(）可以显著提升性能。

通常情况下，尽管多次执行相同的SQL语句并非有用。更多时候，执行的是相似的语句。比如你可能想要多次执行一条插入语句，但插入的值不同。为了使用这类的灵活性，SQLite允许将要执行的SQL语句包含之前绑定的参数。这些值可以在后面更改，这样相同的预编译语句可以使用新的值执行第二次。

在SQLite中，无论是否有效得包含字符串，都可以选取以下的形式作为参数：

 - ?
 - ?NNN
 - :AAA
 - $AAA
 - @AAA

在上面的例子中，NNN是整数值，AAA是标识符。参数初始化值为NULL。首先先调用[sqlite3_step()][67]或者在[sqlite3_reset()][68]立即调用。应用可以调用其中一个sqlite3_bind()接口附加到参数上。每次调用 [sqlite3_bind()][69]将覆盖先前的参数绑定。

应用程序可以提前准备多条SQL语句，并根据需要对其执行。对于准备的语句在数量上没有任何限制。


##配置SQLite
对于大多数应用来说SQLite的默认配置已经足够了。但是有时开发者想要调整设，以尽量提升一点性能，或者使用一些不起眼的特性。

[sqlite3_config()][70]接口用于创建SQLite全局的，进程级的配置更改。在任何数据库连接被创建前，sqlite3_config()接口必须被调用。 [sqlite3_config()][71]接口允许程序员做这样的事情:


 - 调整SQLite内存分配，包括为实时嵌入系统设置内存分配器和设置应用定义的[内存分配器][72]。
 - 设置进程级的[错误日志][73]
 - 指定应用定义的页面缓存
 - 调整互斥体使之适合于不同的[线程模型][74]，或者替换应用定义的互斥系统
进程级的配置完成之后[数据库连接][75]便会建立。个别的数据库连接可以调用 [sqlite3_limit()][76]和 [sqlite3_db_config()][77]进行配置。


##扩展SQLite

SQLite还包括了用来扩展功能的接口，这些程序包括：

 - [sqlite3_create_collation()][78]
 - [sqlite3_create_function()][79]
 - [sqlite3_create_module()][80]
 - [sqlite3_vfs_register()][81]

sqlite3_create_collation()接口用来为排序文本创建新的排序序列。[sqlite3_create_module()][82] 接口用来注册新的[虚拟表][83]实现。[sqlite3_vfs_register(][84])接口创建新的[VFSes][85]。

[sqlite3_create_function()][86]接口创建新的SQL功能 - 无论是标量还是聚合。新功能的实现通常使用了以下几个附加接口:

 - [sqlite3_aggregate_context()][87]
 - [sqlite3_result()][88]
 - [sqlite3_user_data()][89]
 - [sqlite3_value()][90]

SQLite所有内置的SQL函数正式使用这些接口来创建的。参考SQLite源码，特别是date.c和func.c源文件的例子。

共享的库或 Dll 可以用作 SQLite 的[可加载扩展][91]。
##其他接口
这篇文章仅仅提及了SQLite接口的基础功能。SQLite库还包含了很多其他有用的特性但是这里没有描述。你能在[C/C++接口规范][92]中找到SQLite完整的功能列表。请参阅关于SQLite所有接口的权威文档。


  [1]: https://www.sqlite.org/cintro.html
  [2]: https://www.sqlite.org/c3ref/sqlite3.html
  [3]: https://www.sqlite.org/c3ref/stmt.html
  [4]: https://www.sqlite.org/c3ref/prepare.html
  [5]: https://www.sqlite.org/c3ref/finalize.html
  [6]: https://www.sqlite.org/c3ref/open.html
  [7]: https://www.sqlite.org/c3ref/sqlite3.html
  [8]: https://www.sqlite.org/c3ref/prepare.html
  [9]: https://www.sqlite.org/c3ref/stmt.html
  [10]: https://www.sqlite.org/c3ref/bind_blob.html
  [11]: https://www.sqlite.org/lang_expr.html#varparam
  [12]: https://www.sqlite.org/c3ref/step.html
  [13]: https://www.sqlite.org/c3ref/stmt.html
  [14]: https://www.sqlite.org/c3ref/column_blob.html
  [15]: https://www.sqlite.org/c3ref/finalize.html
  [16]: https://www.sqlite.org/c3ref/stmt.html
  [17]: https://www.sqlite.org/c3ref/close.html
  [18]: https://www.sqlite.org/c3ref/sqlite3.html
  [19]: https://www.sqlite.org/c3ref/exec.html
  [20]: https://www.sqlite.org/c3ref/prepare.html
  [21]: https://www.sqlite.org/c3ref/step.html
  [22]: https://www.sqlite.org/c3ref/column_blob.html
  [23]: https://www.sqlite.org/c3ref/finalize.html
  [24]: https://www.sqlite.org/c3ref/intro.html
  [25]: https://www.sqlite.org/c3ref/sqlite3.html
  [26]: https://www.sqlite.org/c3ref/stmt.html
  [27]: https://www.sqlite.org/c3ref/stmt.html
  [28]: https://www.sqlite.org/c3ref/stmt.html
  [29]: https://www.sqlite.org/c3ref/sqlite3.html
  [30]: https://www.sqlite.org/c3ref/stmt.html
  [31]: https://www.sqlite.org/c3ref/open.html
  [32]: https://www.sqlite.org/c3ref/prepare.html
  [33]: https://www.sqlite.org/c3ref/step.html
  [34]: https://www.sqlite.org/c3ref/column_blob.html
  [35]: https://www.sqlite.org/c3ref/finalize.html
  [36]: https://www.sqlite.org/c3ref/close.html
  [37]: https://www.sqlite.org/c3ref/open.html
  [38]: https://www.sqlite.org/c3ref/open.html
  [39]: https://www.sqlite.org/c3ref/open.html
  [40]: https://www.sqlite.org/c3ref/open.html
  [41]: https://www.sqlite.org/c3ref/column_blob.html
  [42]: https://www.sqlite.org/c3ref/open.html
  [43]: https://www.sqlite.org/c3ref/prepare.html
  [44]: https://www.sqlite.org/c3ref/step.html
  [45]: https://www.sqlite.org/c3ref/column_blob.html
  [46]: https://www.sqlite.org/c3ref/finalize.html
  [47]: https://www.sqlite.org/c3ref/close.html
  [48]: https://www.sqlite.org/c3ref/prepare.html
  [49]: https://www.sqlite.org/c3ref/step.html
  [50]: https://www.sqlite.org/c3ref/column_blob.html
  [51]: https://www.sqlite.org/c3ref/step.html
  [52]: https://www.sqlite.org/c3ref/finalize.html
  [53]: https://www.sqlite.org/c3ref/exec.html
  [54]: https://www.sqlite.org/c3ref/exec.html
  [55]: https://www.sqlite.org/c3ref/free_table.html
  [56]: https://www.sqlite.org/c3ref/exec.html
  [57]: https://www.sqlite.org/c3ref/free_table.html
  [58]: https://www.sqlite.org/c3ref/exec.html
  [59]: https://www.sqlite.org/c3ref/free_table.html
  [60]: https://www.sqlite.org/c3ref/reset.html
  [61]: https://www.sqlite.org/c3ref/bind_blob.html
  [62]: https://www.sqlite.org/c3ref/stmt.html
  [63]: https://www.sqlite.org/c3ref/step.html
  [64]: https://www.sqlite.org/c3ref/reset.html
  [65]: https://www.sqlite.org/c3ref/reset.html
  [66]: https://www.sqlite.org/c3ref/prepare.html
  [67]: https://www.sqlite.org/c3ref/step.html
  [68]: https://www.sqlite.org/c3ref/reset.html
  [69]: https://www.sqlite.org/c3ref/bind_blob.html
  [70]: https://www.sqlite.org/c3ref/config.html
  [71]: https://www.sqlite.org/c3ref/config.html
  [72]: https://www.sqlite.org/malloc.html
  [73]: https://www.sqlite.org/errlog.html
  [74]: https://www.sqlite.org/threadsafe.html
  [75]: https://www.sqlite.org/c3ref/sqlite3.html
  [76]: https://www.sqlite.org/c3ref/limit.html
  [77]: https://www.sqlite.org/c3ref/db_config.html
  [78]: https://www.sqlite.org/c3ref/create_collation.html
  [79]: https://www.sqlite.org/c3ref/create_function.html
  [80]: https://www.sqlite.org/c3ref/create_module.html
  [81]: https://www.sqlite.org/c3ref/vfs_find.html
  [82]: https://www.sqlite.org/c3ref/create_module.html
  [83]: https://www.sqlite.org/vtab.html
  [84]: https://www.sqlite.org/c3ref/vfs_find.html
  [85]: https://www.sqlite.org/vfs.html
  [86]: https://www.sqlite.org/c3ref/create_function.html
  [87]: https://www.sqlite.org/c3ref/aggregate_context.html
  [88]: https://www.sqlite.org/c3ref/result_blob.html
  [89]: https://www.sqlite.org/c3ref/user_data.html
  [90]: https://www.sqlite.org/c3ref/value_blob.html
  [91]: https://www.sqlite.org/loadext.html
  [92]: https://www.sqlite.org/c3ref/intro.html