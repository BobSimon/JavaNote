##什么是定时任务调度
基于给定的时间点，给定的时间间隔或者给定的执行次数自动执行的任务。
##Timer定义
Time是jdk自带的一个类，位于java.lang.Object下的java.util.Timer，定义为，有且仅有一个后台线程对多个业务线程进行定时频率的调度。
##Timer的主要构件
Timer和TimerTask,timer定时调用TimerTask。
##Timer的Hello world
###业务逻辑的MyTimerTask类

	public class MyTimerTask extends TimerTask{
		private String name;
		public MyTimerTask(String inputName) {
			this.name = inputName;
		}
		@Override
		public void run() {
			//打印当前name的值
			System.out.println("当前的name值为："+name);
		}
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
	}
###Timer测试类的编写

	public class MyTimer {
		public static void main(String[] args) {
			//创建一个timer实例
			Timer timer = new Timer();
			//创建一个MyTimerTask实例
			MyTimerTask myTimerTask = new MyTimerTask("张三丰");
			//设置timer定时定频率的调用myTimerTask的业务逻辑
			//第一次执行是在两秒之后，之后每隔一秒执行一次
			timer.schedule(myTimerTask, 2000L,1000L);
		}
	}
##Timer的定时调度函数的用法
###schedule的四种用法
####1.timer.schedule(task, time);在时间等于或超过timer的时候执行且仅执行一次task。



 - task：所有安排的任务
 - time：执行任务的时间

实例如下：
MyTimerTask类做了如下的修改

	public class MyTimerTask extends TimerTask{
		private String name;
		public MyTimerTask(String inputName) {
			this.name = inputName;
		}
		@Override
		public void run() {
			Calendar calendar = Calendar.getInstance();
			SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
			System.out.println("当前时间为："+sf.format(calendar.getTime()));
			//打印当前name的值
			System.out.println("当前的name值为："+name);
		}
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
	}

MyTimer类详情如下

	public class MyTimer {
		public static void main(String[] args) {
			//创建一个timer实例
			Timer timer = new Timer();
			//创建一个MyTimerTask实例
			MyTimerTask myTimerTask = new MyTimerTask("张三丰");
			//设置timer定时定频率的调用myTimerTask的业务逻辑
			//获取当前时间，并设置成为距离当前时间的后三秒的时间
			Calendar calendar = Calendar.getInstance();
			SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
			System.out.println("当前时间为："+sf.format(calendar.getTime()));
			calendar.add(Calendar.SECOND, 3);
			myTimerTask.setName("schedule的第一种用法：");
			timer.schedule(myTimerTask, calendar.getTime());
		}
	}
结果，在当前时间后三秒只打印一次name的值。
####2.timer.schedule(task, delay);等待delay毫秒后执行且执行一次task.



	timer.schedule(myTimerTask, 1000);

####3.timer.schedule(task, firstTime, period);时间等于或者超过timer时首次执行task，之后每隔period毫秒重复执行一次task。

 - task：所有安排的任务
 - firstTime：首次执行任务的时间
 - period：执行一次task的时间间隔，单位是毫秒。

实例如下：在第一种用法上修改为如下，表示当前时间三秒后第一次打印，之后每隔两秒打印一次。
	
	timer.schedule(myTimerTask, calendar.getTime(),2000);

####4.timer.schedule(task, delay, period);等待delay毫秒之后首次执行一次task，之后每隔period秒后重复执行一次task。


	timer.schedule(myTimerTask, 3000,1000);


###scheduleAtFixedRate的两种用法
####1.timer.scheduleAtFixedRate(task, firstTime, period);时间等于或超过firsttime时首次执行task,之后每隔period毫秒重复执行一次task。



	timer.scheduleAtFixedRate(myTimerTask, calendar.getTime(), 1000);
####2.timer.scheduleAtFixedRate(task, delay, period);等待delay毫秒后首次执行task，之后每隔period毫秒重复执行一次task。


	timer.scheduleAtFixedRate(myTimerTask, 3000, 1000);
##其他重要函数
###TimerTask的cancel()scheduledExecutionTime()
####cancel()取消当前TimerTask的任务。
实例：任务延迟三秒执行，之后每隔一秒执行一次，执行三次后结束，需要修改MyTimerTask

	public class MyTimerTask extends TimerTask{
		private String name;
		private Integer count = 0;
		public MyTimerTask(String inputName) {
			this.name = inputName;
		}
		@Override
		public void run() {
			if(count < 3){
				Calendar calendar = Calendar.getInstance();
				SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
				System.out.println("当前时间为："+sf.format(calendar.getTime()));
				//打印当前name的值
				System.out.println("当前的name值为："+name);
				count++;
			}else{
				cancel();//取消任务
				System.out.println("任务被取消了");
			}
		}
		public String getName() {
			return name;
		}
		public void setName(String name) {
			this.name = name;
		}
	}
####scheduledExecutionTime()返回此任务最近实际执行的已安排执行的时间，返回值为long型

	timer.schedule(myTimerTask, 3000);
	System.out.println("任务将要执行的时间为："+sf.format(myTimerTask.scheduledExecutionTime()));
其实就是，我们如果要延迟三秒执行，那么打印的就是执行时间。
注意：增加了执行次数统计器。以此来判断执行的次数。
###timer的cancel()和purge();
####cancel();终止计时器，丢弃所有当前已安排的任务

	public class MyTimer2 {
		public static void main(String[] args) throws InterruptedException {
			//创建timer实例
			Timer timer = new Timer();
			//创建TimerTask实例
			MyTimerTask task1 = new MyTimerTask("任务1");
			MyTimerTask task2 = new MyTimerTask("任务2");
			Calendar calendar = Calendar.getInstance();
			SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
			System.out.println("当前时间为："+sf.format(calendar.getTime()));
			//task1是首次执行距离现在时间3秒,之后每隔2秒执行一次。
			//task2是首次执行距离现在时间1秒,之后每隔2秒执行一次。
			timer.schedule(task1, 3000,2000);
			timer.schedule(task2, 1000,2000);
			//休眠5秒
			Thread.sleep(5000);
			Date date = new Date();
			System.out.println("现在时间是："+sf.format(date));
			//取消所有任务
			timer.cancel();
			System.out.println("所有任务被取消");
			
		}
	}
####purge();从此计时器的任务队列中移除所有已取消的任务，返回移除的任务数。

	public class MyTimer2 {
		public static void main(String[] args) throws InterruptedException {
			//创建timer实例
			Timer timer = new Timer();
			//创建TimerTask实例
			MyTimerTask task1 = new MyTimerTask("任务1");
			MyTimerTask task2 = new MyTimerTask("任务2");
			Calendar calendar = Calendar.getInstance();
			SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
			System.out.println("当前时间为："+sf.format(calendar.getTime()));
			//task1是首次执行距离现在时间3秒,之后每隔2秒执行一次。
			//task2是首次执行距离现在时间1秒,之后每隔2秒执行一次。
			timer.schedule(task1, 3000,2000);
			timer.schedule(task2, 3000,2000);
			System.out.println("当前已取消的任务数："+timer.purge());
			//休眠5秒
			Thread.sleep(5000);
			Date date = new Date();
			System.out.println("现在时间是："+sf.format(date));
			//取消所有任务
			task2.cancel();
			System.out.println("当前已取消的任务数："+timer.purge());
			
		}
	}
###schedule与scheduleAtFixedRate的区别
1.当首次执行的时间早于当前时间的时候
在schedule中如果首次执行的时间早于当前时间，那么第一次执行的时间还是会以当前时间为准。

	public class MyTimer {
		public static void main(String[] args) {
			Calendar calendar = Calendar.getInstance();
			final SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
			System.out.println("当前时间为："+sf.format(calendar.getTime()));
			//设置时间为6秒前
			calendar.add(Calendar.SECOND, -6);
			Timer timer = new Timer();
			timer.schedule(new TimerTask() {
				
				@Override
				public void run() {
					//打印当前计划执行的时间
					System.out.println("计划要执行的时间："+sf.format(scheduledExecutionTime()));
					System.out.println("任务正在执行");
				}
			}, calendar.getTime(),2000);
			
		}
	
	}

2.任务执行的时间超出任务的执行周期时间的时候
scheduleAtFixedRate在这种情况下会从首次执行时间开始立马执行，这里就涉及到了同步的问题。

	public class MyTimer {
		public static void main(String[] args) {
			Calendar calendar = Calendar.getInstance();
			final SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
			System.out.println("当前时间为："+sf.format(calendar.getTime()));
			//设置时间为6秒前
			calendar.add(Calendar.SECOND, -6);
			Timer timer = new Timer();
			timer.scheduleAtFixedRate(new TimerTask() {
				
				@Override
				public void run() {
					//打印当前计划执行的时间
					System.out.println("计划要执行的时间："+sf.format(scheduledExecutionTime()));
					System.out.println("任务正在执行");
				}
			}, calendar.getTime(),2000);
			
		}
	
	}
###实例
有两个定时机器人，一个负责灌水，一个负责跳舞，当桶被灌满后，停止四秒，跳舞也结束运行。
####跳舞机器人

	public class DancingRobot extends TimerTask{
	
		@Override
		public void run() {
			//获取最近的一次任务执行时间，并将其格式化
			SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
			System.out.println("下一次任务执行的时间为："+sf.format(scheduledExecutionTime()));
			System.out.println("跳舞机器人正在欢快的跳舞...");
			
		}
	
	}
####灌水机器人

	public class WaterRobot extends TimerTask{
		private Timer timer;
		//桶的最大容量为5L
		private Integer bucketCappacity = 0;
		public WaterRobot(Timer timer) {
			this.timer = timer;
		}
		@Override
		public void run() {
			if(bucketCappacity < 5){
				System.out.println("灌水机器人努力灌水中...");
				bucketCappacity++;
			}else{
				//水满之后就停止执行
				cancel();
				System.out.println("time中取消的任务数："+timer.purge());
				//等待两秒后，终止timer里面的所有内容
				System.out.println("水已经灌满了...");
				try {
					Thread.sleep(4000);
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				timer.cancel();
				System.out.println("所有的任务都取消了");
			}
		}
	
	}
####执行程序

	public class Exector {
		public static void main(String[] args) {
			Timer timer = new Timer();
			Calendar calendar = Calendar.getInstance();
			SimpleDateFormat sf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
			System.out.println("当前时间为："+sf.format(calendar.getTime()));
			
			DancingRobot dr = new DancingRobot();
			WaterRobot wr = new WaterRobot(timer);
			
			timer.schedule(dr, calendar.getTime(),2000);
			timer.scheduleAtFixedRate(wr, calendar.getTime(), 1000);
		}
	
	}
####缺陷
1.不能满足我们的并发需求。
2.如果一个timertask抛出异常，就会终止Timer中的所有任务。

