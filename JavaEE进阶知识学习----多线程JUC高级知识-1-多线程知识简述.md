##多线程基础知识
###1.概述程序，进程和线程
程序：是为了完成某一特定的功能或任务，用某种语言编写的一段静态代码。
进程：是程序的一次执行过程，它自身有产生，存在和消亡的生命周期。
线程：是进程的小单元，是一个程序内部的一条执行路径。
###2.Java中多线程的创建和使用
####2.1线程的创建方法之一：继承Thread类，重写Run()方法。
使用说明：
1.定义子类继承Thread类。
2.子类中重写Thread类中的run方法。
3.创建Thread子类对象，即创建了线程对象
4.调用线程对象的start方法，启动线程，调用run方法。

	class SubThread extends Thread{
		@Override
		public void run() {
			for(int i = 1; i <=100;i++){
				System.out.println(Thread.currentThread().getName()+":"+i);
			}
		}
	}
	public class TestThread {
		public static void main(String[] args) {
			SubThread st = new SubThread();
			st.start();
			for(int i = 1; i <=100;i++){
				System.out.println(Thread.currentThread().getName()+":"+i);
			}
		}
	}
####Thread类常用方法说明
1.start(),启动线程并执行相应的run()方法。
2.run(),子线程要执行的代码放在run()方法中。
3.currentThread(),静态方法，调取当前的线程。
4.getName(),获取此线程的名字。
5.setName(),设置此线程的名字。
6.yield(),调用此方法的线程释放当前cpu的执行权。
以上方法的测试代码如下：

	class SubThread extends Thread{
		@Override
		public void run() {
			for(int i = 1; i <=100;i++){
				System.out.println(Thread.currentThread().getName()+":"+i);
			}
		}
	}
	public class TestThread {
		public static void main(String[] args) {
			SubThread st = new SubThread();
			st.setName("我是子线程");
			st.start();
			Thread.currentThread().setName("我是主线程======");
			for(int i = 1; i <=100;i++){
				System.out.println(Thread.currentThread().getName()+":"+i);
				if(i % 10 == 0){
					Thread.currentThread().yield();
				}
				
			}
		}
	}
7.join(),在A线程中调用B线程的join()方法，表示A线程执行到此方法，A线程停止执行，直到B线程执行结束，在执行A线程剩余的。
8.isAlive(),判断线程是否还存活。
9.sleep(long long),显示的让当前线程睡眠long毫秒。

	class SubThread extends Thread{
		@Override
		public void run() {
			for(int i = 1; i <=100;i++){
				try {
					Thread.currentThread().sleep(1000);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}	
				System.out.println(Thread.currentThread().getName()+":"+i);
			}
		}
	}
	public class TestThread {
		public static void main(String[] args) {
			SubThread st = new SubThread();
			st.setName("我是子线程");
			st.start();
			Thread.currentThread().setName("我是主线程======");
			for(int i = 1; i <=100;i++){
				System.out.println(Thread.currentThread().getName()+":"+i);
				if(i == 20){
					try {
						st.join();
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
			}
			System.out.println(st.isAlive());
		}
	}
10.线程通信：wait(),notify(),notifyAll()
11.getPriority()，返回线程的优先值。
12.setPriority(int newPriority),改变线程的优先级。
####继承方式实例
模拟火车站售票窗口，开启三个窗口售票，总票数为100张

	class Window extends Thread{
		static int ticket = 100;
		
		public void run() {
			while(true){
				if(ticket > 0){
					System.out.println(Thread.currentThread().getName()+":"+ticket--);
				}else{
					break;
				}
			}
		}
	}
	public class ThreadDemo {
		public static void main(String[] args) {
			Window w1 = new Window();
			Window w2 = new Window();
			Window w3 = new Window();
			
			w1.setName("窗口一");
			w2.setName("窗口二");
			w3.setName("窗口三");
			
			w1.start();
			w2.start();
			w3.start();
		}
	}
注意：ticket只有声明为static,因为三个线程是三个对象，如果不声明为static，三个线程对象就会各自拥有一个ticket=100，使用static修饰后，三个对象共用一个ticket=100。
####2.1线程的创建方法之二：实现Runnable接口
1.定义子类，实现Runnable接口。
2.子类重写Runnable接口中的run方法。
3.通过Thread类含参构造器创建线程对象。
4.将Runnable接口的子类对象作为实际参数传递给Thread类的构造方法中。
5.调用Thread类的start方法，开启线程并调用Runnable子类的run方法。

	class RunnableDemo implements Runnable{
		@Override
		public void run() {
			for(int i = 1 ; i <= 100; i++){
				if(i % 2 ==0){
					System.out.println(Thread.currentThread().getName()+":"+i);
				}
			}
		}
	}
	public class TestRunnable {
		public static void main(String[] args) {
			RunnableDemo ra = new RunnableDemo();
			Thread t1 = new Thread(ra);
			t1.start();
			Thread t2 = new Thread(ra);
			t2.start();
		}
	}
####实现方式实例
	class RunnableDemo implements Runnable{
		int ticket = 100;
		@Override
		public void run() {
			while(true){
				if(ticket > 0){
					System.out.println(Thread.currentThread().getName()+":"+ticket--);
				}else{
					break;
				}
			}
		}
	}
	public class TestRunnable {
		public static void main(String[] args) {
			RunnableDemo ra = new RunnableDemo();
			Thread t1 = new Thread(ra);
			Thread t2 = new Thread(ra);
			Thread t3 = new Thread(ra);
			
			t1.setName("窗口一");
			t2.setName("窗口二");
			t3.setName("窗口三");
			
			t1.start();
			t2.start();
			t3.start();
		}
	}
####继承方法和实现方法的联系和区别
区别：
继承：线程代码存放在Thread子类的run方法中。
实现：线程代码存放在接口的子类的run方法中。
实现的好处：
1.避免了单继承的局限性
2.多个线程可以共享同一个接口的实现类的对象，适合多个线程来处理同一份资源。
###3.线程的调度
####3.1调度策略
时间片：就是按时间顺序，先到先执行。
抢占式：高高优先级的线程抢占CPU。
####3.2Java中的调度方法
1.同优先级线程组成先进先出队列，使用时间片策略。
2.对高优先级，使用优先调度的抢占式策略。
####3.3线程的优先级
MAX_PRIORITY（10）;  最大的优先级  
MIN _PRIORITY （1）;  最小的优先级
NORM_PRIORITY （5）;  默认的优先级
getPriority()，返回线程的优先值。
setPriority(int newPriority),改变线程的优先级。
线程的创建时继承父线程的优先级。
优先级别只是说线程抢占CPU的概率增加，并不能说是一定高优先级的先执行完。
###4.线程的生命周期
JDK中用Thread.State枚举表示线程的几种状态。
1.新建： 当一个Thread类或其子类的对象被声明并创建时，新生的线程对象处于新建状态
2.就绪：处于新建状态的线程被start()后，将进入线程队列等待CPU时间片，此时它已具备了运行的条件
3.运行：当就绪的线程被调度并获得处理器资源时,便进入运行状态， run()方法定义了线程的操作和功能
4.阻塞：在某种特殊情况下，被人为挂起或执行输入输出操作时，让出 CPU 并临时中止自己的执行，进入阻塞状态
5.死亡：线程完成了它的全部工作或线程被提前强制性地中止   
![](https://i.imgur.com/EB72h3X.jpg)
###5.线程的同步机制
在上面使用继承和实现方法的案例中，有时运行会出现一种情况重票，和负票的情况。这个就是线程安全问题。
#####线程安全问题原因：
我们创建了多个线程，存在共享数据，那就有可能出现线程安全问题，当其中一个线程操作共享数据时，还未操作完成 ，另外的线程参与进来，导致对共享数据的操作出现问题。
#####解决方法
要求一个线程操作共享数据结束后，其他线程才能参与进来。
####方式一：同步代码块
	synchronized(同步监视器){
		//需要被同步的代码块（即为操作共享数据的代码）
	}
共享数据：多个线程共同操作的同一数据（变量）。
同步监视器：由一个类的对象来充当，那个线程获取此监视器，谁就执行大括号里面被同步的代码。俗称锁，任何一个类的对象都可以充当锁，要想保证线程的安全，就必须要求所有的线程共用一把锁。（如果将锁放在run方法里，就成了局部变量，就不会保证线程安全）
使用实现的方式中，同步监视器可以使用this，继承的的方式慎用this。

	class RunnableDemo implements Runnable{
		int ticket = 100;//共享数据
		@Override
		public void run() {
			while(true){
				synchronized(this){
					if(ticket > 0){
						System.out.println(Thread.currentThread().getName()+":"+ticket--);
					}
				}
			}
		}
	}
	public class TestRunnable {
		public static void main(String[] args) {
			RunnableDemo ra = new RunnableDemo();
			Thread t1 = new Thread(ra);
			Thread t2 = new Thread(ra);
			Thread t3 = new Thread(ra);
			
			t1.setName("窗口一");
			t2.setName("窗口二");
			t3.setName("窗口三");
			
			t1.start();
			t2.start();
			t3.start();
		}
	}
在继承方式中，锁只有声明为static才能保证线程安全，
####方式二：同步方法
将操作共享数据的方法声明为synchronized，即表明此方法为同步方法。

	class RunnableDemo implements Runnable{
		int ticket = 100;//共享数据
		@Override
		public void run() {
			while(true){
				show();
			}
		}
		public synchronized void show(){
			if(ticket > 0){
				try {
					Thread.currentThread().sleep(10);
				} catch (InterruptedException e) {
					// TODO Auto-generated catch block
					e.printStackTrace();
				}
				System.out.println(Thread.currentThread().getName()+":"+ticket--);
			}
		}
	}
	public class TestRunnable {
		public static void main(String[] args) {
			RunnableDemo ra = new RunnableDemo();
			Thread t1 = new Thread(ra);
			Thread t2 = new Thread(ra);
			Thread t3 = new Thread(ra);
			
			t1.setName("窗口一");
			t2.setName("窗口二");
			t3.setName("窗口三");
			
			t1.start();
			t2.start();
			t3.start();
		}
	}
同步方法的锁：就时当前对象this。在继承方式中不能使用，因为锁代表当前对象。继承的方式有多个对象。
###互斥锁
在Java语言中，为了保证共享数据操作的完整性，引入了对象互斥锁的概念。
每一个对象都对应于一个可称为“互斥锁”的标记，这个标记用来保证在任一时刻，只有一个线程访问改对象。
关键字synchronized来与对象的互斥锁联系，当某一个对象用synchronized修饰时，表明改对象在任一时刻只能由一个线程访问。
同步的局限性会导致程序的执行效率降低。
###6.线程的通信
###7.使用多线程的优点
1.提高应用程序的响应，增强用户体验。
2.提高计算机系统的CPU利用率。
3.改善程序结构，将既长又复杂的进程分为多个线程，独立运行，利于修改和理解。


