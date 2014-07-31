#Java 开源项目: Spring Batch #
## 使用Spring Batch读取CSV文件并写入到MySQL中 ##

原文链接: [Reading and writing CVS files with Spring Batch and MySQL](http://www.javaworld.com/article/2458888/spring-framework/open-source-java-projects-spring-batch.html)

原文作者: [Steven Haines - 技术架构师](http://www.javaworld.com/author/Steven-Haines/)



实现批处理流程来处理GB的数据是一个名副其实的海啸的任务,但是你可以带下来一块Spring batch的帮助。 这个流行的Spring模块被设计来处理各种文件的批处理的细节。 开始使用Spring Batch通过构建一个简单的工作,进口产品从CSV文件到一个MySQL数据库,然后探索模块的批处理功能与一个或多个处理器和一个或多个有用的微线程。 最后,得到一个快速概述Spring Batch的弹性的工具不记录,重新尝试记录,和重新启动批处理作业。

如果你曾经不得不执行一个批处理通过成千上万的Java企业系统之间的数据元素,那么你知道什么是负载的工作。 你的批处理系统需要能够处理大量的数据,处理个人的失败记录没有崩溃的整个过程,和管理中断和重新启动而无需重新已经做了什么。

对于不太了解形势的人来说,这里有一些场景需要批处理,并使用Spring batch,可能会节省你的无数个小时:

你收到的文件中缺少一些信息,所以你解析文件,调用服务来检索信息缺失,写文件,另一个批处理过程。
当一个错误发生在您的环境中,你写失败消息到您的数据库。 你有一个过程,寻找失败的消息每15分钟和回放那些你已经确认为可复制。
你有一个工作流,你希望其他系统调用特定服务,除了接收到的事件。 如果这些其他系统不打电话给你服务,那么你几天后自动清理您的数据,以便业务流程不失败。
你收到一个文件包含员工每天更新,你需要为新员工创建的工件。
你有服务,可用于定制订单。 每天晚上你运行一个批处理过程,构造清单文件并将它们发送给您的供应商实现。

工作和块:Spring Batch模式

Spring Batch有很多移动部件,但让我们首先看核心处理,你会在一个批处理作业。 你可以考虑工作的工作如下三个不同的步骤:

阅读
处理
写作
例如,您可以打开一个CSV格式的数据文件,对文件中的数据执行一些处理,然后将数据写入数据库。 在Spring Batch,您将配置一个 读者 读文件的一行,每一行传递给你 处理器 ,处理器会收集和组织结果成“块”,把这些记录 作家 ,插入到数据库中。 你可以看到图1中的循环。

图1所示。 Spring的批处理的基本逻辑

![Spring Batch批处理的基本逻辑](./fig1-basicl-ogic.png)


Spring Batch的大大简化批处理提供了实现读者等常见的输入源的CSV文件,XML文件、数据库、JSON记录中包含一个文件,甚至是JMS以及作家。 这也是相当简单的构建定制的读者和作家如果你需要。

开始,让我们看看配置文件阅读器阅读一个CSV文件,其内容映射到一个对象,并将生成的对象插入数据库。

本教程下载的源代码
SpringBatch-CVS演示代码

阅读和处理一个CVS文件

Spring Batch内置的读者, org.springframework.batch.item.file.FlatFileItemReader 解析文件成单个行。 它需要一个参考平面文件资源,跳过的行数的文件(通常是文件头),和一个 行映射器 ,将单个线转换成一个对象。 映射器,需要一个 行编译器 将一条线划分为其组成字段, 字段设置映射器 构建一个对象的字段值。 的配置 FlatFileItemReader 如下所示:

清单1。 一个Spring的批处理配置文件

    <bean id="productReader" class="org.springframework.batch.item.file.FlatFileItemReader" scope="step">

        <!-- <property name="resource" value="file:./sample.csv" /> -->
        <property name="resource" value="file:#{jobParameters['inputFile']}" />

        <property name="linesToSkip" value="1" />

        <property name="lineMapper">
            <bean class="org.springframework.batch.item.file.mapping.DefaultLineMapper">

                <property name="lineTokenizer">
                    <bean class="org.springframework.batch.item.file.transform.DelimitedLineTokenizer">
                        <property name="names" value="id,name,description,quantity" />
                    </bean>
                </property>

                <property name="fieldSetMapper">
                    <bean class="com.geekcap.javaworld.springbatchexample.simple.reader.ProductFieldSetMapper" />
                </property>
            </bean>
        </property>
    </bean>


让我们来看看这些组件。 首先,图2显示了它们之间的关系图。

图2。 组件的FlatFileItemReader
![FlatFileItemReader组件](./fig2-FlatFileItemReader.png)

资源 : 资源 属性定义了文件阅读。 绝对文件中注释掉资源显示路径,这是 sample.csv 在同一个目录中运行批处理作业。 更有趣的条目 InputFile 工作参数: 工作参数 允许您指定在运行时参数影响的工作。 在导入文件的情况下,它是一个非常重要的参数,以解决在运行时,而不是在构建时。 (这将是相当无聊的导入相同的文件一次又一次!)

行不 : linesToSkip 属性告诉文件阅读器有多少领导行文件跳过。 经常CSV文件将包含头信息,如列名称,在文件的第一行,所以在这个例子中,我们告诉读者跳过第一行的文件。

行映射器 : lineMapper 负责个人行一个文件转换成对象。 这取决于两个组件:

LineTokenizer 定义了如何打破排队到令牌。 在我们的例子中我们列表的名字列在CSV文件中。
fieldSetMapper 构建一个对象的字段值。 在我们的例子中我们建立一个 产品 对象的 ID , 的名字 , 描述 , 数量 字段。
注意,Spring Batch为我们提供了基础设施,但我们仍然负责该领域的逻辑映射器。 清单2显示了的源代码 产品 对象,该对象我们建筑。

清单1。 Product.java

	package com.geekcap.javaworld.springbatchexample.simple.model;

	/**
	 * Simple POJO to represent a product
	 */
	public class Product
	{
	    private int id;
	    private String name;
	    private String description;
	    private int quantity;

	    public Product() {
	    }

	    public Product(int id, String name, String description, int quantity) {
		this.id = id;
		this.name = name;
		this.description = description;
		this.quantity = quantity;
	    }

	    public int getId() {
		return id;
	    }

	    public void setId(int id) {
		this.id = id;
	    }

	    public String getName() {
		return name;
	    }

	    public void setName(String name) {
		this.name = name;
	    }

	    public String getDescription() {
		return description;
	    }

	    public void setDescription(String description) {
		this.description = description;
	    }

	    public int getQuantity() {
		return quantity;
	    }

	    public void setQuantity(int quantity) {
		this.quantity = quantity;
	    }
	}


的 产品 类是一个简单的POJO,包装我们的4个字段。 清单2显示了的源代码 ProductFieldSetMapper 类。



清单2。 ProductFieldSetMapper.java


	package com.geekcap.javaworld.springbatchexample.simple.reader;
	
	import com.geekcap.javaworld.springbatchexample.simple.model.Product;
	import org.springframework.batch.item.file.mapping.FieldSetMapper;
	import org.springframework.batch.item.file.transform.FieldSet;
	import org.springframework.validation.BindException;
	
	/**
	 * Builds a Product from a row in the CSV file (as a set of fields)
	 */
	public class ProductFieldSetMapper implements FieldSetMapper<Product>
	{
	    @Override
	    public Product mapFieldSet(FieldSet fieldSet) throws BindException {
	        Product product = new Product();
	        product.setId( fieldSet.readInt( "id" ) );
	        product.setName( fieldSet.readString( "name" ) );
	        product.setDescription( fieldSet.readString( "description" ) );
	        product.setQuantity( fieldSet.readInt( "quantity" ) );
	        return product;
	    }
	}


的 ProductFieldSetMapper 类继承了 fieldSetMapper ,它定义了一个方法: mapFieldSet() 。 一旦行映射器解析成单独的字段,它构建一个 自定义字段 ,其中包含命名字段,通过的 mapFieldSet() 方法。 该方法负责建立一个CSV文件中的对象来表示行。 在我们的例子中,我们建立一个 产品 通过调用不同的实例 读 的方法 自定义字段 。

写入数据库

之后我们读取的文件,并有一组 产品 年代,下一步是编写数据库。 技术上我们可以连接在一个处理步骤,做了一些数据,但现在我们只写数据到数据库中。 清单3显示了源代码 ProductItemWriter 类。

清单3。 ProductItemWriter.java


	package com.geekcap.javaworld.springbatchexample.simple.writer;
	
	import com.geekcap.javaworld.springbatchexample.simple.model.Product;
	import org.springframework.batch.item.ItemWriter;
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.jdbc.core.JdbcTemplate;
	import org.springframework.jdbc.core.RowMapper;
	
	import java.sql.ResultSet;
	import java.sql.SQLException;
	import java.util.List;
	
	/**
	 * Writes products to a database
	 */
	public class ProductItemWriter implements ItemWriter<Product>
	{
	    private static final String GET_PRODUCT = "select * from PRODUCT where id = ?";
	    private static final String INSERT_PRODUCT = "insert into PRODUCT (id,name,description,quantity) values (?,?,?,?)";
	    private static final String UPDATE_PRODUCT = "update PRODUCT set name = ?, description = ?,quantity = ? where id = ?";
	
	    @Autowired
	    private JdbcTemplate jdbcTemplate;
	
	    @Override
	    public void write(List<? extends Product> products) throws Exception
	    {
	        for( Product product : products )
	        {
	            List<Product> productList = jdbcTemplate.query(GET_PRODUCT, new Object[] {product.getId()}, new RowMapper<Product>() {
	                @Override
	                public Product mapRow( ResultSet resultSet, int rowNum ) throws SQLException {
	                    Product p = new Product();
	                    p.setId( resultSet.getInt( 1 ) );
	                    p.setName( resultSet.getString( 2 ) );
	                    p.setDescription( resultSet.getString( 3 ) );
	                    p.setQuantity( resultSet.getInt( 4 ) );
	                    return p;
	                }
	            });
	
	            if( productList.size() > 0 )
	            {
	                jdbcTemplate.update( UPDATE_PRODUCT, product.getName(), product.getDescription(), product.getQuantity(), product.getId() );
	            }
	            else
	            {
	                jdbcTemplate.update( INSERT_PRODUCT, product.getId(), product.getName(), product.getDescription(), product.getQuantity() );
	            }
	        }
	    }
	}


的 ProductItemWriter 类继承了 ItemWriter 并实现其单一的方法: write() 。 的 write() 方法接受的列表 产品 年代。Spring Batch实现其作者使用“组块”策略,这意味着当读取一次执行一个项目,分块成组写道。 下面的任务配置定义,您可以完全控制项的数量你想要一起(通过分块 commit-interval )到一个写。 在这个例子中, write() 方法如下:

它执行一个SQL SELECT语句来检索 产品 与指定的 ID 。
如果选择返回一个项目 write() 执行一个更新来更新数据库记录的新值。
如果选择不返回一个项目 write() 执行INSERT将产品添加到数据库中。
的 ProductItemWriter 类使用Spring的 jdbctemplate 中定义的类,它是 中 文件并自动连接到以下 ProductItemWriter 类。 如果你还没有使用 jdbctemplate 类,它是一个“四人帮”的实现 模板设计模式 与数据库进行交互背后的JDBC接口。 代码应该很容易读懂,但是如果你需要更多信息,查看 SpringJdbcTemplate javadoc。

在应用程序上下文文件连接在一起

到目前为止我们已经建立了一个 产品 域对象, ProductFieldSetMapper ,将CSV文件中的一行转换成一个对象,和一个 ProductItemWriter 将对象写入数据库。 现在我们需要配置Spring Batch连接所有这些在一起。 清单4显示了源代码 中 文件,它定义了我们的bean。


Listing 4. applicationContext.xml


	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xmlns:batch="http://www.springframework.org/schema/batch"
	       xmlns:jdbc="http://www.springframework.org/schema/jdbc"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
	                http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
	                http://www.springframework.org/schema/batch http://www.springframework.org/schema/batch/spring-batch.xsd
	                http://www.springframework.org/schema/jdbc http://www.springframework.org/schema/jdbc/spring-jdbc.xsd">
	
	
	    <context:annotation-config />
	
	    <!-- Component scan to find all Spring components -->
	    <context:component-scan base-package="com.geekcap.javaworld.springbatchexample" />
	
	
	    <!-- Data source - connect to a MySQL instance running on the local machine -->
	    <bean id="dataSource" class="org.apache.commons.dbcp.BasicDataSource" destroy-method="close">
	        <property name="driverClassName" value="com.mysql.jdbc.Driver"/>
	        <property name="url" value="jdbc:mysql://localhost/spring_batch_example"/>
	        <property name="username" value="sbe"/>
	        <property name="password" value="sbe"/>
	    </bean>
	
	    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
	        <property name="dataSource" ref="dataSource" />
	    </bean>
	
	    <bean id="jdbcTemplate" class="org.springframework.jdbc.core.JdbcTemplate">
	        <property name="dataSource" ref="dataSource" />
	    </bean>
	
	    <!-- Create job-meta tables automatically -->
	    <jdbc:initialize-database data-source="dataSource">
	        <jdbc:script location="org/springframework/batch/core/schema-drop-mysql.sql" />
	        <jdbc:script location="org/springframework/batch/core/schema-mysql.sql" />
	    </jdbc:initialize-database>
	
	
	    <!-- Job Repository: used to persist the state of the batch job -->
	    <bean id="jobRepository" class="org.springframework.batch.core.repository.support.MapJobRepositoryFactoryBean">
	        <property name="transactionManager" ref="transactionManager" />
	    </bean>
	
	
	    <!-- Job Launcher: creates the job and the job state before launching it -->
	    <bean id="jobLauncher" class="org.springframework.batch.core.launch.support.SimpleJobLauncher">
	        <property name="jobRepository" ref="jobRepository" />
	    </bean>
	
	
	    <!-- Reader bean for our simple CSV example -->
	    <bean id="productReader" class="org.springframework.batch.item.file.FlatFileItemReader" scope="step">
	
	        <!-- <property name="resource" value="file:./sample.csv" /> -->
	        <property name="resource" value="file:#{jobParameters['inputFile']}" />
	
	
	        <!-- Skip the first line of the file because this is the header that defines the fields -->
	        <property name="linesToSkip" value="1" />
	
	        <!-- Defines how we map lines to objects -->
	        <property name="lineMapper">
	            <bean class="org.springframework.batch.item.file.mapping.DefaultLineMapper">
	
	                <!-- The lineTokenizer divides individual lines up into units of work -->
	                <property name="lineTokenizer">
	                    <bean class="org.springframework.batch.item.file.transform.DelimitedLineTokenizer">
	
	                        <!-- Names of the CSV columns -->
	                        <property name="names" value="id,name,description,quantity" />
	                    </bean>
	                </property>
	
	                <!-- The fieldSetMapper maps a line in the file to a Product object -->
	                <property name="fieldSetMapper">
	                    <bean class="com.geekcap.javaworld.springbatchexample.simple.reader.ProductFieldSetMapper" />
	                </property>
	            </bean>
	        </property>
	    </bean>
	
	    <bean id="productWriter" class="com.geekcap.javaworld.springbatchexample.simple.writer.ProductItemWriter" />
	
	</beans>


注意,将我们的工作从我们的应用程序/配置环境配置使我们能够将工作从一个环境移动到另一个没有重新定义工作。 下面的清单4中定义bean:

数据源 :样例应用程序连接到MySQL,数据源配置为连接到一个MySQL数据库命名 spring_batch_example 在本地主机上运行设置说明(见下文)。
transactionmanager :Spring事务管理器是用于管理MySQL的事务。
jdbctemplate :这类提供了模板设计模式的实现与JDBC连接交互。 这是一个助手类来简化我们的数据库集成。 在生产应用程序中我们可能会选择使用ORM工具Hibernate等在服务层,但我想保持尽可能简单的例子。
jobrepository : MapJobRepositoryFactoryBean 是一个Spring Batch组件,管理工作的状态。 在这种情况下它存储工作到MySQL数据库使用前面配置的信息 jdbctemplate 。
jobLauncher :这是组件,启动和管理工作流的Spring的批处理作业。
productReader :这个bean执行读操作在我们的工作。
productWriter :这个bean写道 产品 到数据库实例。
注意, jdbc:initialize-database 节点指向两个Spring Batch的脚本创建数据库表来支持运行批处理作业。 这些脚本位于Spring Batch核心JAR文件(由Maven自动进口)在指定的路径。 JAR文件包含脚本为各种数据库供应商,包括MySQL、Oracle、SQL Server,等等。 这些脚本创建的模式运行时使用工作。 在这个例子中它滴,然后创建表,你可以做一个临时运行。 在生产环境中你可以自己提取SQL文件和创建表——在这种情况下,你可以让他们永远在。

#懒惰在Spring Batch范围
你可能已经注意到 productReader bean定义为一个“步骤”范围。 的 一步 范围是Spring框架范围之一,特定于Spring Batch。 它本质上是一个 懒惰的范围 告诉Spring创建bean首次访问时。 在这种情况下,我们需要选择一步因为资源使用的工作参数范围的“ InputFile “价值,将不会创建应用程序上下文时可用。 使用步骤使Spring Batch能够接收范围” InputFile “价值并使其创建bean时可用。

定义工作

清单5显示了 file-import-job.xml 文件,它定义了实际工作。

清单5。 file-import-job.xml


	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xmlns:batch="http://www.springframework.org/schema/batch"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
	                http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
	                http://www.springframework.org/schema/batch http://www.springframework.org/schema/batch/spring-batch.xsd">
	
	
	    <!-- Import our beans -->
	    <import resource="classpath:/applicationContext.xml" />
	
	    <job id="simpleFileImportJob" xmlns="http://www.springframework.org/schema/batch">
	        <step id="importFileStep">
	            <tasklet>
	                <chunk reader="productReader" writer="productWriter" commit-interval="5" />
	            </tasklet>
	        </step>
	    </job>
	
	</beans>


请注意, 工作 可以包含零个或多个步骤; 一步 可以包含零个或一个微; 微 可以包含零个或一块,如图3中以图形的方式说明了。

图3。 工作,步骤、微线程和块

![ Jobs, steps, tasklets, and chunks](./fig3-chunks.png)

在我们的示例中, simpleFileImportJob 包含一个单步命名 importFileStep 。 的 importFileStep 包含一块包含一个不知名的微线程。 块是我们配置一个引用 productReader 和 productWriter 。 它定义了一个 commit-interval 5,这意味着它将作者5记录一次。 一步将读取5个产品使用 productReader 然后通过这些产品 productWriter 写出来。 这个查克重复,直到耗尽所有的数据。

清单5还进口了 中 文件,其中包含我们所有的bean。 工作通常在单独的文件中定义;这是因为发射器的工作需要一个工作文件和工作名称时执行。 一切可以被定义在一个文件中,但是它会很快变得笨拙,所以作为一个约定,一个文件中定义的工作是和进口所有依赖文件。

最后,你可能会注意到,XML名称空间( XMLNS )内的定义 工作 节点。 我们这么做,这样我们不需要序言每个节点” 批处理: 。 “定义名称空间在节点级别影响节点定义它和所有的子节点。

构建项目

清单6显示了POM文件的内容,构建此示例项目。

Listing 6. pom.xml


	<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	  <modelVersion>4.0.0</modelVersion>
	
	  <groupId>com.geekcap.javaworld</groupId>
	  <artifactId>spring-batch-example</artifactId>
	  <version>1.0-SNAPSHOT</version>
	  <packaging>jar</packaging>
	
	  <name>spring-batch-example</name>
	  <url>http://maven.apache.org</url>

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <spring.version>3.2.1.RELEASE</spring.version>
        <spring.batch.version>2.2.1.RELEASE</spring.batch.version>
        <java.version>1.6</java.version>
    </properties>

    <dependencies>
        <!-- Spring Dependencies -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-context</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-core</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-beans</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-jdbc</artifactId>
            <version>${spring.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.batch</groupId>
            <artifactId>spring-batch-core</artifactId>
            <version>${spring.batch.version}</version>
        </dependency>
        <dependency>
            <groupId>org.springframework.batch</groupId>
            <artifactId>spring-batch-infrastructure</artifactId>
            <version>${spring.batch.version}</version>
        </dependency>

        <!-- Apache DBCP-->
        <dependency>
            <groupId>commons-dbcp</groupId>
            <artifactId>commons-dbcp</artifactId>
            <version>1.4</version>
        </dependency>

        <!-- MySQL -->
        <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>5.1.27</version>
        </dependency>


        <!-- Testing -->
        <dependency>
            <groupId>junit</groupId>
            <artifactId>junit</artifactId>
            <version>4.11</version>
            <scope>test</scope>
        </dependency>
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <source>${java.version}</source>
                    <target>${java.version}</target>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <configuration>
                    <archive>
                        <manifest>
                            <addClasspath>true</addClasspath>
                            <classpathPrefix>lib/</classpathPrefix>
                        </manifest>
                    </archive>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
                <executions>
                    <execution>
                        <id>copy</id>
                        <phase>install</phase>
                        <goals>
                            <goal>copy-dependencies</goal>
                        </goals>
                        <configuration>
                            <outputDirectory>${project.build.directory}/lib</outputDirectory>
                        </configuration>
                    </execution>
                </executions>
            </plugin>
        </plugins>
        <finalName>spring-batch-example</finalName>
    </build>


	</project>



POM文件导入Spring的背景下,核心,豆类,和JDBC包,然后进口Spring Batch的核心和基础设施的包。 这些依赖项设置弹簧和弹簧的批处理。 也导入Apache DBCP依赖使我们能够建立一个数据库连接池和MySQL驱动程序。 插件部分定义了构建使用Java 1.6和配置构建将所有依赖项复制到 自由 目录中。 使用下面的命令来构建项目:


	mvn clean install



Spring Batch连接到一个数据库

你的工作是设置但你需要将Spring Batch连接到数据库,如果你想在生产环境中运行它。 Spring Batch维护一组表,用于记录工作的当前状态和记录处理。 这样,如果一份工作确实需要重新启动,它可以继续从那里离开。

你可以将Spring Batch连接到任何你喜欢的数据库,但对于本演示,我们将使用MySQL。 请 下载MySQL 遵循的例子。 社区版是免费的,并将满足您的需要。 检查你的操作系统的安装说明的环境中运行。

一旦你MySQL建立你需要创建数据库和用户权限与数据库进行交互。 从命令行启动 MySQL 从MySQL的bin目录并执行以下命令(请注意,您可能需要执行 MySQL 作为根用户或使用 SUDO 根据您的操作系统):


	create database spring_batch_example;
	create user 'sbe'@'localhost' identified by 'sbe';
	grant all on spring_batch_example.* to 'sbe'@'localhost';


第一行创建一个新的数据库命名 spring_batch_example 将保持你的产品。 第二行创建一个用户 sbE ( Spring Batch的例子 )和密码 sbE 。 最后一行上的所有权限 spring_batch_example 数据库的 sbE 用户。

接下来,创建 产品 表使用下面的命令:


	CREATE TABLE PRODUCT (
		ID INT NOT NULL,
		NAME VARCHAR(128) NOT NULL,
		DESCRIPTION VARCHAR(128),
		QUANTITY INT,
		PRIMARY KEY(ID)
	);


现在创建一个文件命名 sample.csv 在您的项目的目标目录下面的数据:


	id,name,description,quantity
	1,Product One,This is product 1, 10
	2,Product Two,This is product 2, 20
	3,Product Three,This is product 3, 30
	4,Product Four,This is product 4, 20
	5,Product Five,This is product 5, 10
	6,Product Six,This is product 6, 50
	7,Product Seven,This is product 7, 80
	8,Product Eight,This is product 8, 90


批处理作业可以推出:
--
java -cp spring-batch-example.jar:./lib/* org.springframework.batch.core.launch.support.CommandLineJobRunner classpath:/jobs/file-import-job.xml simpleFileImportJob inputFile=sample.csv
--

的 CommandLineJobRunner 类是一个Spring Batch类执行工作。 它需要的XML文件的名称,包含工作,工作执行的名称,选择任何工作参数,您想要发送。 因为 file-import-job.xml 内部文件的JAR文件,它可以访问如下: 类路径:/ / file-import-job.xml工作 。 我们想要执行的工作 simpleFileImportJob 并通过一个工作参数命名 InputFile 的价值 sample.csv 。

亨得利托马斯于下面的输出结果:

	Nov 12, 2013 4:09:17 PM org.springframework.context.support.AbstractApplicationContext prepareRefresh
	INFO: Refreshing org.springframework.context.support.ClassPathXmlApplicationContext@6b4da8f4: startup date [Tue Nov 12 16:09:17 EST 2013]; root of context hierarchy
	Nov 12, 2013 4:09:17 PM org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
	INFO: Loading XML bean definitions from class path resource [jobs/file-import-job.xml]
	Nov 12, 2013 4:09:18 PM org.springframework.beans.factory.xml.XmlBeanDefinitionReader loadBeanDefinitions
	INFO: Loading XML bean definitions from class path resource [applicationContext.xml]
	Nov 12, 2013 4:09:19 PM org.springframework.beans.factory.support.DefaultListableBeanFactory registerBeanDefinition
	INFO: Overriding bean definition for bean 'simpleFileImportJob': replacing [Generic bean: class [org.springframework.batch.core.configuration.xml.SimpleFlowFactoryBean]; scope=; abstract=false; lazyInit=false; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null] with [Generic bean: class [org.springframework.batch.core.configuration.xml.JobParserJobFactoryBean]; scope=; abstract=false; lazyInit=false; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null]
	Nov 12, 2013 4:09:19 PM org.springframework.beans.factory.support.DefaultListableBeanFactory registerBeanDefinition
	INFO: Overriding bean definition for bean 'productReader': replacing [Generic bean: class [org.springframework.batch.item.file.FlatFileItemReader]; scope=step; abstract=false; lazyInit=false; autowireMode=0; dependencyCheck=0; autowireCandidate=false; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null; defined in class path resource [applicationContext.xml]] with [Root bean: class [org.springframework.aop.scope.ScopedProxyFactoryBean]; scope=; abstract=false; lazyInit=false; autowireMode=0; dependencyCheck=0; autowireCandidate=true; primary=false; factoryBeanName=null; factoryMethodName=null; initMethodName=null; destroyMethodName=null; defined in BeanDefinition defined in class path resource [applicationContext.xml]]
	Nov 12, 2013 4:09:19 PM org.springframework.beans.factory.support.DefaultListableBeanFactory preInstantiateSingletons
	INFO: Pre-instantiating singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@6aba4211: defining beans [org.springframework.context.annotation.internalConfigurationAnnotationProcessor,org.springframework.context.annotation.internalAutowiredAnnotationProcessor,org.springframework.context.annotation.internalRequiredAnnotationProcessor,org.springframework.context.annotation.internalCommonAnnotationProcessor,dataSource,transactionManager,jdbcTemplate,jobRepository,jobLauncher,productReader,productWriter,org.springframework.batch.core.scope.internalStepScope,org.springframework.beans.factory.config.CustomEditorConfigurer,org.springframework.batch.core.configuration.xml.CoreNamespacePostProcessor,importFileStep,simpleFileImportJob,org.springframework.context.annotation.ConfigurationClassPostProcessor.importAwareProcessor,scopedTarget.productReader]; root of factory hierarchy
	Nov 12, 2013 4:09:19 PM org.springframework.batch.core.launch.support.SimpleJobLauncher afterPropertiesSet
	INFO: No TaskExecutor has been set, defaulting to synchronous executor.
	Nov 12, 2013 4:09:22 PM org.springframework.batch.core.launch.support.SimpleJobLauncher$1 run
	INFO: Job: [FlowJob: [name=simpleFileImportJob]] launched with the following parameters: [{inputFile=sample.csv}]
	Nov 12, 2013 4:09:22 PM org.springframework.batch.core.job.SimpleStepHandler handleStep
	INFO: Executing step: [importFileStep]
	Nov 12, 2013 4:09:22 PM org.springframework.batch.core.launch.support.SimpleJobLauncher$1 run
	INFO: Job: [FlowJob: [name=simpleFileImportJob]] completed with the following parameters: [{inputFile=sample.csv}] and the following status: [COMPLETED]
	Nov 12, 2013 4:09:22 PM org.springframework.context.support.AbstractApplicationContext doClose
	INFO: Closing org.springframework.context.support.ClassPathXmlApplicationContext@6b4da8f4: startup date [Tue Nov 12 16:09:17 EST 2013]; root of context hierarchy
	Nov 12, 2013 4:09:22 PM org.springframework.beans.factory.support.DefaultSingletonBeanRegistry destroySingletons
	INFO: Destroying singletons in org.springframework.beans.factory.support.DefaultListableBeanFactory@6aba4211: defining beans [org.springframework.context.annotation.internalConfigurationAnnotationProcessor,org.springframework.context.annotation.internalAutowiredAnnotationProcessor,org.springframework.context.annotation.internalRequiredAnnotationProcessor,org.springframework.context.annotation.internalCommonAnnotationProcessor,dataSource,transactionManager,jdbcTemplate,jobRepository,jobLauncher,productReader,productWriter,org.springframework.batch.core.scope.internalStepScope,org.springframework.beans.factory.config.CustomEditorConfigurer,org.springframework.batch.core.configuration.xml.CoreNamespacePostProcessor,importFileStep,simpleFileImportJob,org.springframework.context.annotation.ConfigurationClassPostProcessor.importAwareProcessor,scopedTarget.productReader]; root of factory hierarchy




验证 产品 表在数据库中包含八行和他们有正确的值。

与Spring Batch的批处理

此时,例子从CSV文件中读取数据,并将该信息导入到数据库中。 虽然这是有用的,很有可能你会有时候想改变或过滤数据之前将它插入到数据库中。 在本节中,我们将构建一个简单的处理器,而不是覆盖产品的数量,而不是从数据库中检索现有记录,然后将CSV文件中的数量添加到产品之前的作家。

清单7显示了的源代码 ProductItemProcessor 类。

清单7。 ProductItemProcessor.java


	package com.geekcap.javaworld.springbatchexample.simple.processor;
	
	import com.geekcap.javaworld.springbatchexample.simple.model.Product;
	import org.springframework.batch.item.ItemProcessor;
	import org.springframework.beans.factory.annotation.Autowired;
	import org.springframework.jdbc.core.JdbcTemplate;
	import org.springframework.jdbc.core.RowMapper;
	
	import java.sql.ResultSet;
	import java.sql.SQLException;
	import java.util.List;
	
	/**
	 * Processor that finds existing products and updates a product quantity appropriately
	 */
	public class ProductItemProcessor implements ItemProcessor<Product,Product>
	{
	    private static final String GET_PRODUCT = "select * from PRODUCT where id = ?";
	    @Autowired
	    private JdbcTemplate jdbcTemplate;
	
	    @Override
	    public Product process(Product product) throws Exception
	    {
	        // Retrieve the product from the database
	        List<Product> productList = jdbcTemplate.query(GET_PRODUCT, new Object[] {product.getId()}, new RowMapper<Product>() {
	            @Override
	            public Product mapRow( ResultSet resultSet, int rowNum ) throws SQLException {
	                Product p = new Product();
	                p.setId( resultSet.getInt( 1 ) );
	                p.setName( resultSet.getString( 2 ) );
	                p.setDescription( resultSet.getString( 3 ) );
	                p.setQuantity( resultSet.getInt( 4 ) );
	                return p;
	            }
	        });
	
	        if( productList.size() > 0 )
	        {
	            // Add the new quantity to the existing quantity
	            Product existingProduct = productList.get( 0 );
	            product.setQuantity( existingProduct.getQuantity() + product.getQuantity() );
	        }
	
	        // Return the (possibly) update prduct
	        return product;
	    }
	}


项处理器实现的接口 ItemProcessor < I,O > ,在那里 我 类型的对象发送到处理器和 O 返回的是对象的类型的处理器。 在这个例子中,我们通过在一个 产品 然后返回一个 产品 。 的 ItemProcessor 定义了一个方法: 过程() ,我们执行 选择 查询检索 产品 与指定的 ID 从数据库中。 如果 产品 发现,将现有的吗 产品 数量的新数量。

这个处理器并不做任何过滤,但如果 过程() 方法返回 空 Spring Batch会忽略这个项目从列表中被发送到作家。

连接到工作非常简单。 首先,添加一个新的bean 中 文件:


	<bean id="productProcessor" class="com.geekcap.javaworld.springbatchexample.simple.processor.ProductItemProcessor" />

接下来,引用的 块 随着 处理器 :



    <job id="simpleFileImportJob" xmlns="http://www.springframework.org/schema/batch">
        <step id="importFileStep">
            <tasklet>
                <chunk reader="productReader" processor="productProcessor" writer="productWriter" commit-interval="5" />
            </tasklet>
        </step>
    </job>


建立和执行工作,您应该看到数据库中的产品数量增加每次运行该批处理作业。

建立多个处理器

我们定义一个处理器,但在某种情况下,您可能想要建立几个finely-grained条目处理器和执行都先后在同一块。 例如,您可能有一个过滤器来跳过项不存在于数据库和一个处理器,正确地管理项目数量。 如果是这种情况,那么您可以使用Spring Batch的 CompositeItemProcessor 。 流程如下:

	构建处理器类
	在你定义处理器bean 中 文件
	定义一个类型的bean org.springframework.batch.item.support.CompositeItemProcessor 并设置其 代表 到你想执行的处理器bean列表
	定义 块 的 处理器 引用 CompositeItemProcessor


考虑到我们有一个假设 ProductFilterProcessor ,我们可以写流程如下:

	<bean id="productFilterProcessor" class="com.geekcap.javaworld.springbatchexample.simple.processor.ProductFilterItemProcessor" />
	
	<bean id="productProcessor" class="com.geekcap.javaworld.springbatchexample.simple.processor.ProductItemProcessor" />
	
	<bean id="productCompositeProcessor" class="org.springframework.batch.item.support.CompositeItemProcessor">
		<property name="delegates">
			<list>
				<ref bean="productFilterProcessor" />
				<ref bean="productProcessor" />
			</list>
		</property>
	</bean>


然后只需修改工作配置,如:

    <job id="simpleFileImportJob" xmlns="http://www.springframework.org/schema/batch">
        <step id="importFileStep">
            <tasklet>
                <chunk reader="productReader" processor="productCompositeProcessor" writer="productWriter" commit-interval="5" />
            </tasklet>
        </step>
    </job>


微线程

将工作划分为组块是一个非常好的战略,嗯,块:阅读项目一个一个,处理它们,然后把它们写在一块。 线性操作,但是如果你有一个你想执行需要执行一次? 在这种情况下,你可以建立一个 微 。 微线程可以做任何你需要做的! 例如,它可以从一个FTP站点下载一个文件,解压缩或解密文件,或调用一个web服务来确定是否已经批准执行文件处理。 这里的基本过程建立一个微线程:


定义一个类实现 org.springframework.batch.core.step.tasklet.Tasklet 。
实现 execute() 方法。
返回适当的 org.springframework.batch.repeat.RepeatStatus 值: 可持续 或 完成了 。
定义的bean 中 文件。
创建一个 一步 这有一个 微 引用您的bean。
清单8展示了新的微,档案的内容我们输入文件并复制到存档目录。

清单8。 ArchiveProductImportFileTasklet.java


	package com.geekcap.javaworld.springbatchexample.simple.tasklet;
	
	import org.apache.commons.io.FileUtils;
	import org.springframework.batch.core.StepContribution;
	import org.springframework.batch.core.scope.context.ChunkContext;
	import org.springframework.batch.core.step.tasklet.Tasklet;
	import org.springframework.batch.repeat.RepeatStatus;
	
	import java.io.File;
	
	/**
	 * A tasklet that archives the input file
	 */
	public class ArchiveProductImportFileTasklet implements Tasklet
	{
	    private String inputFile;
	
	    @Override
	    public RepeatStatus execute(StepContribution stepContribution, ChunkContext chunkContext) throws Exception
	    {
	        // Make our destination directory and copy our input file to it
	        File archiveDir = new File( "archive" );
	        FileUtils.forceMkdir( archiveDir );
	        FileUtils.copyFileToDirectory( new File( inputFile ), archiveDir );
	
	        // We're done...
	        return RepeatStatus.FINISHED;
	    }
	
	    public String getInputFile() {
	        return inputFile;
	    }
	
	    public void setInputFile(String inputFile) {
	        this.inputFile = inputFile;
	    }
	}


的 ArchiveProductImportFileTasklet 类实现了 微 接口,并提供了一个实现的 execute() 方法。 它使用Apache Commons I / O fileutils 类来创建一个新的 存档 目录,然后输入文件副本。

bean定义而言,以下bean添加到 中 文件:



    <bean id="archiveFileTasklet" class="com.geekcap.javaworld.springbatchexample.simple.tasklet.ArchiveProductImportFileTasklet" scope="step">
        <property name="inputFile" value="#{jobParameters['inputFile']}" />
    </bean>


注意,我们通过 InputFile 工作参数的bean和bean 一步 确保工作范围参数创建bean定义之前。

清单9显示了更新后的工作。

清单9。 file-import-job.xml


	<?xml version="1.0" encoding="UTF-8"?>
	<beans xmlns="http://www.springframework.org/schema/beans"
	       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	       xmlns:context="http://www.springframework.org/schema/context"
	       xmlns:batch="http://www.springframework.org/schema/batch"
	       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
	                http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
	                http://www.springframework.org/schema/batch http://www.springframework.org/schema/batch/spring-batch.xsd">
	
	
	    <!-- Import our beans -->
	    <import resource="classpath:/applicationContext.xml" />
	
	    <job id="simpleFileImportJob" xmlns="http://www.springframework.org/schema/batch">
	        <step id="importFileStep" next="archiveFileStep">
	            <tasklet>
	                <chunk reader="productReader" processor="productProcessor" writer="productWriter" commit-interval="5" />
	            </tasklet>
	        </step>
	        <step id="archiveFileStep">
	            <tasklet ref="archiveFileTasklet" />
	        </step>
	    </job>
	
	</beans>


清单9中添加一个新的名为步骤文件导入工作 archiveFileStep 然后配置后的“下一个”步骤 importFileStep 。 “下一个”参数允许您控制的流程步骤,安排你的工作。 虽然超出了本文的范围,请注意,您可以定义特殊的决定步骤导致工作分支基于任务的完成状态。 的 archiveFileStep 包含一个 微 ,我们上面创建的bean的引用。

弹性

Spring Batch的工作弹性给你三个工具:

跳过 :如果一个元素在你处理不正确,如不正确格式化的线在你的CSV文件,那么你可以选择跳过该对象并继续处理下一个。
重试 :如果出现错误,很有可能再次被重试处理解决在几毫秒,那么你可以选择让Spring Batch重试该元素。 例如,你可能会更新记录在数据库中,但另一个查询,物品锁。 不久,锁定的记录有可能会被释放并重新尝试可能会成功。
重新启动 :如果工作是配置为其状态存储在一个数据库,它失败了,那么你可以选择重新开始,继续在你离开那份工作实例。
虽然我不会去通过每个弹性特性的细节,我想总结的选项可用。

跳跃项目

有时你可能想要跳过无效记录读者或加工过程中出现的异常或写作。 这样做,您可以指定两件事:

定义一个 skip-limit 在你的 块 元素告诉Spring有多少物品可以跳过前工作失败(你可能会处理一些无效的记录,但是如果你有太多然后输入数据可能是无效的)。
定义的列表 skippable-exception-classes 触发跳过的记录,您可以定义 包括 元素的异常将被忽略 排除 元素的异常不会跳过(用在当你想跳过异常层次结构,但排除一个或更多的子类)。
例如:

	<job id="simpleFileImportJob" xmlns="http://www.springframework.org/schema/batch">
        <step id="importFileStep">
            <tasklet>
                <chunk reader="productReader" processor="productProcessor" writer="productWriter" commit-interval="5" skip-limit="10">
			<skippable-exception-classes>
				<include class="org.springframework.batch.item.file.FlatFileParseException" />
			</skippable-exception-classes>
		</chunk>
            </tasklet>
        </step>
    </job>


在这种情况下,记录中 FlatFileParseException 这是将被忽略。 如果有超过10跳过那么工作失败。

重试的物品

在其他情况下,可能发生异常的时候重试是可行的,如失败由于数据库锁。 跳过重试实现非常相似:

定义一个 retry-limit 在你的 块 元素告诉Spring可以重试多少次一个项目之前,它被认为是失败的。 一次记录失败了就不能工作,除非你把重试和跳过。
定义的列表 retryable-exception-classes 触发记录重播;您可以定义 包括 元素将重试的异常 排除 元素的异常不会重试。
例如:

	<job id="simpleFileImportJob" xmlns="http://www.springframework.org/schema/batch">
	    <step id="importFileStep">
	        <tasklet>
	            <chunk reader="productReader" processor="productProcessor" writer="productWriter" commit-interval="5" retry-limit="5">
			<retryable-exception-classes>
				<include class="org.springframework.dao.OptimisticLockingFailureException" />
			</retryable-exception-classes>
		</chunk>
	        </tasklet>
	    </step>
	</job>


你可以结合重试和skippable异常通过定义一个skippable异常类相匹配的重试例外。 所以,如果你有一个例外,触发5回放,5回放之后,如果还在skippable列表中,那么记录将被忽略。 如果例外不是skippable列表后重试5,它将整个工作失败。

重新启动工作

最后,对于工作,做失败了,您可以选择重新启动它们,让他们拿起自己的确切位置。为了做到这一点,你需要开始工作实例使用相同的工作参数和Spring Batch会发现实例数据库中并继续工作。 你可以选择拒绝重启,你可以控制工作中的一个步骤的次数可以重启重试次数(在一些你可能想放弃。)

##总结##

一些业务问题最好的解决方法是使用批处理和Spring batch实现批处理作业提供了一个框架。 Spring Batch定义了一个分块模式有三个阶段:阅读、过程,和写作,以及对阅读和写作常见的资源支持。 这一期的 开源Java项目 系列探讨了Spring Batch做什么以及如何使用它。

我们开始通过构建一个简单的工作,产品从CSV文件导入到数据库,然后扩展,通过添加一个处理器工作管理产品数量。 最后,我们写了一个单独的微档案输入文件。 虽然不是示例的一部分,Spring Batch的弹性特性很重要,所以我很快了三个弹性Spring Batch提供工具:跳过的记录,重新尝试记录,和重新启动批处理作业。

本文只触及表面Spring Batch的能力,但我希望它给你足够的开始构建自己的Spring的批处理作业。