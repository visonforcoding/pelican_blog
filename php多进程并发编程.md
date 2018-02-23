Title: php多进程并发编程
Date: 2018-02-02 09:59:16
Category: php
keywords: php多进程 pcntl

> PHP从一出生就被设计用来快速开发WEB应用，这也注定了它在某些方面的先天不足，例如在cli环境下处理大量数据的情况，或者在并发编程方面，都显得力不从心。但基于LINUX的PHP扩展PCNTL却可以提供多进程编程。

## fork

FORK编程的大概原理是，每次调用fork函数，操作系统就会产生一个子进程，**儿子进程所有的堆栈信息都是原封不动复制父进程的**，而在fork之后，父进程与子进程实际上是相互独立的，父子进程不会相互影响。也就是说，fork调用位置之前的所有变量，父进程和子进程是一样的，但fork之后则取决于各自的动作，且数据也是独立的；


#例子

```php
<?php

class Process
{

	/**
	 * 创建多进程任务
	 * @param callable $fn  子进程执行方法
	 * @param mixed $param  参数列表
	 */
	public static function fork(callable $fn, ...$param)
	{
		$pid = pcntl_fork();
		if ($pid == -1) {
            //创建子进程失败
			die('创建子进程失败');
		} else if ($pid) {
            //父进程的到子进程号 父进程逻辑
			println(ColorOut::red('创建子进程:' . $pid));
                //			pcntl_wait($status); //等待子进程中断，防止子进程成为僵尸进程。
                //			self::waitChildCompleted();
		} else {
            //子进程逻辑
			call_user_func_array($fn, $param);
			exit(0);
		}
	}

	public static function waitChildCompleted()
	{
		while (($pid = pcntl_waitpid(0, $status)) != -1) {
			$status = pcntl_wexitstatus($status);
			println(ColorOut::red("子进程$pid 完成 状态 $status"));
		}
	}

}

```
