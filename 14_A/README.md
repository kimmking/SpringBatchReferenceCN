# 附录A ItemReader 与 ItemWriter 列表

## A.1 Item Readers

>> **Table A.1. 所有可用的Item Reader列表**


<table>
	<colgroup>
		<col align="center">
		<col>
	</colgroup>
	<thead>
		<tr>
			<th style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="center">Item Reader</th><th style="border-bottom: 0.5pt solid ; " align="center">说明</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">AbstractItemCountingItemStreamItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">抽象基类，支持重启，通过统计(counting)从 <code class="classname">ItemReader</code> 返回对象的数量来实现.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">AggregateItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left"> 此 ItemReader 提供一个 list , 用来存储 ItemReader 读取的对象, 直到他们已准备装配为一个集合。<br/> 此 ItemReader 通过 FieldSetMapper 的常量值 AggregateItemReader#<span class="bold"><strong>BEGIN_RECORD</strong></span> 以及 AggregateItemReader#<span class="bold"><strong>END_RECORD</strong></span> 来标记记录的开始与结束。 </td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">AmqpItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">给定一个提供同步获取方法(
			synchronous receive methods)的 Spring AmqpTemplate. 使用 receiveAndConvert() 方法可以得到 POJO 对象. </td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">FlatFileItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">从平面文件(flat file)中读取数据，支持 ItemStream
			以及 Skippable 特性. 请参考 Read from a
			File 一节</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">HibernateCursorItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">从基于 HQL 查询的 cursor 中读取数据。 请参考  Reading from a Database 一节。</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">HibernatePagingItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">从分页(paginated)的HQL查询中读取数据</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">IbatisPagingItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">通过 iBATIS 的分页查询读取数据，对于大型数据集,分页能避免内存不足/溢出的问题. 请参考: HOWTO - Read from a Database. 这个 ItemReader 在 Spring Batch 3.0 中已废弃.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">ItemReaderAdapter</td><td style="border-bottom: 0.5pt solid ; " align="left">将任意类适配到 <code class="classname">
				ItemReader</code> 接口.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JdbcCursorItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">通过 JDBC 从 一个 database cursor 中读取数据. 请参考: HOWTO - Read from a Database</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JdbcPagingItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left"> 给定一个 SQL statement, 通过分页查询读取数据，避免读取大型数据集时 内存不足/溢出的问题</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JmsItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left"> 给一个 Spring JmsOperations 对象和一个 JMS
			Destination 对象/也可以是用来发送错误的 destination name , 调用注入的 JmsOperations 里面的 receive()
			方法来获取对象</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JpaPagingItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left"> 给定一个 JPQL statement, 通过分页查询读取数据，避免读取大型数据集时 内存不足/溢出的问题</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">ListItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left"> 从 list 中读取数据，一次返回一条</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">MongoItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">给定一个 MongoOperations 对象,以及从 MongoDB
			中查询数据所使用的JSON, 通过 MongoOperations 的 find 方法来获取数据</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">Neo4jItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left"> 给定一个 Neo4jOperations 对象,以及一个
			Cyhper query 所需的 components, 将 Neo4jOperations.query 方法的结果返回 </td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">RepositoryItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left"> 给定一个 Spring Data PagingAndSortingRepository 对象, 一个 Sort 对象,以及要执行的 method name, 返回 Spring Data repository 实现提供的数据</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">StoredProcedureItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left"> 执行存储过程(database stored procedure),从返回的 database cursor 中读取数据. 请参考: HOWTO - Read from a
			Database</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; " align="left">StaxEventItemReader</td><td style="" align="left">通过 StAX 读取. 请参考 HOWTO - Read from a
			File</td>
		</tr>
	</tbody>
</table>



## A.2 Item Writers

>> **Table A.2. 所有可用的 Item Writer 列表**


<table>
	<colgroup>
		<col align="center">
		<col>
	</colgroup>
	<thead>
		<tr>
			<th style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="center">Item Writer</th><th style="border-bottom: 0.5pt solid ; " align="center">说明</th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">AbstractItemStreamItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">抽象基类, 组合了 <code class="classname">
				ItemStream</code> 和 <code class="classname">
				ItemWriter</code> 接口。</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">AmqpItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">给定一个提供同步发送方法的 Spring AmqpTemplate. 使用convertAndSend(Object)
			方法可以输出 POJO 对象. </td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">CompositeItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">将注入的 <span class="bold"><strong>List</strong></span> 里面每一个元素都传给 <span class="bold"><strong>ItemWriter</strong></span> 的处理方法</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">FlatFileItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left"> 写入平面文件(flat file). 支持 ItemStream 以及 Skippable 特性. 请参考 Writing to a File 一节</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">GemfireItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left"> 使用 GemfireOperations 对象, 根据配置的 delete 标志，对 items 进行写入或者删除</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">HibernateItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left"> 这个 item writer 是Hibernate会话相关的(hibernate session aware), 用来处理非 hibernate 相关的组件(non-"hibernate aware" )不需要关心的事务性工作, 并且委托另一个 item writer 来执行实际的写入工作.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">IbatisBatchItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left"> 在批处理中直接使用 iBatis 的 API. 这个 ItemWriter 在 Spring Batch 3.0 中已废弃.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">ItemWriterAdapter</td><td style="border-bottom: 0.5pt solid ; " align="left">将任意类适配到  <code class="classname">
				ItemWriter</code> 接口.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JdbcBatchItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">尽可能地利用 <code class="classname">
				PreparedStatement</code> 的批处理功能(batching features), 还可以采取基本的步骤来定位 <code class="methodname">
				flush</code> 失败等问题。 </td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JmsItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">利用 JmsOperations 对象,  通过 JmsOperations.convertAndSend() 方法将 items 写入到默认队列( default queue)</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JpaItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">这个 item writer 是 JPA EntityManager aware 的, 用来处理非jpa相关的(non-"jpa aware") <code class="classname">
				ItemWriter</code> 不需要关心的事务性工作, 并且委托另一个 item writer 来执行实际的写入工作.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">MimeMessageItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left"> 通过 Spring 的 JavaMailSender 对象, 类型为 <code class="classname">
				MimeMessage</code> 的 item 可以作为 mail messages 发送出去</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">MongoItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left"> 给定一个 MongoOperations 对象, 数据通过 MongoOperations.save(Object) 方法写入.  实际的写操作会推迟到事务提交时才执行.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">Neo4jItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left"> 给定一个 Neo4jOperations 对象, item 通过
			save(Object) 方法完成持久化,或者通过 delete(Object) 方法来删除, 取决于 <code class="classname">
				ItemWriter</code> 的配置</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">PropertyExtractingDelegatingItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left"> 扩展 AbstractMethodInvokingDelegator 创建动态参数. 动态参数是通过注入的 field name数组,从(SpringBeanWrapper)处理的 item 中获取的</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">RepositoryItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">给定一个 Spring Data CrudRepository 实现, 则使用配置文件指定的方法保存 item。</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; " align="left">StaxEventItemWriter</td><td style="" align="left">通过<span class="bold"><strong>ObjectToXmlSerializer</strong></span> 对象将每个 item 转换为XML, 然后用 StAX 将这些内容写入 XML 文件。</td>
		</tr>
	</tbody>
</table>









