
创建一个批处理服务

Creating a Batch Service
http://spring.io/guides/gs/batch-processing/



本文将通过实例引导您完成创建一个基本的batch-driven解决方案。

你将构建什么

您将构建一个服务,从CSV文件读取数据,转换为自定义对象,并将最终结果存储到数据库中。

你需要准备什么

大约15分钟
一个常用的文本编辑器或IDE
JDK 1.6 或更高版本
Gradle 1.11 + 或 Maven 3.0 +
你也可以在  Spring Tool Suite (STS) 中直接打开本指南的web页面,并通过它直接导入相关的代码。

如何使用这个指南

最喜欢春天 入门指南 ,你可以从头开始并完成每一步,你也可以绕过你已经熟悉的基本设置步骤。 无论哪种方式,你最终得到的工作代码。

来 从头开始 ,继续 设置项目 。

来 跳过基础知识 ,请执行以下操作:

下载 并解压缩源库本指南,或者克隆使用 Git : git克隆 https://github.com/spring-guides/gs-batch-processing.git
cd到 gs-batch-processing /初始
提前跳 创建一个业务类 。
当你完成 ,您可以检查您的结果代码 gs-batch-processing /完成 。

设置项目

首先建立了一个基本的构建脚本。 您可以使用您喜欢的任何构建系统在构建应用程序时,春天,但您需要使用的代码 Gradle 和 Maven 包括在这里。 如果你不熟悉,请参考 与它建立Java项目 或 与Maven构建Java项目 。

创建目录结构

你选择的项目目录,创建以下目录结构;例如,使用 mkdir - p src /主/ java /你好 在* nix系统:

└── src
    └── main
        └── java
            └── hello
创建一个Gradle构建文件

下面是 初始Gradle构建文件 。 但是你也可以使用Maven。 砰的一声。 xml文件包含 在这里 。 如果您使用的是 弹簧工具套件(STS) ,您可以直接导入向导。

如果你看看 pom.xml ,你会发现它有一个特定的版本的 maven-compiler-plugin 。 这是 不 一般推荐。 相反,它是为了解决一个问题与我们的CI系统默认一个非常古老的(pre-Java5)版本的这个插件。
build.gradle

buildscript {
    repositories {
        maven { url "http://repo.spring.io/libs-release" }
        mavenLocal()
        mavenCentral()
    }
    dependencies {
        classpath("org.springframework.boot:spring-boot-gradle-plugin:1.1.5.RELEASE")
    }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'idea'
apply plugin: 'spring-boot'

jar {
    baseName = 'gs-batch-processing'
    version =  '0.1.0'
}

repositories {
    mavenLocal()
    mavenCentral()
    maven { url "http://repo.spring.io/libs-release" }
}

dependencies {
    compile("org.springframework.boot:spring-boot-starter-batch")
    compile("org.hsqldb:hsqldb")
    testCompile("junit:junit")
}

task wrapper(type: Wrapper) {
    gradleVersion = '1.11'
}
的 弹簧引导gradle插件 提供了很多方便的特点:

它收集的所有jar文件的类路径中,构建一个单一的、可运行的“uber-jar”,这使得它更方便执行和运输服务。
它搜索 公共静态void main() 标志作为一个可运行的类方法。
它提供了一个内置的依赖项解析器,设置版本号相匹配 弹簧引导依赖性 。 你希望你可以覆盖任何版本,但它将默认启动的选择的版本。
通常客户或业务分析师供应电子表格。 在这种情况下,您弥补这个缺点。

src / main /资源/ sample-data.csv

Jill,Doe
Joe,Doe
Justin,Doe
Jane,Doe
John,Doe
这个表格包含一个姓氏和名字在每一行,由一个逗号分开。 这是一种相当常见的模式,春天处理开箱即用的,正如您将看到的。

接下来,编写一个SQL脚本创建一个表来存储数据。

src / main /资源/ schema-all.sql

DROP TABLE people IF EXISTS;

CREATE TABLE people  (
    person_id BIGINT IDENTITY NOT NULL PRIMARY KEY,
    first_name VARCHAR(20),
    last_name VARCHAR(20)
);
弹簧启动运行 模式-@@platform@@.sql 自动启动。 - 是默认为所有平台。
创建一个业务类

现在你看到数据输入和输出的格式,您编写代码来表示一行数据。

主要/ src / java / hello /将位于
--
package hello;

public class Person {
    private String lastName;
    private String firstName;

    public Person() {

    }

    public Person(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    public void setFirstName(String firstName) {
        this.firstName = firstName;
    }

    public String getFirstName() {
        return firstName;
    }

    public String getLastName() {
        return lastName;
    }

    public void setLastName(String lastName) {
        this.lastName = lastName;
    }

    @Override
    public String toString() {
        return "firstName: " + firstName + ", lastName: " + lastName;
    }

}
--

您可以实例化 人 类通过第一个和最后一个名字一个构造函数,或通过设置属性。

创建一个中间处理器

批处理中的常见模式是摄取数据,转换,然后管别的地方。 在这里你写一个简单的变压器,将名称转换为大写。

主要/ src / java / hello / PersonItemProcessor.java
--
package hello;

import org.springframework.batch.item.ItemProcessor;

public class PersonItemProcessor implements ItemProcessor<Person, Person> {

    @Override
    public Person process(final Person person) throws Exception {
        final String firstName = person.getFirstName().toUpperCase();
        final String lastName = person.getLastName().toUpperCase();

        final Person transformedPerson = new Person(firstName, lastName);

        System.out.println("Converting (" + person + ") into (" + transformedPerson + ")");

        return transformedPerson;
    }

}
--

PersonItemProcessor 实现Spring Batch的 ItemProcessor 接口。 这使它容易线为一个批处理作业,您定义的代码进一步在本指南。 根据接口,你接收传入的 人 你对象,然后将其转换为大写 人 。

没有要求输入和输出类型是相同的。 事实上,一个源的数据被读取后,有时应用程序的数据流需要一个不同的数据类型。
批处理作业

现在你实际的批处理作业。 Spring Batch提供了许多实用工具类,减少需要编写自定义代码。 相反,您可以专注于业务逻辑。

主要/ src / java / hello / BatchConfiguration.java
--
package hello;

import javax.sql.DataSource;

import org.springframework.batch.core.Job;
import org.springframework.batch.core.Step;
import org.springframework.batch.core.configuration.annotation.EnableBatchProcessing;
import org.springframework.batch.core.configuration.annotation.JobBuilderFactory;
import org.springframework.batch.core.configuration.annotation.StepBuilderFactory;
import org.springframework.batch.core.launch.support.RunIdIncrementer;
import org.springframework.batch.item.ItemProcessor;
import org.springframework.batch.item.ItemReader;
import org.springframework.batch.item.ItemWriter;
import org.springframework.batch.item.database.BeanPropertyItemSqlParameterSourceProvider;
import org.springframework.batch.item.database.JdbcBatchItemWriter;
import org.springframework.batch.item.file.FlatFileItemReader;
import org.springframework.batch.item.file.mapping.BeanWrapperFieldSetMapper;
import org.springframework.batch.item.file.mapping.DefaultLineMapper;
import org.springframework.batch.item.file.transform.DelimitedLineTokenizer;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.core.io.ClassPathResource;
import org.springframework.jdbc.core.JdbcTemplate;

@Configuration
@EnableBatchProcessing
public class BatchConfiguration {

    // tag::readerwriterprocessor[]
    @Bean
    public ItemReader<Person> reader() {
        FlatFileItemReader<Person> reader = new FlatFileItemReader<Person>();
        reader.setResource(new ClassPathResource("sample-data.csv"));
        reader.setLineMapper(new DefaultLineMapper<Person>() {{
            setLineTokenizer(new DelimitedLineTokenizer() {{
                setNames(new String[] { "firstName", "lastName" });
            }});
            setFieldSetMapper(new BeanWrapperFieldSetMapper<Person>() {{
                setTargetType(Person.class);
            }});
        }});
        return reader;
    }

    @Bean
    public ItemProcessor<Person, Person> processor() {
        return new PersonItemProcessor();
    }

    @Bean
    public ItemWriter<Person> writer(DataSource dataSource) {
        JdbcBatchItemWriter<Person> writer = new JdbcBatchItemWriter<Person>();
        writer.setItemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider<Person>());
        writer.setSql("INSERT INTO people (first_name, last_name) VALUES (:firstName, :lastName)");
        writer.setDataSource(dataSource);
        return writer;
    }
    // end::readerwriterprocessor[]

    // tag::jobstep[]
    @Bean
    public Job importUserJob(JobBuilderFactory jobs, Step s1) {
        return jobs.get("importUserJob")
                .incrementer(new RunIdIncrementer())
                .flow(s1)
                .end()
                .build();
    }

    @Bean
    public Step step1(StepBuilderFactory stepBuilderFactory, ItemReader<Person> reader,
            ItemWriter<Person> writer, ItemProcessor<Person, Person> processor) {
        return stepBuilderFactory.get("step1")
                .<Person, Person> chunk(10)
                .reader(reader)
                .processor(processor)
                .writer(writer)
                .build();
    }
    // end::jobstep[]

    @Bean
    public JdbcTemplate jdbcTemplate(DataSource dataSource) {
        return new JdbcTemplate(dataSource);
    }

}
--

首先, @EnableBatchProcessing 注释添加了许多关键的bean支持工作,可以为您节省很多腿的工作。 这个例子使用一个基于内存的数据库(所提供的 @EnableBatchProcessing ),也就是说,当它结束的时候,数据就丢失了。

将其分解:

主要/ src / java / hello / BatchConfiguration.java
--
    @Bean
    public ItemReader<Person> reader() {
        FlatFileItemReader<Person> reader = new FlatFileItemReader<Person>();
        reader.setResource(new ClassPathResource("sample-data.csv"));
        reader.setLineMapper(new DefaultLineMapper<Person>() {{
            setLineTokenizer(new DelimitedLineTokenizer() {{
                setNames(new String[] { "firstName", "lastName" });
            }});
            setFieldSetMapper(new BeanWrapperFieldSetMapper<Person>() {{
                setTargetType(Person.class);
            }});
        }});
        return reader;
    }

    @Bean
    public ItemProcessor<Person, Person> processor() {
        return new PersonItemProcessor();
    }

    @Bean
    public ItemWriter<Person> writer(DataSource dataSource) {
        JdbcBatchItemWriter<Person> writer = new JdbcBatchItemWriter<Person>();
        writer.setItemSqlParameterSourceProvider(new BeanPropertyItemSqlParameterSourceProvider<Person>());
        writer.setSql("INSERT INTO people (first_name, last_name) VALUES (:firstName, :lastName)");
        writer.setDataSource(dataSource);
        return writer;
    }
--

。 第一块代码定义了输入,处理器和输出。 - - - - - - 读者() 创建一个 ItemReader 。 查找一个文件 sample-data.csv 并解析每一行项目有足够的信息来把它变成一个 人 。 - - - - - - 处理器() 我们创建了一个实例 PersonItemProcessor 你前面定义,为了大写数据。 - - - - - - 写(数据源) 创建一个 ItemWriter 。 这是针对JDBC目的地并自动创建的数据源的一个副本 @EnableBatchProcessing 。 它包括所需的SQL语句插入一个 人 由Java bean属性。

下一个块关注实际的配置工作。

主要/ src / java / hello / BatchConfiguration.java

--
    @Bean
    public Job importUserJob(JobBuilderFactory jobs, Step s1) {
        return jobs.get("importUserJob")
                .incrementer(new RunIdIncrementer())
                .flow(s1)
                .end()
                .build();
    }

    @Bean
    public Step step1(StepBuilderFactory stepBuilderFactory, ItemReader<Person> reader,
            ItemWriter<Person> writer, ItemProcessor<Person, Person> processor) {
        return stepBuilderFactory.get("step1")
                .<Person, Person> chunk(10)
                .reader(reader)
                .processor(processor)
                .writer(writer)
                .build();
    }
--

。 第一个方法定义了工作,第二个定义了一个单一的步骤。 工作是由步骤,每个步骤可以包括一个读者,一个处理器,和一位作家。

在这个工作定义,您需要一个增量器因为工作使用一个数据库维护执行状态。 然后列出每一步,这个工作只有一个步骤。 工作结束后,和Java API产生一个完全配置工作。

你的步骤定义,定义多少数据写一次。 在这种情况下,它写到十记录一次。 接下来,配置读者、处理器和作家从早些时候使用注入的部分。

块()是前缀 < >的人,人 因为它是一个通用的方法。 这是每个“块”的输入和输出类型的处理,和线条 ItemReader <人> 和 ItemWriter <人> 。
使应用程序可执行文件

尽管批处理可以嵌入在web应用程序WAR文件,以下简单的方法证明了创建一个独立的应用程序。 你包所有在一个单一的、可执行的JAR文件,由一个好的旧Java main() 方法。

主要/ src / java / hello / Application.java
--
package hello;

import java.sql.ResultSet;
import java.sql.SQLException;
import java.util.List;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.EnableAutoConfiguration;
import org.springframework.context.ApplicationContext;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.jdbc.core.JdbcTemplate;
import org.springframework.jdbc.core.RowMapper;

@ComponentScan
@EnableAutoConfiguration
public class Application {

    public static void main(String[] args) {
        ApplicationContext ctx = SpringApplication.run(Application.class, args);

        List<Person> results = ctx.getBean(JdbcTemplate.class).query("SELECT first_name, last_name FROM people", new RowMapper<Person>() {
            @Override
            public Person mapRow(ResultSet rs, int row) throws SQLException {
                return new Person(rs.getString(1), rs.getString(2));
            }
        });

        for (Person person : results) {
            System.out.println("Found <" + person + "> in the database.");
        }
    }

}
--

的 main() 方法推迟到 SpringApplication 助手类,提供 Application.class 其作为一个参数 run() 方法。 这告诉春读元数据注释 应用程序 和管理它的组件 Spring应用程序上下文 。

的 @ComponentScan 递归搜索通过注释告诉春天 你好 包和它的孩子类直接或间接与春天的标志 @ component 注释。 这个指令确保弹簧发现和寄存器 BatchConfiguration ,因为它是显著的 @Configuration ,这是一种 @ component 注释。

的 @EnableAutoConfiguration 注释交换机根据内容合理的默认行为的类路径中。 例如,它看起来对任何类实现 CommandLineRunner 接口和调用它 run() 方法。 在这种情况下,本指南的演示代码。

出于演示目的,有代码来创建一个 jdbctemplate 查询数据库,并打印的名字人批处理作业插入。

构建一个可执行JAR

您可以构建一个可执行的JAR文件,其中包含所有必需的依赖关系,类和资源。 这使它容易船、版本和服务作为应用程序部署在整个开发生命周期,在不同的环境,等等。

./gradlew build
然后您可以运行JAR文件:

java -jar build/libs/gs-batch-processing-0.1.0.jar
如果你是使用Maven,您可以运行应用程序使用 运行mvn spring-boot: 。 或者您可以构建JAR文件 mvn清洁包 和运行JAR通过键入:

java -jar target/gs-batch-processing-0.1.0.jar
上面的程序将创建一个可运行JAR。 你也可以选择 构建一个典型的WAR文件 来代替。
运行批处理作业

如果您正在使用它,您可以在命令行运行批处理作业:

./gradlew clean build && java -jar build/libs/gs-batch-processing-0.1.0.jar
如果你是使用Maven,您可以运行你的批处理作业类型 mvn清洁包& & java / gs-batch-processing-0.1.0.jar -jar目标 。
你可以直接从Gradle或者运行应用程序是这样的:

./gradlew bootRun
您可以运行mvn 运行mvn spring-boot: 。
每个人的工作打印一行被改变了。 工作运行结束后,您还可以看到从数据库查询的输出。

Converting (firstName: Jill, lastName: Doe) into (firstName: JILL, lastName: DOE)
Converting (firstName: Joe, lastName: Doe) into (firstName: JOE, lastName: DOE)
Converting (firstName: Justin, lastName: Doe) into (firstName: JUSTIN, lastName: DOE)
Converting (firstName: Jane, lastName: Doe) into (firstName: JANE, lastName: DOE)
Converting (firstName: John, lastName: Doe) into (firstName: JOHN, lastName: DOE)
Found <firstName: JILL, lastName: DOE> in the database.
Found <firstName: JOE, lastName: DOE> in the database.
Found <firstName: JUSTIN, lastName: DOE> in the database.
Found <firstName: JANE, lastName: DOE> in the database.
Found <firstName: JOHN, lastName: DOE> in the database.
总结

恭喜你! 你建立了一个批处理作业,摄入来自一个电子表格的数据,处理它,并写了一个数据库。



