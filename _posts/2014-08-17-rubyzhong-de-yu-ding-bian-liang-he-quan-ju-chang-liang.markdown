---
layout: post
title: "ruby中的预定变量和全局常量"
date: 2014-08-17 22:31
comments: true
categories: Ruby
---

每次看别人的gem中间出现的全局常量，总得费不少力气查查什么意思，这里花点时间全部总结出来。

#### 预定义变量

    变量         类型        解释

    异常信息
    1. $!       异常        传递给raise的异常对象
    2. $@       数组        最有一个异常产生时调用的栈。一个字符数组指向产生异常的方法调用的地
                           方，格式："filename:line"或者 "filename:line:in `methodname'"
    模式匹配
    3. $&       String     成功匹配的字符。该变量为只读并且局部于当前作用域，当匹配失败之后将nil
    4. $+       String     成功匹配时最后一个括号匹配的字符。该变量局部于当前作用域，当匹配失败之
                           后将nil
    5. $`       String     匹配字符串之前的字符串。该变量局部于当前作用域
    6. $'       String     匹配字符串之后的字符串。该变量局部于当前作用域
    7. $=       Object     1.9之前忽略大小写标志。设置为nil和fasle之外的值时，将忽略大小写。默
                           认值是nil,ruby1.9不被支持
    8. $1..$9   String     匹配对应圆括号的字符。该变量为只读并且局部于当前作用域
    9. $~       MatchData  当前作用域最后一次匹配信息。$&,$+,$`,$1..$9等都是由$~派生而来，对$~
                           赋值会影响$&,$1到$9
    输入/输出变量
    10. $/      String     输入的分隔符(默认为\n)。如果设置为nil，则读取整个文件
    11. $\      String     附加的print和IO#write之后的字符。默认值是nil
    12. $,      String     像print, Array#join这样参数输出时的分隔符。默认为nil
    13. $;      String     String#split使用的默认分隔符，如果$;为nil, split将使用空格作为分隔
    14. $.      Fixnum     从当前文件中读入的最后一行的行号
    15. $<      Object     shell输入源，去读命令行给出的文件参数对象，没有参数时为$stdin
    16. $>      IO         Kernel#print,printf的输出目标，默认为$stdout. 为shell的output
    17. $_      String     Kernel#gets或者Kernel#readline读入最后一行。变量局部于当前作用域
    18. $defout IO         和$>同义。1.8使用，已经遗弃被$stdout代替
    19. $deferr IO         和STDER同义。1.8使用，已经遗弃，使用$stderr替代
    20. $stdin  IO         标准输入
    21. $stdout IO         标准输出
    22. $stderr IO         当前标准错误输出


    执行环境变量
    23. $0      String     被执行的ruby程序名字。通常是文件名。在一些系统中改变$0将会改变ps(1)
                           命令报告的进程名字
    24. $*      Array      执行ruby程序时参数数组，其中被ruby解释器使用的参数已经删除
    25. $"      Array      一个包含require加载的文件名和模块名的数组
    26. $$      Fixnum     被执行程序的进程号
    27. $?      Process:Status
                           最后一个子进程退出状态
    28. $:      Array      一个字符串数组，load和require方法将在每个字符串指定的目录中搜索ruby
                           脚步和二进制文件。初始值为命令行-I选项指定的的参数，然后加上当前路径
                           以及标准库的路径。程序中可以修改此变量以调整加载路径。
    29. $DEBUG    Object   如果命令行指定了-d选项，则为真
    30. __FILE__  String   当前源文件的名字
    31. __LINE__  String   源代码当前行的行号
    32. $F        Array    如果使用了-a命令行选项，则此数组表示分隔的输入行。
    33. $FILENAME String   当期输入文件名字。$<.filename。
    34. $LOAD_PATH Array   与$:同义。
    35. $SAFE     Fixnum   当前安全级别。不能用赋值语句来减小该变量的值。
    36. $verbose  Object   如果命令行给出了-v,--version,-W,-w,则为true。如果没有这些参数或者
                           有-w1，则为false。如果有-w0则为nil。此项设置为真使得解释器和库输出
                           更多额外的信息，设置为nil则禁止所有警告包括Kernel.warn的输出

    选项变量
    用来显示Ruby解释器命令行信息的变量。其形式为$-?，?的部分是1位选项字符。
    37. $-0      String    变量$/的别名
    38. $-a      Object    如果设置了-a则为真
    39. $-d      Object    $DEBUG的同义词
    40. $-F      String    和$;同义
    41. $-i      String    如果启动了in-place编辑模式（可能使用-i命令行选项），则$-i保存了用
                           保存备份文件的扩展。如果设置一个值给$-i，则将启动in-place编辑模式。
    42. $-I      Array     与$:，$LOAD_PATH同义
    43. $-K      String    $KCODE 的别名。等价于-K命令行选项。
    44. $-l      Object    命令行指定-l选项则为真。只读变量
    45. $-p      Object    若指定了-p时，其值为真。只读变量
    46. $-v      Object    $VERBOSE 的别名
    47. $-w      Object    $VERBOSE 的别名
    48. $-W      Fixnum    返回由-W[level]指定的值。
                             nil: 不输出警告 -> 0
                             false: 只输出重要警告(默认) -> 1
                             true: 输出所有警告 -> 2

    标准对象
    49. ARGF     Object    与$<同义
    50. ARGV     Array     与$*同义
    51. ENV      Object    一个类似Hash包含环境变量的对象
    52. false    FalseClass  类FalseClass的Singleton实例
    53. true     TrueClass   类TrueClass的Singleton实例
    54. nil      NilClass    类NilClass的Singleton实例, 表示为初始化的实例变量和全局变量的值
    55. self     Object      当前方法的接受者

#### 全局常量
    由ruby解释器定义
    56. DATA     IO  如果主程序中含有__END__指令，那么常量DATA将会被初始化为源代码中__END__之后的行
    57. FALSE    FalseClass 和false同义
    58. TRUE     TrueClass  和true同义
    59. NIL      NilClass   和nil同义
    60. STDIN    IO         程序实际的输入流。初始值为$stdin
    61. STDOUT   IO         程序实际的输出流。初始值为$stdout
    62. STDERR   IO         程序实际的错误流。初始值为$stderr
    63. RUBY_PLATFORM      String   运行程序的平台标识
    64. RUBY_RELEASE_DATE  String   版本发布日期
    65. RUBY_VERSION       String   解释器版本号
    66. SCRIPTS_LINES_     
    67. TOPLEVEL_BINDING   Binding  一个Binding对象表示ruby顶层上的绑定
    