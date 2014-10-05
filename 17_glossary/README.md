# 术语表(Glossary) #


## Spring Batch术语表 ##


**Batch** 

批处理, 需要一定耗时的大量业务处理


Batch Application Style
Term used to designate batch as an application style in its own right similar to online, Web or SOA. It has standard elements of input, validation, transformation of information to business model, business processing and output. In addition, it requires monitoring at a macro level.


批处理应用程序风格
术语用于指定批处理应用程序在其自己的风格 类似于网络,网络或SOA。 它有标准的元素 的信息输入、验证、转换经营模式, 业务处理和输出。 此外,它需要监测 宏观层面。


Batch Processing
The handling of a batch of many business transactions that have accumulated over a period of time (e.g. an hour, day, week, month, or year). It is the application of a process, or set of processes, to many data entities or objects in a repetitive and predictable fashion with either no manual element, or a separate manual element for error processing.


批处理
一批的处理许多业务事务 积累在一段时间内(如一个小时,天,周,月,或 年)。 它是应用程序的一个过程,或一组流程 许多数据实体或对象重复的和可预测的方式 与没有手工元素,或一个单独的手动元素错误 处理。


**Batch Window**

批处理窗口, 批处理作业必须要完成的一段时间范围。 这可能是受到其他在线系统的限制,或者是其他需要执行的相关作业的限制,也可能是因为特定于批处理环境的其他因素 。

Step
It is the main batch task or unit of work controller. It initializes the business logic, and controls the transaction environment based on commit interval setting, etc.


一步
它是主要的批处理任务或工作单元控制器。 它 初始化业务逻辑,并控制事务 环境基础上提交间隔设置,等等。


Tasklet
A component created by application developer to process the business logic for a Step.


微
application developer创建的组件来处理 业务逻辑步骤。


Batch Job Type
Job Types describe application of jobs for particular type of processing. Common areas are interface processing (typically flat files), forms processing (either for online pdf generation or print formats), report processing.


批处理作业类型
作业类型描述应用程序的特定类型的工作 处理。 公共区域接口处理(通常是平的 文件),形式处理(在线生成pdf或打印 格式)、报告处理。


Driving Query
A driving query identifies the set of work for a job to do; the job then breaks that work into individual units of work. For instance, identify all financial transactions that have a status of "pending transmission" and send them to our partner system. The driving query returns a set of record IDs to process; each record ID then becomes a unit of work. A driving query may involve a join (if the criteria for selection falls across two or more tables) or it may work with a single table.


开查询
驾驶查询标识组工作工作要做; 工作然后休息成单个的工作单元。 例如, 识别所有金融交易,有一个“等待的状态 传输”和寄给我们的合作伙伴系统。 开车的查询 返回一组记录ID处理;然后成为每个记录ID 工作单元。 驾驶查询可能涉及一个连接(如果的标准 选择落在两个或多个表)或者它可能工作 单表。


Item
An item represents the smallest ammount of complete data for processing. In the simplest terms, this might mean a line in a file, a row in a database table, or a particular element in an XML file.


项
一个项目代表最小的供方的完整的数据 处理。 在最简单的术语中,这可能意味着在一个文件,一行一个 在一个数据库表行,或者一个特定的XML元素 文件。


Logicial Unit of Work (LUW)
A batch job iterates through a driving query (or another input source such as a file) to perform the set of work that the job must accomplish. Each iteration of work performed is a unit of work.


Logicial工作单元(LUW)
批处理作业(或另一个输入迭代驾驶查询 源文件等),工作必须执行的工作 完成的目标。 每次迭代的工作表现是一个工作单元。


Commit Interval
A set of LUWs processed within a single transaction.


提交时间间隔
一组的luw中在一个事务中处理。


Partitioning
Splitting a job into multiple threads where each thread is responsible for a subset of the overall data to be processed. The threads of execution may be within the same JVM or they may span JVMs in a clustered environment that supports workload balancing.


分区
把一份工作分解为多个线程,每个线程 负责处理整个数据的一个子集。 的 执行的线程可能在相同的JVM或他们可能跨越JVM 在集群环境中,支持工作负载平衡。


Staging Table
A table that holds temporary data while it is being processed.


分段表
存储临时数据,因为它在一个表 处理。

Restartable
A job that can be executed again and will assume the same identity as when run initially. In othewords, it is has the same job instance id.


可重新开始的
一份工作,可以再将承担相同的执行 身份运行时。 在othewords,具有相同的工作 实例id。


Rerunnable
A job that is restartable and manages its own state in terms of previous run's record processing. An example of a rerunnable step is one based on a driving query. If the driving query can be formed so that it will limit the processed rows when the job is restarted than it is re-runnable. This is managed by the application logic. Often times a condition is added to the where statement to limit the rows returned by the driving query with something like "and processedFlag != true".


Rerunnable
一个可重新起动的工作和管理自己的国家 以前运行的记录处理。 的一个例子是rerunnable一步 基于驾驶查询。 如果驾驶查询就可以形成 它将限制加工行工作时重新启动 这是re-runnable。 这是由应用程序逻辑。 经常 次条件添加到where子句来限制行 返回的查询与“processedFlag开车 ! = true”。


Repeat
One of the most basic units of batch processing, that defines repeatability calling a portion of code until it is finished, and while there is no error. Typically a batch process would be repeatable as long as there is input.


重复
批处理的最基本单位之一,定义 可重复性调用代码的一部分,直到完成为止,和 虽然没有错误。 通常是一个批处理将是可重复的 只要有输入。


Retry
Simplifies the execution of operations with retry semantics most frequently associated with handling transactional output exceptions. Retry is slightly different from repeat, rather than continually calling a block of code, retry is stateful, and continually calls the same block of code with the same input, until it either succeeds, or some type of retry limit has been exceeded. It is only generally useful if a subsequent invocation of the operation might succeed because something in the environment has improved.


重试
重试语义最简化的执行操作 经常与处理事务输出异常有关。 从重复重试略有不同,而不是不断 调用的代码块,重试状态,不断地调用 与相同的输入相同的代码块,直到成功,或 某种类型的重试限制已经超过了。 这只是一般 有用的后续操作的调用是否成功 因为一些环境得到了改善。

Recover
Recover operations handle an exception in such a way that a repeat process is able to continue.

恢复
恢复操作处理异常的方式 重复过程能够持续下去。

Skip
Skip is a recovery strategy often used on file input sources as the strategy for ignoring bad input records that failed validation.

跳过
跳过是经常被用到的一个恢复策略文件输入源 失败的战略忽视坏输入记录 验证。


