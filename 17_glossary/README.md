# 术语表(Glossary)


## Spring Batch术语表


### Batch 批

An accumulation of business transactions over time.

随着时间累积而形成的一批业务事务。


### Batch Application Style (批处理程序风格)

Term used to designate batch as an application style in its own right similar to online, Web or SOA. It has standard elements of input, validation, transformation of information to business model, business processing and output. In addition, it requires monitoring at a macro level.

用来称呼批处理自身的程序风格的术语，类似于 online, Web 或者 SOA。 其具有的标准元素包括： 输入、验证、将信息转换为业务模型、业务处理 以及 输出。此外,还需要在宏观层面上进行监控。


### Batch Processing (批处理任务)

The handling of a batch of many business transactions that have accumulated over a period of time (e.g. an hour, day, week, month, or year). It is the application of a process, or set of processes, to many data entities or objects in a repetitive and predictable fashion with either no manual element, or a separate manual element for error processing.

积累了一定时间周期(比如小时、天、周、月或年)的业务事务归到一批进行处理。这种程序可以有一个进程,或者一组进程; 以重复可预测的方式处理很多数据实体/对象,要么没有人工干预，或者有些错误需要人工单独进行处理。


### Batch Window (批处理窗口)

The time frame within which a batch job must complete. This can be constrained by other systems coming online, other dependent jobs needing to execute or other factors specific to the batch environment.

批处理作业必须在这个时间范围内完成。 可能受到的制约包括: 其他系统要上线, 相关作业要执行, 或者是特定于批处理环境的其他因素 。


### Step (步骤)

It is the main batch task or unit of work controller. It initializes the business logic, and controls the transaction environment based on commit interval setting, etc.

这是主要的批处理任务, 或者工作控制器的组成单元。在其中进行业务逻辑初始化, 基于提交间隔控制事务环境,等等。


### Tasklet (小任务)
A component created by application developer to process the business logic for a Step.

由程序员创建的组件, 用来处理某个 step 中的业务逻辑。


### Batch Job Type (批处理作业类型)

Job Types describe application of jobs for particular type of processing. Common areas are interface processing (typically flat files), forms processing (either for online pdf generation or print formats), report processing.

作业类型描述特定类型的作业处理程序。共同领域包括 接口处理(通常是平面文件), 格式处理(如在线生成pdf或打印格式), 报表处理等。


### Driving Query (驱动查询)

A driving query identifies the set of work for a job to do; the job then breaks that work into individual units of work. For instance, identify all financial transactions that have a status of "pending transmission" and send them to our partner system. The driving query returns a set of record IDs to process; each record ID then becomes a unit of work. A driving query may involve a join (if the criteria for selection falls across two or more tables) or it may work with a single table.

一次驱动查询用来标识一个作业要做的工作组; 然后工作被打散为单个的工作单元。例如, 找出所有状态为“等待传输”的金融交易 并发送给合作伙伴系统。驱动查询返回要处理的记录的ID集合; 每个记录ID 稍后都会成为一个工作单元。 一次驱动查询可能涉及 join连接(如果条件遇到两个或多个表), 也可能只使用单个表。


### Item (数据项)

An item represents the smallest ammount of complete data for processing. In the simplest terms, this might mean a line in a file, a row in a database table, or a particular element in an XML file.

一个 item 代表要处理的最小的完整的数据。 最简单的理解, 可以是文件中的一行(line), 数据表中的一行(row), 或者XML文件中一个特定的元素(element)。


### Logical Unit of Work (LUW, 逻辑工作单元)

> 原文可能错了, `Logicial`

A batch job iterates through a driving query (or another input source such as a file) to perform the set of work that the job must accomplish. Each iteration of work performed is a unit of work.

批处理作业通过驱动查询(或者是文件之类的输入源)来迭代执行必须完成的工作。工作执行中的每次迭代就是一个工作单元。


### Commit Interval (提交区间)

A set of LUWs processed within a single transaction.

在单个事务中处理的 逻辑工作单元集合.


### Partitioning (分块, 分区)

Splitting a job into multiple threads where each thread is responsible for a subset of the overall data to be processed. The threads of execution may be within the same JVM or they may span JVMs in a clustered environment that supports workload balancing.

将一个作业拆分给多个线程来执行, 每个线程只负责处理整个数据中的一部分。这些线程可能在同一个JVM中执行, 也可能跨越JVM在支持工作负载平衡的集群环境中运行。


### Staging Table (分段表,阶段表)

A table that holds temporary data while it is being processed.

一个存储临时数据的表, 里面的数据即将被处理。


### Restartable (可再次启动的)

A job that can be executed again and will assume the same identity as when run initially. In otherwords, it is has the same job instance id.

可再次执行的作业, 而且再次执行时, 和初次运行具有同样的身份。换言之, 两者具有相同的作业实例 id.


### Rerunnable (可再次运行)

A job that is restartable and manages its own state in terms of previous run's record processing. An example of a rerunnable step is one based on a driving query. If the driving query can be formed so that it will limit the processed rows when the job is restarted than it is re-runnable. This is managed by the application logic. Often times a condition is added to the where statement to limit the rows returned by the driving query with something like "and processedFlag != true".

可再次启动的作业, 并且可根据之前的处理记录来合理调整自身的状态。可再次运行 Step 的一个例子是基于 driving query 的部分。如果是 re-runnable 的, 那么当 restarted 后, driving query 就会排除已经处理过的那些行。当然这由应用程序逻辑决定。通常是在 where 子句中添加条件来限制查询返回的结果, 例如 “processedFlag != true”。


### Repeat (重复)

One of the most basic units of batch processing, that defines repeatability calling a portion of code until it is finished, and while there is no error. Typically a batch process would be repeatable as long as there is input.

批处理最基本的单元之一, 定义了可重复调用的一部分代码, 直到完成某个任务为止, 如果不出错的话。通常来说, 只要还有输入数据, 批处理过程就会一直重复。


### Retry (重试)

Simplifies the execution of operations with retry semantics most frequently associated with handling transactional output exceptions. Retry is slightly different from repeat, rather than continually calling a block of code, retry is stateful, and continually calls the same block of code with the same input, until it either succeeds, or some type of retry limit has been exceeded. It is only generally useful if a subsequent invocation of the operation might succeed because something in the environment has improved.

简化了的重试语义通常和事务输出异常处理有关。重试(Retry)和重复(Repeat)略有不同, 不仅仅是持续不断地调用某个代码块, 因为重试是有状态的, 所以每次都是使用相同的输入, 直到成功为止, 或者是已经超过了某种类型的重试限制。一般只有在依赖某种外部环境的情况下, 如果外部环境得到改善, 就会使得后续操作会成功的情况下就会很有用。


### Recover (恢复)

Recover operations handle an exception in such a way that a repeat process is able to continue.

恢复操作用来对付异常, 通过这种方式使重复过程得以继续下去。


### Skip (跳过)

Skip is a recovery strategy often used on file input sources as the strategy for ignoring bad input records that failed validation.

跳过是一种容错策略, 通常在读取文件输入时，用来忽略验证失败的脏数据。

