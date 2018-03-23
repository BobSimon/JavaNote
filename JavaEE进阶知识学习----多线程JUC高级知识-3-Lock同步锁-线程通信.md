###7.Lock同步锁
解决线程安全问题的方式，使用synchronize隐式锁，1.同步代码块，2.同步方法，3.java5之后使用同步锁Lock：显示锁，也就是说必须通过lock()方法上锁，通过unlock()方法释放锁。
实例如下：

	public class TestLock {
		public static void main(String[] args) {
			Ticket ticket = new Ticket();
			new Thread(ticket, "1号窗口").start();
			new Thread(ticket, "2号窗口").start();
			new Thread(ticket, "3号窗口").start();
		}
	
	}
	
	class Ticket implements Runnable {
		private int tick = 100;
	
		private Lock lock = new ReentrantLock();
	
		@Override
		public void run() {
			while (true) {
				lock.lock();// 上锁
				try {
					if (tick > 0) {
						try {
							Thread.sleep(200);
						} catch (InterruptedException e) {
							e.printStackTrace();
						}
						System.out.println(Thread.currentThread().getName()
								+ "完成售票，余票为：" + --tick);
					}
				} finally {
					lock.unlock();//释放锁，那必须写在finally中
				}
	
			}
		}
	}
####使用lock如何实现等待-唤醒机制，实例为生产者消费者案例
####什么是虚假唤醒
在生产者和消费者中，生产者向店员提供商品，消费者向店员消费商品，在多个线程中同时生产和消费，就会出现一种情况就是不停的显示缺货，为了解决这个问题就是，如果店员没有商品，那么消费者消费的线程就等待，否则就唤醒，同理，生产者发现店员商品已满就等待生产，否则就唤醒生产，虚假唤醒就是一些obj.wait()会在除了obj.notify()和obj.notifyAll()的其他情况被唤醒，而此时是不应该唤醒的。以下是一种解决办法：

	public class TestProductorAndCosumer {
		public static void main(String[] args) {
			Clerk clerk = new Clerk();
			
			Productor productor = new Productor(clerk);
			Customer customer = new Customer(clerk);
			
			new Thread(productor,"生产者 A").start();
			new Thread(customer,"消费者 B").start();
			
			new Thread(productor,"生产者 C").start();
			new Thread(customer,"消费者 D").start();
		}
	} 
	//店员
	class Clerk{
		private int product = 0;
		
		//进货
		public synchronized void get(){
			while(product >= 1){// 为了避免虚假唤醒的问题，应该使用在循环中
				System.out.println("产品已满！！");
				try {
					this.wait();//产品已满，等待消费者的通知
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
				System.out.println(Thread.currentThread().getName()+" : "+ ++product);
				this.notifyAll();
			
		}
		//卖货
		public synchronized void sale(){
			while(product <= 0){
				System.out.println("缺货！！");
				try {
					this.wait();
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
			}
				System.out.println(Thread.currentThread().getName()+" : "+ --product);
				this.notifyAll();
			
		}
	}
	//生产者
	class Productor implements Runnable{
		private Clerk clerk;
		public Productor(Clerk clerk){
			this.clerk = clerk;
		}
	
		@Override
		public void run() {
			for (int i = 0; i < 20; i++) {
				try {
					Thread.sleep(200);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				clerk.get();//20个员工不断生产商品给店员
			}
		}
	}
	//消费者
	class Customer implements Runnable{
		private Clerk clerk;
		public Customer(Clerk clerk) {
			this.clerk = clerk;
		}
		@Override
		public void run() {
			for (int i = 0; i < 20; i++) {
				clerk.sale();
			}
		}
	}
结果如下：
	
	缺货！！
	缺货！！
	生产者 A : 1
	消费者 D : 0
	缺货！！
	缺货！！
	生产者 C : 1
	消费者 B : 0
	缺货！！
	缺货！！
	生产者 A : 1
	消费者 D : 0
	缺货！！
	缺货！！
	生产者 C : 1
	消费者 B : 0
	缺货！！
	缺货！！
	生产者 A : 1
	消费者 D : 0
	缺货！！
	生产者 C : 1
	消费者 B : 0
####解决办法之二：使用Lock锁

	public class TestProductorAndCosumerforLock {
		public static void main(String[] args) {
			Clerk clerk = new Clerk();
			
			Productor productor = new Productor(clerk);
			Customer customer = new Customer(clerk);
			
			new Thread(productor,"生产者 A").start();
			new Thread(customer,"消费者 B").start();
			
			new Thread(productor,"生产者 C").start();
			new Thread(customer,"消费者 D").start();
		}
	} 
	//店员
	class Clerk{
		private int product = 0;
		private Lock lock = new ReentrantLock();
		private Condition condition = lock.newCondition();
		
		//进货
		public void get(){
			lock.lock();
			try {
				while(product >= 1){// 为了避免虚假唤醒的问题，应该使用在循环中
					System.out.println("产品已满！！");
					try {
						condition.await();//产品已满，等待消费者的通知
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
					System.out.println(Thread.currentThread().getName()+" : "+ ++product);
					condition.signalAll();
			}finally{
				lock.unlock();
			}
			
		}
		//卖货
		public  void sale(){
			lock.lock();
			try {
				while(product <= 0){
					System.out.println("缺货！！");
					try {
						condition.await();;
					} catch (InterruptedException e) {
						
					}
				}
					System.out.println(Thread.currentThread().getName()+" : "+ --product);
					condition.signalAll();
			}finally{
				lock.unlock();
			}
			
			
		}
	}
	//生产者
	class Productor implements Runnable{
		private Clerk clerk;
		public Productor(Clerk clerk){
			this.clerk = clerk;
		}
	
		@Override
		public void run() {
			for (int i = 0; i < 3; i++) {
				try {
					Thread.sleep(200);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				clerk.get();//20个员工不断生产商品给店员
			}
		}
	}
	//消费者
	class Customer implements Runnable{
		private Clerk clerk;
		public Customer(Clerk clerk) {
			this.clerk = clerk;
		}
		@Override
		public void run() {
			for (int i = 0; i < 3; i++) {
				clerk.sale();
			}
		}
	}
注意上述代码中改动的地方有很多，使用了接下来要说的Condition控制线程通信

###8.Condition控制线程通信
在上述代码中也使用了Condition控制线程通信，Condition接口描述了可能会与锁有关联的条件变量，在Condition对象中与wait、notify、notifyAll方法分别对应的是await、signal、signalAll。 
####一个面试题
编写一个程序，开启三个线程，这三个线程的ID分别为A、B、C，每一个线程将自己的ID打印10遍，输出结果为
ABBCCCABBCCC....依次递归

	public class TestABC {
		
		public static void main(String[] args) {
			final AlternateDemo ad = new AlternateDemo();
			
			new Thread(new Runnable() {
				
				@Override
				public void run() {
					for (int i = 1; i <= 10; i++) {
						ad.loopA(i);
					}
					
				}
			},"A").start();
			new Thread(new Runnable() {
						
				@Override
				public void run() {
					for (int i = 1; i <= 10; i++) {
						ad.loopB(i);
					}
					
				}
			},"B").start();
			new Thread(new Runnable() {
				
				@Override
				public void run() {
					for (int i = 1; i <= 10; i++) {
						ad.loopC(i);
						System.out.println("================一轮结束==================");
					}
					
				}
			},"C").start();
		}
	
	}
	class AlternateDemo{
		private int number = 1;// 当前正在执行线程的标记
		private Lock  lock = new ReentrantLock();
		private Condition condition1 = lock.newCondition();
		private Condition condition2 = lock.newCondition();
		private Condition condition3 = lock.newCondition();
		
		public void loopA(int totalLoop){
			lock.lock();
			try {
				if(number != 1){
					condition1.await();
				}
				//打印
				for(int i = 1; i <= 1; i++){
					System.out.println(Thread.currentThread().getName()+"\t"+i+"\t"+totalLoop);
				}
				//唤醒别人
				number = 2;
				condition2.signal();//只唤醒线程B
			} catch (Exception e) {
				e.printStackTrace();
			}finally{
				lock.unlock();
			}
		}
		
		public void loopB(int totalLoop){
			lock.lock();
			try {
				if(number != 2){
					condition2.await();
				}
				//打印
				for(int i = 1; i <= 2; i++){
					System.out.println(Thread.currentThread().getName()+"\t"+i+"\t"+totalLoop);
				}
				//唤醒别人
				number = 3;
				condition3.signal();//只唤醒线程C
			} catch (Exception e) {
				e.printStackTrace();
			}finally{
				lock.unlock();
			}
		}
		
		public void loopC(int totalLoop){
			lock.lock();
			try {
				if(number != 3){
					condition3.await();
				}
				//打印
				for(int i = 1; i <= 3; i++){
					System.out.println(Thread.currentThread().getName()+"\t"+i+"\t"+totalLoop);
				}
				//唤醒别人
				number = 1;
				condition1.signal();//只唤醒线程A
			} catch (Exception e) {
				e.printStackTrace();
			}finally{
				lock.unlock();
			}
		}
	}


###14.ForkJoinPool分支/合并框架