# 配置Step

正如在[Batch Domain Language][0]中叙述的，Step是一个独立封装域对象，包含了所有定义和控制实际处理信息批任务的序列。这是一个比较抽象的描述，因为任意一个Step的内容都是开发者自己编写的Job。一个Step的简单或复杂取决于开发者的意愿。一个简单的Step也许是从本地文件读取数据存入数据库，写很少或基本无需写代码。一个复杂的Step也许有复杂的业务规则（取决于所实现的方式），并作为整个个流程的一部分。

![step](./step.png)

[0]:http://docs.spring.io/spring-batch/trunk/reference/html/domain.html
