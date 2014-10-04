# 配置并运行Job

在[上一章节(domain section)](../03_domain_language/README.md)，即批处理的域语言中,讨论了整体的架构设计，并使用如下关系图来进行表示：

![Spring Batch参考模型](./spring-batch-reference-model.png) 

虽然Job对象看上去像是对于多个Step的一个简单容器，但是开发者必须要注意许多配置项。此外，Job的运行以及Job运行过程中元数据如何被保存也是需要考虑的。本章将会介绍Job在运行时所需要注意的各种配置项。
