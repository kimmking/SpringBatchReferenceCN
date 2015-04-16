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
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">AggregateItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">An ItemReader that delivers a list as its
			item, storing up objects from the injected ItemReader until they
			are ready to be packed out as a collection. This ItemReader should
			mark the beginning and end of records with the constant values in
			FieldSetMapper AggregateItemReader#<span class="bold"><strong>BEGIN_RECORD</strong></span> and
			AggregateItemReader#<span class="bold"><strong>END_RECORD</strong></span></td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">AmqpItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">Given a Spring AmqpTemplate it provides
			synchronous receive methods. The receiveAndConvert() method
			lets you receive POJO objects. </td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">FlatFileItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">Reads from a flat file. Includes ItemStream
			and Skippable functionality. See section on Read from a
			File</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">HibernateCursorItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">Reads from a cursor based on an HQL query. See
			section on Reading from a Database</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">HibernatePagingItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">从分页(paginated)的HQL查询中读取数据</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">IbatisPagingItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">Reads via iBATIS based on a query. Pages
			through the rows so that large datasets can be read without
			running out of memory. See HOWTO - Read from a Database. This
			ItemReader is now deprecated as of Spring Batch 3.0.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">ItemReaderAdapter</td><td style="border-bottom: 0.5pt solid ; " align="left">Adapts any class to the <code class="classname">
				ItemReader</code> interface.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JdbcCursorItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">Reads from a database cursor via JDBC. See
			HOWTO - Read from a Database</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JdbcPagingItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">Given a SQL statement, pages through the rows,
			such that large datasets can be read without running out of
			memory</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JmsItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">Given a Spring JmsOperations object and a JMS
			Destination or destination name to send errors, provides items
			received through the injected JmsOperations receive()
			method</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JpaPagingItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">Given a JPQL statement, pages through the
			rows, such that large datasets can be read without running out of
			memory</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">ListItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">Provides the items from a list, one at a
			time</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">MongoItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">Given a MongoOperations object and JSON based MongoDB
			query, proides items received from the MongoOperations find method</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">Neo4jItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">Given a Neo4jOperations object and the components of a
			Cyhper query, items are returned as the result of the Neo4jOperations.query
			method</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">RepositoryItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">Given a Spring Data PagingAndSortingRepository object,
			a Sort and the name of method to execute, returns items provided by the
			Spring Data repository implementation</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">StoredProcedureItemReader</td><td style="border-bottom: 0.5pt solid ; " align="left">Reads from a database cursor resulting from the
			execution of a database stored procedure. See HOWTO - Read from a
			Database</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; " align="left">StaxEventItemReader</td><td style="" align="left">通过 StAX 读取. 请参考 <b>HOWTO - Read from a
			File</b></td>
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
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">AmqpItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">Given a Spring AmqpTemplate it provides
			for synchronous send method. The convertAndSend(Object)
			method lets you send POJO objects. </td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">CompositeItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">Passes an item to the process method of each
			in an injected <span class="bold"><strong>List</strong></span> of <span class="bold"><strong>ItemWriter</strong></span> objects</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">FlatFileItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">Writes to a flat file. Includes ItemStream and
			Skippable functionality. See section on Writing to a File</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">GemfireItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">Using a GemfireOperations object, items wre either written
			or removed from the Gemfire instance based on the configuration of the delete
			flag</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">HibernateItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">This item writer is hibernate session aware
			and handles some transaction-related work that a non-"hibernate
			aware" item writer would not need to know about and then delegates
			to another item writer to do the actual writing.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">IbatisBatchItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">Writes items in a batch using the iBatis API's
			directly. This ItemWriter is deprecated as of Spring Batch 3.0.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">ItemWriterAdapter</td><td style="border-bottom: 0.5pt solid ; " align="left">Adapts any class to the <code class="classname">
				ItemWriter</code> interface.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JdbcBatchItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">Uses batching features from a <code class="classname">
				PreparedStatement</code>, if available, and can
			take rudimentary steps to locate a failure during a <code class="methodname">
				flush</code>.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JmsItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">Using a JmsOperations object, items are written
			to the default queue via the JmsOperations.convertAndSend() method</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">JpaItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">This item writer is JPA EntityManager aware
			and handles some transaction-related work that a non-"jpa aware" <code class="classname">
				ItemWriter</code> would not need to know about and
			then delegates to another writer to do the actual writing.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">MimeMessageItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">Using Spring's JavaMailSender, items of type <code class="classname">
				MimeMessage</code> are sent as mail messages</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">MongoItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">Given a MongoOperations object, items are written
			via the MongoOperations.save(Object) method.  The actual write is delayed
			until the last possible moment before the transaction commits.</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">Neo4jItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">Given a Neo4jOperations object, items are persisted via the
			save(Object) method or deleted via the delete(Object) per the <code class="classname">
				ItemWriter</code>'s configuration</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">PropertyExtractingDelegatingItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">Extends AbstractMethodInvokingDelegator
			creating arguments on the fly. Arguments are created by retrieving
			the values from the fields in the item to be processed (via a
			SpringBeanWrapper) based on an injected array of field
			name</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; border-bottom: 0.5pt solid ; " align="left">RepositoryItemWriter</td><td style="border-bottom: 0.5pt solid ; " align="left">给定一个 Spring Data CrudRepository 实现, 则使用配置文件指定的方法保存 item。</td>
		</tr>
		<tr>
			<td style="border-right: 0.5pt solid ; " align="left">StaxEventItemWriter</td><td style="" align="left">通过<span class="bold"><strong>ObjectToXmlSerializer</strong></span> 对象将每个 item 转换为XML, 然后用 StAX 将这些内容写入 XML 文件。</td>
		</tr>
	</tbody>
</table>









