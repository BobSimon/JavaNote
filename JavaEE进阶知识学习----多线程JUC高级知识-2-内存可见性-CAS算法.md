###1.Java JUC简介
在java5.0之后提供了一个java.util.concurrent包（简称JUC），此包中增加了很多在并发编程中常用的工具类，用于定义类似于线程的自定义系统，包括线程池，异步IO等等。

###2.volatile关键字-内存可见性
内存可见性问题：当多个线程同时操作共享数据时，彼此不可见。图解如下：
![](https://i.imgur.com/qIgmGqb.png)
当主存有一个flag数据时，线程1负责修改flag的值，Main线程负责读取flag的值，线程1修改值之前，先将flag= false取到自己手中，然后再修改其值，当线程1还没将修改好的值放进主存中，Main线程已经从主存读到了flag= false，然后main线程就执行自己的逻辑，这就是内存可见性的问题，就是说Main线程不知道其他线程执行结束没有。
代码说明如下：

	public class TerstVolatile {
		public static void main(String[] args) {
			ThreadDemo td = new ThreadDemo();
			new Thread(td).start();
			while (true) {
				if(td.isFlag()){
					System.out.println("-------------------");
					break;
				}
				
			}
		}
	
	}
	class ThreadDemo implements Runnable{
		private boolean flag = false;
	
		@Override
		public void run() {
			try {
				Thread.sleep(200);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			flag = true;
			System.out.println("flaf="+isFlag());
			
		}
	
		public boolean isFlag() {
			return flag;
		}
	
		public void setFlag(boolean flag) {
			this.flag = flag;
		}
	}
解决办法之一：使用synchronize关键字加锁

	public class TerstVolatile {
		public static void main(String[] args) {
			ThreadDemo td = new ThreadDemo();
			new Thread(td).start();
			while (true) {
				synchronized(td){
					if(td.isFlag()){
						System.out.println("-------------------");
						break;
					}
				}
			}
		}
	}
但是加锁会导致效率变低，解决办法之二使用volatile关键字，如下图：
![](https://i.imgur.com/R3lh3tT.png)
使用了volatile关键字就是线程直接操作主存中的数据。在保证数据的同步性，效率也得以提升。volatile相较于synchronize是一种较为轻量的同步策略。

	private volatile boolean flag = false;
注意：
1. volatile不具备互斥性，互斥性就是说如果多个线程共同抢一把锁的时候，如果其中一个线程抢到锁，那么其他线程就只能等待，volatile就是多个线程共同在主存中完成。
2. volatile不能变量的原子性，

有关原子性的问题，我们看一个i++的实例

	public class TestAtomicDemo {
		public static void main(String[] args) {
			AtomicDemo ad = new AtomicDemo();
			for (int i = 0; i < 10; i++) {
				new Thread(ad).start();
			}
		}
	}
	class AtomicDemo implements Runnable{
		private int serialNumber = 0;
		@Override
		public void run() {
			try {
				Thread.sleep(200);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println(Thread.currentThread().getName()+":"+getSerialNumber());
		}
		public int getSerialNumber(){
			return serialNumber++;
		}
		
	}
结果为：

	Thread-1:0
	Thread-0:1
	Thread-8:2
	Thread-9:5
	Thread-6:7
	Thread-5:3
	Thread-3:3
	Thread-2:4
	Thread-4:8
	Thread-7:6
说明：
1. i++在程序底层其实是三步走，读-改-写，本来是不可分割的操作，但是在多个线程访问时，一个线程还没写成功，另外一个线程也开始写了，这也就是线程安全问题。
2. 使用volatile也不能解决这个问题，因为volatile是让多个线程在内存中共同操作共享数据，i++的操作也会被分割成三步。
3. 解决办法就是使用原子变量。

###3.原子变量-CAS算法
jdk1.5之后java.concurrent.atomic包下提供了常用的原子变量，原子变量使用volatile保证内存可见性，使用CAS算法保证数据的原子性。CAS算法是硬件对于并发操作共享数据的支持，CAS包含了三个操作数，分别为：内存值V,预估值A,更新值B,当且仅当V==A,V=B,否则，不做任何操作。
####java.concurrent.atomic包下的类结构
![](https://i.imgur.com/mYKRSs5.png)
####AtomicInteger类的方法如下，其他类的参考API
#####1. int	addAndGet(int delta) ;以原子方式将给定值与当前值相加。
#####2. boolean	compareAndSet(int expect, int update) ;如果当前值 == 预期值，则以原子方式将该值设置为给定的更新值。
#####3. int	decrementAndGet() ;以原子方式将当前值减 1。
#####4. double	doubleValue() ; 以 double 形式返回指定的数值。
#####5. float	floatValue() ;以 float 形式返回指定的数值。
#####6. int	get() ;获取当前值。
#####7. int	getAndAdd(int delta) ;以原子方式将给定值与当前值相加。
#####8. int	getAndDecrement() ;以原子方式将当前值减 1。
#####9. int	getAndIncrement() ;以原子方式将当前值加 1。
#####10. int	getAndSet(int newValue) ; 以原子方式设置为给定值，并返回旧值。
#####11. int	incrementAndGet(); 以原子方式将当前值加 1。
#####12. int	intValue() ;以 int 形式返回指定的数值。
#####13. void	lazySet(int newValue) ;最后设置为给定值。
#####14. long	longValue() ;以 long 形式返回指定的数值。
#####15. void	set(int newValue) ;设置为给定值。
#####16. String	toString() ;返回当前值的字符串表示形式。
#####17. boolean	weakCompareAndSet(int expect, int update) ;如果当前值 == 预期值，则以原子方式将该设置为给定的更新值。
使用原子变量改写上述程序如下：

	class AtomicDemo implements Runnable{
		private AtomicInteger serialNumber = new AtomicInteger();
		@Override
		public void run() {
			try {
				Thread.sleep(200);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println(Thread.currentThread().getName()+":"+getSerialNumber());
		}
		public int getSerialNumber(){
			return serialNumber.getAndIncrement();
		}
	}


###4.ConcurrentHashMap锁分段机制
ConcurrentHashMap同步容器类是java5增加的一个线程安全的哈希表，对于多线程的操作，介于HashMap和HashTable之间，内部采用锁分段的机制替代HashTable的独占锁，进而提高了性能。包结构如下：
![](https://i.imgur.com/W8aNvO0.png)
当期望许多线程访问一个给定Collection时，ConcurrentHashMap通常优于HashMap，当期望的读数和遍历远远大于列表的更新读数时CopyOnWriteArrayList优于ArrayList。
ConcurrentHashMap锁分段机制图解如下：
![](https://i.imgur.com/R7gS1YA.png)
 CopyOnWriteArrayList实例如下：如果我们不使用 CopyOnWriteArrayList，使用 Collections.synchronizedList运行下面程序就会报并发修改异常。

	public class TestCopyOnWriteArrayList {
		public static void main(String[] args) {
			HelloThread ht = new HelloThread();
			for (int i = 0; i < 10; i++) {
				new Thread(ht).start();
			}
		}
	}
	class HelloThread implements Runnable{
		private static List<String> list = Collections.synchronizedList(new ArrayList<String>());
		static{
			list.add("AA");
			list.add("BB");
			list.add("CC");
		}
		@Override
		public void run() {
			Iterator<String> iterator = list.iterator();
			while (iterator.hasNext()) {
				System.out.println(iterator.next());
				list.add("DD");
			}
		}
	}
异常如下：

	java.util.ConcurrentModificationException
使用 CopyOnWriteArrayList修改代码如下所示：

	private static CopyOnWriteArrayList<String> list = new CopyOnWriteArrayList<String>();
说明：CopyOnWriteArrayList：写入并复制，就是每次写入的时候都会复制，如果添加操作多时，效率低，每次都会进行复制，开销很大，并发迭代操作多时可以选择。
###5.CountDownLatch闭锁
CountDownlatch闭锁，就是在完成某些运算时，只有其他所有线程的运算全部完成，当前运算才继续执行。
实例如下：如果不使用CountDownlatch闭锁就无法计算出执行10子线程所需要的时间

	public class TestCountDownlatch {
		public static void main(String[] args) {
			final CountDownLatch latch = new CountDownLatch(5);
			LatchDemo ld = new LatchDemo(latch);
			long start = System.currentTimeMillis();
			for (int i = 0; i < 10; i++) {
				new Thread(ld).start();
			}
			long end = System.currentTimeMillis();
			System.out.println("耗费时间为："+(end-start));
		}
	
	}
	class LatchDemo implements Runnable{
		private CountDownLatch latch;
		public LatchDemo(CountDownLatch latch) {
			this.latch = latch;
		}
		@Override
		public void run() {
			for (int i = 0; i < 50000; i++) {
				if(i % 2 ==0){
					System.out.println(i);
				}
			}
		}
	}
使用CountDownlatch闭锁的实例如下，可以计算出所有子线程执行需要的时间。

	public class TestCountDownlatch {
		public static void main(String[] args) {
			//5 和子线程数目一致
			final CountDownLatch latch = new CountDownLatch(5);
			LatchDemo ld = new LatchDemo(latch);
			long start = System.currentTimeMillis();
			for (int i = 0; i < 5; i++) {
				new Thread(ld).start();
			}
			//等待所有的子线程全部执行完
			try {
				latch.await();
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			long end = System.currentTimeMillis();
			System.out.println("耗费时间为："+(end-start));
		}
	
	}
	class LatchDemo implements Runnable{
		private CountDownLatch latch;
		public LatchDemo(CountDownLatch latch) {
			this.latch = latch;
		}
		@Override
		public void run() {
			synchronized(this){
				try {
					for (int i = 0; i < 50000; i++) {
						if(i % 2 ==0){
							System.out.println(i);
						}
					}
				}finally{
					latch.countDown();// 减一操作
				}
			}
			
		}
	}

###6.实现Callable接口
####创建执行线程的方式：四种方式
1. 定义Thread子类，并重写run方法，创建Thread子类实例，也就是线程对象，再调用线程对象的start方法。 new Thread子类对象.start(); 
2. A类实现Runnable接口，重写run方法，创建Runnable实现类的实例A a = new A(); new Thread(a).start();
3. 实现Callable接口，方式如下：


	public class TestCallable {
		public static void main(String[] args) {
			CallableThreadDemo ctd = new CallableThreadDemo();
			//执行Callable方法，需要FutureTask实现类的支持，用于接收运算返回的结果
			//FuterTask是Future接口
			FutureTask<Integer> result = new FutureTask<Integer>(ctd);
			new Thread(result).start();
			//接收线程的返回值,当子线程执行结束result.get()才执行，和闭锁类似
			//所以FutureTask也可以用于闭锁
			try {
				Integer sum = result.get();
				System.out.println(sum);
			} catch (InterruptedException | ExecutionException e) {
				e.printStackTrace();
			} 
		}
	}
	class CallableThreadDemo implements Callable<Integer>{
	
		@Override
		public Integer call() throws Exception {
			int sum = 0;
			for (int i = 0; i < 100; i++) {
				System.out.println(i);
				sum +=i;
			}
			return sum;
		}
	}
说明：实现Callable接口和实现Runnable接口的不同在于实现Callable接口有返回值和异常抛出。
