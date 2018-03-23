###11.线程池
先看一个简单的实例

	public class TestThreadPool {
		public static void main(String[] args) {
			ThreadPoolDemo td = new ThreadPoolDemo();
			new Thread(td).start();
			new Thread(td).start();
			new Thread(td).start();
		}
	}
	class ThreadPoolDemo implements Runnable{
		private int i = 0;
		@Override
		public void run() {
			while (i<= 100) {
				System.out.println(Thread.currentThread().getName());
			}
			
		}
	}
上面我们就是创建和销毁了三个线程实例，这样不断的创建和销毁，就会消耗资源，于是就出现了线程池，和数据库连接池一样，就是有许多的线程已经创建好了，我们直接使用就好。
线程池：提供了一个线程队列，队列中保存着所有等待状态的线程，避免了创建与销毁额外的开销，提高了响应的性能。

####线程池的体系结构
java.util.concurrent.Executor:负责线程的使用与调度的根接口
![](https://i.imgur.com/7EJzrLI.png)
一个 ExecutorService，它使用可能的几个池线程之一执行每个提交的任务，通常使用 Executors 工厂方法配置。

线程池可以解决两个不同问题：由于减少了每个任务调用的开销，它们通常可以在执行大量异步任务时提供增强的性能，并且还可以提供绑定和管理资源（包括执行任务集时使用的线程）的方法。每个 ThreadPoolExecutor 还维护着一些基本的统计数据，如完成的任务数。
####工具类Executor
1. ExecutorService	newFixedThreadPool(int nThreads) 创建一个可重用固定线程数的线程池，以共享的无界队列方式来运行这些线程。
2. ExecutorService	newCachedThreadPool()  创建一个可根据需要创建新线程的线程池
3. static ExecutorService	newSingleThreadExecutor() 创建一个使用单个 worker 线程的 Executor
4. static ScheduledExecutorService	newScheduledThreadPool(int corePoolSize) 创建一个线程池，它可安排在给定延迟后运行命令或者定期地执行。

线程池的使用方法如下：

	public class TestThreadPool {
		public static void main(String[] args) {
			// 1.创建线程池
			ExecutorService pool = Executors.newFixedThreadPool(5);//创建了5个线程的线程池
			ThreadPoolDemo td = new ThreadPoolDemo();
			// 2.为线程池中的线程分配任务
			for (int i = 0; i < 10; i++) {
				pool.submit(td);
			}
			
			// 3.关闭线程池
			pool.shutdown();
		}
	}
	class ThreadPoolDemo implements Runnable{
		private int i = 0;
		@Override
		public void run() {
			while (i<= 5) {
				System.out.println(Thread.currentThread().getName()+":"+i++);
			}
			
		}
	}
####使用Callable创建线程的方式

	public class TestThreadPool {
		public static void main(String[] args) throws InterruptedException, ExecutionException {
			// 1.创建线程池
			ExecutorService pool = Executors.newFixedThreadPool(5);//创建了5个线程的线程池
			List<Future<Integer>> list = new ArrayList<Future<Integer>>();
			for (int i = 0; i < 10; i++) {
				Future<Integer> future = pool.submit(new Callable<Integer>() {
					@Override
					public Integer call() throws Exception {
						int sum = 0;
						for (int i = 0; i < 100; i++) {
							sum +=i;
						}
						return sum;
					}
				});
				list.add(future);
			}
			// 3.关闭线程池
			pool.shutdown();
			for (Future<Integer> future: list) {
				System.out.println(future.get());
			}
		}
	}

###12.线程调度
在线程池中提到了一个ScheduledExecutorService这一个类就是用来实现线程的调度的。

	public class TestScheduledThread {
		public static void main(String[] args) throws InterruptedException, ExecutionException {
			ScheduledExecutorService pool = Executors.newScheduledThreadPool(5);// 创建五个线程的线程池
			for (int i = 0; i < 5; i++) {
				ScheduledFuture<Integer> result = pool.schedule(new Callable<Integer>() {
					@Override
					public Integer call() throws Exception {
						int num = new Random().nextInt(100);//产生随机数
						System.out.println(Thread.currentThread().getName()+":"+num);
						return num;
					}
				}, 3, TimeUnit.SECONDS);
				System.out.println(result.get());
			}
			pool.shutdown();
			
		}
	}
###13.Fork/Join框架
Fork/Join框架就是在必要的情况下，将一个大任务，进行拆分（fork）成若干个小任务（拆到不可再拆为止，即临界值），再将一个个的小任务运算的结果进行join汇总，这里要说明的是拆分和合并也需要时间，例如求100的累加和，可以使用for循环直接累加，也可以使用Fork/Join,但是执行时间反而是for循环要快的多，如果要计算100000000就是Fork/Join要快的多，所以包括临界值的设定都要不停的测试得出，下面给出一个实例吧。
	
	public class TestForkJoinPool {
		public static void main(String[] args) {
			ForkJoinPool pool = new ForkJoinPool();
			ForkJoinTask<Long> task = new ForkJoinSum(0L, 1000000L);
			long sum = pool.invoke(task);
			System.out.println(sum);
		}
	
	}
	class ForkJoinSum extends RecursiveTask<Long>{
	
		private static final long serialVersionUID = 1L;
		
		private long start;
		private long end;
		private static final long THURSHOLD = 10000L;// 临界值
		
		public ForkJoinSum(long start,long end) {
			this.start = start;
			this.end = end;
		}
	
		@Override
		protected Long compute() {
			long length = end -start;
			if(length <= THURSHOLD){
				long sum = 0L;
				for(long i = start; i<= end;i++){
					sum +=i;
				}
				return sum;
			}else{
				long middle = (end+start) / 2;
				ForkJoinSum left = new ForkJoinSum(start, middle);//递归操作
				left.fork();// 进行拆分，同时压入线程队列
				
				ForkJoinSum right = new ForkJoinSum(middle+1, end);
				right.fork();
				return left.join()+right.join();
			}
		}
	}

