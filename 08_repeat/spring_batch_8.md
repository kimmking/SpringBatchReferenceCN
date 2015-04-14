#8.Repeat#

#8.1 RepeatTemplate#

批处理是重复的动作-无论是作为一个简单的优化，或作为工作的一部分。策划和归纳重复以及提供一个相当于迭代器的框架，Spring Batch提供RepeatOperations接口，这个RepeatOperations接口看起来像是这样：


	public interface RepeatOperations {

   	 RepeatStatus iterate(RepeatCallback callback) throws RepeatException;

	}

上面代码中的callback是一个简单的接口,允许您插入一些重复的业务逻辑：

	public interface RepeatCallback {

    	RepeatStatus doInIteration(RepeatContext context) throws Exception;

	}

上面代码中的callback是反复执行的，直到执行决定的迭代应该结束。这些接口的返回值是一个枚举，可以是repeatstatus.continuable或repeatstatus.finished二者其中任何一个。
一个RepeatStatus传达信息给重复操作的调用者，是否有更多的工作要做。
一般来说，实现repeatoperations应检查repeatstatus并使用它作为决定结束迭代的一部分。
任何callback都希望发出信号给调用者，如果没有更多的工作要做，可以返回repeatstatus.finished
最简单的repeatoperations通用实现repeattemplate。它可以这样使用：

	RepeatTemplate template = new RepeatTemplate();

	template.setCompletionPolicy(new FixedChunkSizeCompletionPolicy(2));

	template.iterate(new RepeatCallback() {

    	public ExitStatus doInIteration(RepeatContext context) {
      	  // Do stuff in batch...
    	  return ExitStatus.CONTINUABLE;
    	}

	});

在这个例子中我们返回repeatstatus.continuable表明这儿还有更多的工作要做。该callback也可以返回exitstatus.finished信号给调用者，如果这儿没有更多的工作要做。

8.1.1 RepeatContext
===================
上文提到的repeatcallback方法的参数是一个repeatcontext。许多callback会简单地忽略上下文，但必要时它可以作为属性包，存储临时数据迭代的持续时间。在迭代方法返回后，上下文将不再存在。
如果在一个嵌套迭代过程，RepeatContext将有一个父上下文。该父上下文是偶尔用于存储数据,需要调用迭代之间共享。
例如这种情况：如果你想计算一个事件发生迭代的次数和记住它后续调用。

#8.1.2 RepeatStatus#

RepeatStatus是一个枚举，Spring Batch中用表明是否处理完成。这些都是RepeatStatus可能的值:

<font size="3" color="black">Table 8.1. ExitStatus Properties</font>
<table>
	<tr>
		<td>值</td><td>描述</td>
	</tr>
	<tr>
		<td>CONTINUABLE</td><td>这儿还有很多工作要做.</td>
	</tr>					
	<tr>
		<td>FINISHED</td><td>没有更多的重复发生.</td>
	</tr>			
</table>

RepeatStatus值也可以调用RepeatStatus中的and()方法来做逻辑与操作.效果就是和一个可持续的标志位做逻辑与操作.换句话说,如果状态是FINISHED,那么结果必须是FINISHED的

#8.2 结束策略#

CompletionPolicy可以中止RepeatTemplate内部的循环迭代，CompletionPolicy同样也是一个RepeatContext工厂。RepeatTemplate有责任使用当前的策略来创建一个RepeatContext并且在迭代时的每一个阶段传递RepeatCallback。如果迭代已经完成，在doInIteration的回调完成之后,RepeatTemplate必须通知CompletionPolicy来更新自己的状态（状态存储在RepeatContext中）.
Spring Batch的CompletionPolicy提供了一些简单的通用实现。SimpleCompletionPolicy只允许执行固定的次数(任何时候RepeatStatus.FINISHED强制的提早完成)。
用户可能需要自己完成实现更复杂的决定策略。例如,一个批处理窗口,在线系统需要使用一个定制的策略阻止批任务执行一次。

#8.3 异常处理#

如果RepeatCallback内部抛出一个异常。RepeatCallback通过ExceptionHandler决定是否再抛出这个异常。

	public interface ExceptionHandler {

   		void handleException(RepeatContext context, Throwable throwable)
        throws RuntimeException;

	}
	
一个常见的用例是计算指定类型异常的数量,并到达限制，为此Spring Batch提供SimpleLimitExceptionHandler和稍微更灵活的RethrowOnThresholdExceptionHandler。RethrowOnThresholdExceptionHandler有limit属性和异常类型，包括当前异常类型及其所有子类提供的类型。其他类型正常被抛出，指定的异常类型会被忽略，直到达到数量限制时抛出。
SimpleLimitExceptionHandler一个重要可选择的属性useparent该属性为boolean类型。它默认为false，仅限制当前的RepeatContext。设置为true时，限制保持在兄弟上下文的嵌套迭代中（例如 一个块内的一步）

#8.4 监听#

不同的迭代往往能够在的横切点获得有用的回调。为了这个目的，Spring Batch提供repeatlistener接口。RepeatTemplate允许用户注册RepeatListeners,监听器会回调RepeatContext and RepeatStatus，这两个参数在整个迭代过程中都可用。
这个interface看起来像是这样：

	public interface RepeatListener {
   	 	void before(RepeatContext context);
    	void after(RepeatContext context, RepeatStatus result);
    	void open(RepeatContext context);
    	void onError(RepeatContext context, Throwable e);
    	void close(RepeatContext context);
	}

打开和关闭回调之前和之后整个迭代。之前,之后和onError适用于单独RepeatCallback调用。

值得注意的是，当有一个以上的监听，他们是在一个列表，所以有一个顺序。


#8.5 并行处理#

RepeatOperations的实现不限于顺序执行的回调。一些实现能够并行执行的回调，这是很重要的。为此,Spring Batch提供TaskExecutorRepeatTemplate，它使用Spring TaskExecutor 策略运行RepeatCallback。默认使用SynchronousTaskExecutor，整个迭代执行的影响是在同一线程中（和一个正常的repeattemplate相同）

#8.6 声明式迭代#

S有时会有一些业务处理,你想知道每次重复产生的结果。典型的例子就是消息的优化管道——这是更有效地处理一批消息,如果他们频繁到来,超过每条消息单独的事务的成本。为了这个目的，Spring Batch AOP提供了一个拦截器,封装RepeatOperations方法的调用。RepeatOperationsInterceptor执行拦截方法和重复的RepeatTemplate.CompletionPolicy。

下面是一个示例使用Spring AOP命名空间的声明式迭代重复服务调用一个名为processMessage的方法（如何配置AOP拦截器更多细节见Spring用户指南）:

	<aop:config>
    	<aop:pointcut id="transactional"
        	expression="execution(* com..*Service.processMessage(..))" />
    	<aop:advisor pointcut-ref="transactional"
        	advice-ref="retryAdvice" order="-1"/>
		</aop:config>

	<bean id="retryAdvice" class="org.spr...RepeatOperationsInterceptor"/>


上面的例子使用了默认的repeattemplate在拦截。要改变政策，监听器等。你只需要为拦截器注入repeattemplate实例。

如果拦截方法返回void那么拦截器总是返回ExitStatus.CONTINUABLE（因此这儿有个危险如果CompletionPolicy不含有一个有限的结束点会形成一个无限循环）。　否则返回ExitStatus.CONTINUABLE直到拦截方法的返回值为空,此时它返回ExitStatus.FINISHED。所以目标方法内的业务逻辑可以发出信号,没有更多的工作要做返回null,或RepeatTemplate.ExceptionHandler重新抛出异常。
