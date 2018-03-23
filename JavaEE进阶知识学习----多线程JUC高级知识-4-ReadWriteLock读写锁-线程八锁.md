###9.ReadWriteLock读写锁
说明：写写/读写需要‘互斥’，读读不需要‘互斥’。不能一存在线程问题就加锁，读就不需要锁，所以才有了读写分离的读写锁ReadWriteLock。
是一个接口，位于java.util.concurrent.locks包下。有两个方法
#####Lock	readLock() 返回用于读取操作的锁。
#####Lock	writeLock() 返回用于写入操作的锁。
实例如下：

	public class TestReadWriteLock {
		public static void main(String[] args) {
			final ReadWriteLockDemo rw = new ReadWriteLockDemo();
			new Thread(new Runnable() {
				
				@Override
				public void run() {
					rw.set((int)(Math.random()*10));
					
				}
			},"写线程").start();
			for(int i = 0; i< 5; i++){
				new Thread(new Runnable() {
					
					@Override
					public void run() {
						rw.get();
					}
				},"读操作").start();
			}
		}
		
	}
	class ReadWriteLockDemo{
		private int number = 0;
		private ReadWriteLock lock = new ReentrantReadWriteLock();
		//读操作
		public void get(){
			lock.readLock().lock();//上锁
			try{
				System.out.println(Thread.currentThread().getName()+":"+number);
			}finally{
				lock.readLock().unlock();//释放锁
			}
			
		}
		//写操作
		public void set(int number){
			lock.writeLock().lock();
			try{
				System.out.println(Thread.currentThread().getName());
				this.number = number;
			}finally{
				lock.writeLock().unlock();
			}
			
		}
	}

###10.线程八锁
如下实例：
####1.两个普通同步方法，两个线程，标准打印，结果：one two 

	public class TestThread8Monitor {
		public static void main(String[] args) {
			final Number number = new Number();
			
			new Thread(new Runnable() {
				@Override
				public void run() {
					number.getOne();
				}
			}).start();
			new Thread(new Runnable() {
				@Override
				public void run() {
					number.getTwo();
				}
			}).start();
		}
	}
	class Number{
		public synchronized void getOne(){
			System.out.println("one");
		}
		public synchronized void getTwo(){
			System.out.println("Two");
		}
	}


####2.新增Thread.sleep()给getOne(),结果：one two
	
	public class TestThread8Monitor {
		public static void main(String[] args) {
			final Number number = new Number();
			
			new Thread(new Runnable() {
				@Override
				public void run() {
					number.getOne();
				}
			}).start();
			new Thread(new Runnable() {
				@Override
				public void run() {
					number.getTwo();
				}
			}).start();
		}
	}
	class Number{
		public synchronized void getOne(){
			try {
				Thread.sleep(3000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println("one");
		}
		public synchronized void getTwo(){
			System.out.println("Two");
		}
	}
####3.新增普通方法getThree(),结果：three one two

	public class TestThread8Monitor {
		public static void main(String[] args) {
			final Number number = new Number();
			
			new Thread(new Runnable() {
				@Override
				public void run() {
					number.getOne();
				}
			}).start();
			new Thread(new Runnable() {
				@Override
				public void run() {
					number.getTwo();
				}
			}).start();
			new Thread(new Runnable() {
				@Override
				public void run() {
					number.getThree();
				}
			}).start();
		}
	}
	class Number{
		public synchronized void getOne(){
			try {
				Thread.sleep(3000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println("one");
		}
		public synchronized void getTwo(){
			System.out.println("Two");
		}
		public void getThree(){
			System.out.println("Three");
		}
	}

####4.两个普通同步方法，两个对象，结果：two one

	public class TestThread8Monitor {
		public static void main(String[] args) {
			final Number number1 = new Number();
			final Number number2 = new Number();
			
			new Thread(new Runnable() {
				@Override
				public void run() {
					number1.getOne();
				}
			}).start();
			new Thread(new Runnable() {
				@Override
				public void run() {
					number2.getTwo();
				}
			}).start();
			
		}
	}
	class Number{
		public synchronized void getOne(){
			try {
				Thread.sleep(3000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println("one");
		}
		public synchronized void getTwo(){
			System.out.println("Two");
		}
	}
####5.修改getOne()为静态同步方法，结果：two one

	public class TestThread8Monitor {
		public static void main(String[] args) {
			final Number number1 = new Number();
			final Number number2 = new Number();
			
			new Thread(new Runnable() {
				@Override
				public void run() {
					number1.getOne();
				}
			}).start();
			new Thread(new Runnable() {
				@Override
				public void run() {
					number2.getTwo();
				}
			}).start();
			
		}
	}
	class Number{
		public static synchronized void getOne(){
			try {
				Thread.sleep(3000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println("one");
		}
		public synchronized void getTwo(){
			System.out.println("Two");
		}
	}

####6.修改两个方法均为静态同步方法，一个对象，结果：one two

	public class TestThread8Monitor {
		public static void main(String[] args) {
			final Number number1 = new Number();
			
			new Thread(new Runnable() {
				@Override
				public void run() {
					number1.getOne();
				}
			}).start();
			new Thread(new Runnable() {
				@Override
				public void run() {
					number1.getTwo();
				}
			}).start();
			
		}
	}
	class Number{
		public static synchronized void getOne(){
			try {
				Thread.sleep(3000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println("one");
		}
		public static synchronized void getTwo(){
			System.out.println("Two");
		}
	}
####7.一个静态同步方法，一个非静态同步方法，两个对象，结果：two one

	public class TestThread8Monitor {
		public static void main(String[] args) {
			final Number number1 = new Number();
			final Number number2 = new Number();
			
			new Thread(new Runnable() {
				@Override
				public void run() {
					number1.getOne();
				}
			}).start();
			new Thread(new Runnable() {
				@Override
				public void run() {
					number2.getTwo();
				}
			}).start();
			
		}
	}
	class Number{
		public static synchronized void getOne(){
			try {
				Thread.sleep(3000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println("one");
		}
		public  synchronized void getTwo(){
			System.out.println("Two");
		}
	}
####8.两个静态同步方法，两个对象，结果：one two

	public class TestThread8Monitor {
		public static void main(String[] args) {
			final Number number1 = new Number();
			final Number number2 = new Number();
			
			new Thread(new Runnable() {
				@Override
				public void run() {
					number1.getOne();
				}
			}).start();
			new Thread(new Runnable() {
				@Override
				public void run() {
					number2.getTwo();
				}
			}).start();
			
		}
	}
	class Number{
		public static synchronized void getOne(){
			try {
				Thread.sleep(3000);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
			System.out.println("one");
		}
		public static synchronized void getTwo(){
			System.out.println("Two");
		}
	}
以上就是线程的八种常见的情况，线程八锁的关键在于：
1. 非静态方法的锁默认为this，静态方法的锁为对应的class实例（这里是NUmber.class）
2. 某一个时刻内，只能有一个线程持有锁，无论有几个方法
