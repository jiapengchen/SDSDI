SQLite定义了许多APIs。这些API函数在应用程序与SQLite库之间拥有单一的含义。前面一节我已经简要介绍了几个API函数。本节我将介绍一些最常使用的函数。这些函数的详细介绍可以在[http://www.sqlite.org/capi3ref.html中找到。](http://www.sqlite.org/capi3ref.html中找到。)

1.sqlite3open:

这个函数有两个参数，一个输入一个输出。输入数据库文件名，执行这个函数后，应用程序打开了一个新的连接，这个连接用来访问指定的数据文件。本书我将称它为库连接。（应用程序可以同时打开其它相同或者不同的数据库文件连接）。如果所指定的文件不存在，这个函数将自动创建这个数据库文件。默认的文件权限为0644。如果数据库能够打开成功，这个函数将返回SQLITE\_OK。否则，返回一个错误码。

```
2.3 延迟数据文件初始化
当创建一个数据库文件时，SQLITE允许使用一种称为延迟初始化的方式来打开数据文件。
这种延迟初始化意思是，直到这个文件真正被访问的时候才初始化。
这种策略允许你在真正打开数据文件之前能够通过pragma命令定义一些变量值。（在第三章介绍）
```

这个函数通过参数返回一个连接句柄。这个句柄将用于后续的操作。这个句柄维护着库连接的完整状态。

例子2.3的程序打开了两个库连接来访问一个相同的数据文件。这两个连接互不干扰，每个连接由不同的sqlite3对象表示。一个sqlite3对象代表并维护着一个数据库连接。例如在2.3中一个对象有三个prepared statements，而另外那个一个都没有。

1. sqlite3\_prepare:

这个函数用来编译SQL表达式，并产生一个内部对象sqlite3\_stmt。这个对象一般表示一个prepared statement，它通过bytecode实现，并由数据库引擎执行。在7.2节中我将讨论这些bytecode程序语言。这个函数如果成功返回SQLITE\_OK，否则返回相应错误码。

该函数通过参数返回statement的句柄（一个指向sqlite3stmt类型的指针）。这个句柄用于后续执行该statement的操作。在前面的例子里，我为select SID from Students order by SID这个语句创建了statement句柄。这个句柄就是一个打开的游标，用于获取select语句的结果集。当执行sqlite3\_step函数时，这个游标将前进指向下个结果。

1. sqlite3\_step:

当一个SQL语句已经通过sqlite2prepare初始化好后，可以用sqlite3sep执行一次或多次。每一次调用将执行内部bytecode程序，直到遇到一次break point，或者已经没有其它结果行。这个函数执行后如果能够获取行结果将返回SQLITEROW，如果没有结果行了则返回SQLITEDONE。在第一种情况下，你可以通过sqlite3_column\*读取每一列的结果。如果想继续读取下一行的结果，你可以继续执行sqlite3step函数。这个函数移动游标到下一个行结果。在一开始，这个游标指向第一个行结果的前面。后续每一次执行step函数，则会将游标指向下一个结果。这个游标只能朝一个方向移动。对于那些不产生查询级的语句例如，UPDATE, INSERT, DELETE, CREATE,和DROP，step函数将返回SQLITEDONE。因为这些函数不产生任何查询结果，所以返回的是SQLITE\_DONE。此后，这个statement句柄不能再用step函数继续执行，除非调用sqlite3_\_reset函数让它重置初始状态。\_

在每次执行后，可能会返回一些错误码，例如SQLITE_BUSY, SQLITE\_ERROR, 或者SQLITE\_MISUSE。SQLITE\_BUSY的意思是引擎正在访问一个正忙的数据库，同时没有注册回调函数来处理这种繁忙的状况，或者注册了回调函数但是该函数决定终止这次操作。遇到这个错误码时，你需要做的是重新执行一遍step函数。SQLITE\_ERROR的意思是遇到一次运行时错误，例如遇到约束冲突。在这种情况下就不要再次执行step函数了。SQLITE\_MISUSE的意思是，step函数没有被正确的调用，可能step函数用于一个已经终止的statement句柄。_

1. sqlite3\__column\_\*\_

如果sqlite_step函数返回SQLITE\_ROW。你可以通过sqlite3\_column\*函数来获取当前行的列值。sqlite和c语言将自动转换调用函数与类型不匹配的值。例如，如果内部存储的是FLOAT类型，但应用程序请求的是text类型，那么sqlite将使用spritf\(\)方法内部对这个值进行转换。_

一共有5个column函数：sqlite3_column\_int, sqlite3\_column\_int64, sqlite3\_column\_double, sqlite3\_column\_text, 与sqlite3\_column\_blob。这些函数的最后一个单词表示你喜欢获取的值类型。在上述的例子中，行结果的值为int类型，我们读取SID列的值时用了sqlite3\_column\_int这个方法让它返回integer类型。（最左边那一列的下标是0，下一个为1，并以此类推，你可以用sqlite3\_column\_count函数来获取总列数，对于没有非select语句，这个函数将返回0）。Blob与text类型值需要应用程序直到他们的大小。SQLite可以通过sqlite3\_column\_bytes函数来获取列值得字节数。_

1. sqlite3\_finalize

这个函数用于关闭和销毁statement句柄和关联的statement。它清除bytecode程式和清理所有statement句柄分配的资源。调用这个函数后statement句柄变为无效，不能再次使用。

如果statement执行成功或者完全没有执行过，finalize函数都返回SQLITE_OK。如果statement执行失败，那么这个函数将返回一个错误码。finalize函数可以在statement的执行过程中调用。如果引擎还没有执行完statement的时候调用了这个函数，就像遇到一个错误或者一次中断。不完整的更新将会回滚后终止，并返回SQLITE\_ABORT。_

1. sqlite3\_close

这个函数关闭一个库连接，并清理这个连接的所有资源，这个连接句柄将变得无效且不能再用。这个函数执行成功时返回SQLITE_OK，否则返回相应的错误码。如果有已经初始化的statement还没有关闭，那么它将返回SQLITE\_BUSY。_

1. 其它有用的函数

下面将介绍6类API函数。

在一个SQL语句字符创里，你可以使用一些站位符？。这些占位符在一个初始化的statement将变成输入参数。对于没有命名的参数，下标从1开始。对于已命名的参数，下标可以通过sqlite3\_bind\_parameter\_index API获取。这些参数对应的值可以通过bind函数指定。如果没有绑定值，这个参数将默认使用SQL NULL。

绑定函数有：

sqlite3\_bind\_null， sqlite3\_bind\_int, sqlite3\_bind\_int64, sqlite3\_bind\_double, sqlite3bind\_text, sqlite3\_bind\_text16, sqlite3\_bind\_text64, sqlite3\_bind\_blob, sqlite3\_bindblob64,sqlite3\_bind\_zeroblob,与sqlite3\_bind\_value。这些函数的最后一个单词表示的是输入参数的类型（sqlite3\_bind\_value函数用于帮助绑定一个通用值）。

reset函数将重置一个statement句柄到初始状态，但所有已绑定的参数还保留着。这个statement可以再次运行，再次执行时这些参数将被重用。然而，如果你想用于执行新的动作，那么你需要重新绑定这些参数。你可以通过执行sqlite3\_clear\_bindings这个api来移除所有绑定值。

8. 返回值

所有API函数返回0或正整数。SQLite开发团队强烈建议使用代名词而不要使用硬编码的整数值。SQLITE_OK返回值意味着执行成功。SQLITE_ROW意味着sqlite3_step函数找到一行数据。SQLITE_DONE返回值意味着statement已经执行完毕。因为这些返回值是SQLite接口的一部分，所以这些值在minor版本更新时不会有任何变化。

小结：应用程序初始化号SQL statement，并对它绑定好需要的值，然后对它执行一次或者多次step函数，执行完后使用reset函数重置这个statement，然后在用相同的绑定值或者不同的绑定值重新执行这个statement。当认为这个statemnt已经完成任务后，调用finalize函数销毁掉。

