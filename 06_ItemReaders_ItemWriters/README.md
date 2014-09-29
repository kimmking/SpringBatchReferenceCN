#ItemReaders和ItemWriters#


所有的批处理都可以描述为最简单的形式： 读取大量的数据, 执行某种类型的计算/转换, 以及写出执行结果. Spring Batch 提供了三个主要接口来辅助执行大量的读取与写出: **ItemReader**, **ItemProcessor** 和 **ItemWriter**.