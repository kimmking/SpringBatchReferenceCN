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


##等待整理

### Batch Job Type
Job Types describe application of jobs for particular type of processing. Common areas are interface processing (typically flat files), forms processing (either for online pdf generation or print formats), report processing.


### 批处理作业类型
作业类型描述应用程序的特定类型的工作 处理。 公共区域接口处理(通常是平的 文件),形式处理(在线生成pdf或打印 格式)、报告处理。


### Driving Query
A driving query identifies the set of work for a job to do; the job then breaks that work into individual units of work. For instance, identify all financial transactions that have a status of "pending transmission" and send them to our partner system. The driving query returns a set of record IDs to process; each record ID then becomes a unit of work. A driving query may involve a join (if the criteria for selection falls across two or more tables) or it may work with a single table.


### 开查询
驾驶查询标识组工作工作要做; 工作然后休息成单个的工作单元。 例如, 识别所有金融交易,有一个“等待的状态 传输”和寄给我们的合作伙伴系统。 开车的查询 返回一组记录ID处理;然后成为每个记录ID 工作单元。 驾驶查询可能涉及一个连接(如果的标准 选择落在两个或多个表)或者它可能工作 单表。


### Item
An item represents the smallest ammount of complete data for processing. In the simplest terms, this might mean a line in a file, a row in a database table, or a particular element in an XML file.


### 项
一个项目代表最小的供方的完整的数据 处理。 在最简单的术语中,这可能意味着在一个文件,一行一个 在一个数据库表行,或者一个特定的XML元素 文件。


### Logicial Unit of Work (LUW)
A batch job iterates through a driving query (or another input source such as a file) to perform the set of work that the job must accomplish. Each iteration of work performed is a unit of work.


### Logicial工作单元(LUW)
批处理作业(或另一个输入迭代驾驶查询 源文件等),工作必须执行的工作 完成的目标。 每次迭代的工作表现是一个工作单元。


### Commit Interval
A set of LUWs processed within a single transaction.


### 提交时间间隔
一组的luw中在一个事务中处理。


### Partitioning
Splitting a job into multiple threads where each thread is responsible for a subset of the overall data to be processed. The threads of execution may be within the same JVM or they may span JVMs in a clustered environment that supports workload balancing.


### 分区
把一份工作分解为多个线程,每个线程 负责处理整个数据的一个子集。 的 执行的线程可能在相同的JVM或他们可能跨越JVM 在集群环境中,支持工作负载平衡。


### Staging Table (分段表,阶段表)

A table that holds temporary data while it is being processed.

一个存储临时数据的表, 里面的数据即将被处理。


### Restartable (可重新启动的)

A job that can be executed again and will assume the same identity as when run initially. In otherwords, it is has the same job instance id.

可再次执行的作业, 而且再次执行时, 和初次运行具有同样的身份。换言之, 两者具有相同的作业实例 id.


### Rerunnable
A job that is restartable and manages its own state in terms of previous run's record processing. An example of a rerunnable step is one based on a driving query. If the driving query can be formed so that it will limit the processed rows when the job is restarted than it is re-runnable. This is managed by the application logic. Often times a condition is added to the where statement to limit the rows returned by the driving query with something like "and processedFlag != true".


### Rerunnable
一个可重新起动的工作和管理自己的国家 以前运行的记录处理。 的一个例子是rerunnable一步 基于驾驶查询。 如果驾驶查询就可以形成 它将限制加工行工作时重新启动 这是re-runnable。 这是由应用程序逻辑。 经常 次条件添加到where子句来限制行 返回的查询与“processedFlag开车 ! = true”。


### Repeat
One of the most basic units of batch processing, that defines repeatability calling a portion of code until it is finished, and while there is no error. Typically a batch process would be repeatable as long as there is input.


### 重复
批处理的最基本单位之一,定义 可重复性调用代码的一部分,直到完成为止,和 虽然没有错误。 通常是一个批处理将是可重复的 只要有输入。


### Retry (重试)
Simplifies the execution of operations with retry semantics most frequently associated with handling transactional output exceptions. Retry is slightly different from repeat, rather than continually calling a block of code, retry is stateful, and continually calls the same block of code with the same input, until it either succeeds, or some type of retry limit has been exceeded. It is only generally useful if a subsequent invocation of the operation might succeed because something in the environment has improved.


### 
重试语义最简化的执行操作 经常与处理事务输出异常有关。 从重复重试略有不同,而不是不断 调用的代码块,重试状态,不断地调用 与相同的输入相同的代码块,直到成功,或 某种类型的重试限制已经超过了。 这只是一般 有用的后续操作的调用是否成功 因为一些环境得到了改善。



### Recover (恢复)
Recover operations handle an exception in such a way that a repeat process is able to continue.

恢复操作用来对付异常, 通过这种方式使重复过程得以继续下去。


### Skip (跳过)

Skip is a recovery strategy often used on file input sources as the strategy for ignoring bad input records that failed validation.

跳过是一种容错策略, 通常在读取文件输入时，用来忽略验证失败的脏数据。





