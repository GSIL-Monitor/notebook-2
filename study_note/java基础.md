# 1. 线程
## 1.1 实现线程的两种方式
### 1.1.1 extends Thread
```
public class MyThreadWithExtends extends Thread {
	String flag;
	
	public MyThreadWithExtends(String flag){
		this.flag = flag;
	}
	
	

	@Override
	public void run() {
		String tname = Thread.currentThread().getName();
		System.out.println(tname+"线程的run方法被调用……");
		Random random = new Random();
		for(int i=0;i<20;i++){
			try {
				Thread.sleep(random.nextInt(10)*100);
				System.out.println(tname+ "...."+ flag);
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}

	public static void main(String[] args) {
		Thread thread1 = new MyThreadWithExtends("a");
		Thread thread2 = new MyThreadWithExtends("b");
		thread1.start();
		thread2.start();
		/**
		 * 如果是调用thread的run方法，则只是一个普通的方法调用，不会开启新的线程
		 */
//		thread1.run();
//		thread2.run();
	}
}
```

### 1.1.2 implements Runnable
```
public class MyThreadWithImpliment implements Runnable {
	int x;

	public MyThreadWithImpliment(int x) {
		this.x = x;
	}

	@Override
	public void run() {
		String name = Thread.currentThread().getName();
		System.out.println("线程" + name + "的run方法被调用……");
		for (int i = 0; i < 10; i++) {
			System.out.println(x);
			try {
				Thread.sleep(100);
				
			} catch (InterruptedException e) {
				e.printStackTrace();
			}
		}
	}

	public static void main(String[] args) {
		Thread thread1 = new Thread(new MyThreadWithImpliment(1), "thread-1");
		Thread thread2 = new Thread(new MyThreadWithImpliment(2), "thread-2");
		 thread1.start();
		 thread2.start();
		// 注意调用run和调用start的区别,直接调用run，则都运行在main线程中
//		thread1.run();
//		thread2.run();
	}
}
```

## 1.2 synchronized
```
public class MySynchronized {
	public static void main(String[] args) {
		final MySynchronized mySynchronized = new MySynchronized();
		final MySynchronized mySynchronized2 = new MySynchronized();
		new Thread("thread1") {
			public void run() {
				synchronized (mySynchronized) {
				try {
					System.out.println(this.getName()+" start");
					int i =1/0;   //如果发生异常，jvm会将锁释放
					Thread.sleep(5000);
					System.out.println(this.getName()+"醒了");
					System.out.println(this.getName()+" end");
				} catch (InterruptedException e) {
					e.printStackTrace();
				}
				}
			}
		}.start();
		new Thread("thread2") {
			public void run() {
				synchronized (mySynchronized) {         //争抢同一把锁时，线程1没释放之前，线程2只能等待
//					synchronized (mySynchronized2) {    //如果不是一把锁，可以看到两句话同时打印
					System.out.println(this.getName()+" start");
					System.out.println(this.getName()+" end");
					
				}
			}
		}.start();
	}
}
```
## 1.3 Lock
### 1.3.1 local unlock
```
public class MyLockTest {
	private static ArrayList<Integer> arrayList = new ArrayList<Integer>();
	static Lock lock = new ReentrantLock(); // 注意这个地方
	public static <E> void main(String[] args) {
		new Thread() {
			public void run() {
				Thread thread = Thread.currentThread();
				
				lock.lock();
				try {
					System.out.println(thread.getName() + "得到了锁");
					for (int i = 0; i < 5; i++) {
						arrayList.add(i);
					}
				} catch (Exception e) {
					// TODO: handle exception
				} finally {
					System.out.println(thread.getName() + "释放了锁");
					lock.unlock();
				}

			};
		}.start();
		
		new Thread() {
			public void run() {
				Thread thread = Thread.currentThread();
				lock.lock();
				try {
					System.out.println(thread.getName() + "得到了锁");
					for (int i = 0; i < 5; i++) {
						arrayList.add(i);
					}
				} catch (Exception e) {
					// TODO: handle exception
				} finally {
					System.out.println(thread.getName() + "释放了锁");
					lock.unlock();
				}

			};
		}.start();
	}

}
```
### 1.3.2 TryLock
```
/**
 * 观察现象：一个线程获得锁后，另一个线程取不到锁，不会一直等待
 * @author
 *
 */
public class MyTryLock {

	private static ArrayList<Integer> arrayList = new ArrayList<Integer>();
	static Lock lock = new ReentrantLock(); // 注意这个地方
	public static void main(String[] args) {
		
		new Thread() {
			public void run() {
				Thread thread = Thread.currentThread();
				boolean tryLock = lock.tryLock();
				System.out.println(thread.getName()+" "+tryLock);
				if (tryLock) {
					try {
						System.out.println(thread.getName() + "得到了锁");
						for (int i = 0; i < 5; i++) {
							arrayList.add(i);
						}
					} catch (Exception e) {
						// TODO: handle exception
					} finally {
						System.out.println(thread.getName() + "释放了锁");
						lock.unlock();
					}
				}
			};
		}.start();

		new Thread() {
			public void run() {
				Thread thread = Thread.currentThread();
				boolean tryLock = lock.tryLock();
				System.out.println(thread.getName()+" "+tryLock);
				if (tryLock) {
					try {
						System.out.println(thread.getName() + "得到了锁");
						for (int i = 0; i < 5; i++) {
							arrayList.add(i);
						}
					} catch (Exception e) {
						// TODO: handle exception
					} finally {
						System.out.println(thread.getName() + "释放了锁");
						lock.unlock();
					}
				}

			};
		}.start();
	}
}
```
### 1.3.3 lockInterruptibly
```
/**
 * 观察现象：如果thread-0得到了锁，阻塞。。。thread-1尝试获取锁，如果拿不到，则可以被中断等待
 * @author
 *
 */
public class MyInterruptibly {
	 private Lock lock = new ReentrantLock();  
	 
	    public static void main(String[] args)  {
	    	MyInterruptibly test = new MyInterruptibly();
	        MyThread thread0 = new MyThread(test);
	        MyThread thread1 = new MyThread(test);
	        thread0.start();
	        thread1.start();
	         
	        try {
	            Thread.sleep(2000);
	        } catch (InterruptedException e) {
	            e.printStackTrace();
	        }
	        thread1.interrupt();
	        System.out.println("=====================");
	    }  
	     
	    public void insert(Thread thread) throws InterruptedException{
	        lock.lockInterruptibly();   //注意，如果需要正确中断等待锁的线程，必须将获取锁放在外面，然后将InterruptedException抛出
	        try {  
	            System.out.println(thread.getName()+"得到了锁");
	            long startTime = System.currentTimeMillis();
	            for(    ;     ;) {
	                if(System.currentTimeMillis() - startTime >= Integer.MAX_VALUE)
	                    break;
	                //插入数据
	            }
	        }
	        finally {
	            System.out.println(Thread.currentThread().getName()+"执行finally");
	            lock.unlock();
	            System.out.println(thread.getName()+"释放了锁");
	        }  
	    }
	}
	 
	class MyThread extends Thread {
	    private MyInterruptibly test = null;
	    public MyThread(MyInterruptibly test) {
	        this.test = test;
	    }
	    @Override
	    public void run() {
	         
	        try {
	            test.insert(Thread.currentThread());
	        } catch (Exception e) {
	            System.out.println(Thread.currentThread().getName()+"被中断");
	        }
	    }

}
```
### 1.3.4 SynchronizedReadWrite
```
/**
 * 一个线程又要读又要写，用synchronize来实现的话，读写操作都只能锁住后一个线程一个线程地进行
 * @author
 *
 */
public class MySynchronizedReadWrite {
    
    public static void main(String[] args)  {
        final MySynchronizedReadWrite test = new MySynchronizedReadWrite();
         
        new Thread(){
            public void run() {
                test.get(Thread.currentThread());
            };
        }.start();
         
        new Thread(){
            public void run() {
                test.get(Thread.currentThread());
            };
        }.start();
         
    }  
     
    public synchronized void get(Thread thread) {
        long start = System.currentTimeMillis();
        int i=0;
        while(System.currentTimeMillis() - start <= 1) {
        	i++;
        	if(i%4==0){
            System.out.println(thread.getName()+"正在进行写操作");
        	}else {
        		System.out.println(thread.getName()+"正在进行读操作");	
			}
        }
        System.out.println(thread.getName()+"读写操作完毕");
    }

}
```
### 1.3.5 ReentrantReadWriteLock 
```
/**
 * 使用读写锁，可以实现读写分离锁定，读操作并发进行，写操作锁定单个线程
 * 
 * 如果有一个线程已经占用了读锁，则此时其他线程如果要申请写锁，则申请写锁的线程会一直等待释放读锁。
 * 如果有一个线程已经占用了写锁，则此时其他线程如果申请写锁或者读锁，则申请的线程会一直等待释放写锁。
 * @author
 *
 */
public class MyReentrantReadWriteLock {
	 private ReentrantReadWriteLock rwl = new ReentrantReadWriteLock();
     
	    public static void main(String[] args)  {
	        final MyReentrantReadWriteLock test = new MyReentrantReadWriteLock();
	         
	        new Thread(){
	            public void run() {
	                test.get(Thread.currentThread());
	                test.write(Thread.currentThread());
	            };
	        }.start();
	         
	        new Thread(){
	            public void run() {
	                test.get(Thread.currentThread());
	                test.write(Thread.currentThread());
	            };
	        }.start();
	         
	    }  
	    
	    /**
	     * 读操作,用读锁来锁定
	     * @param thread
	     */
	    public void get(Thread thread) {
	        rwl.readLock().lock();
	        try {
	            long start = System.currentTimeMillis();
	             
	            while(System.currentTimeMillis() - start <= 1) {
	                System.out.println(thread.getName()+"正在进行读操作");
	            }
	            System.out.println(thread.getName()+"读操作完毕");
	        } finally {
	            rwl.readLock().unlock();
	        }
	    }

	    /**
	     * 写操作，用写锁来锁定
	     * @param thread
	     */
	    public void write(Thread thread) {
	        rwl.writeLock().lock();;
	        try {
	            long start = System.currentTimeMillis();
	             
	            while(System.currentTimeMillis() - start <= 1) {
	                System.out.println(thread.getName()+"正在进行写操作");
	            }
	            System.out.println(thread.getName()+"写操作完毕");
	        } finally {
	            rwl.writeLock().unlock();
	        }
	    }
}
```

### 1.3.6 Lock和synchronized的选择
- 1）Lock是一个接口，而synchronized是Java中的关键字，synchronized是内置的语言实现；
- 2）synchronized在发生异常时，会自动释放线程占有的锁，因此不会导致死锁现象发生；而Lock在发生异常时，如果没有主动通过unLock()去释放锁，则很可能造成死锁现象，因此使用Lock时需要在finally块中释放锁；
- 3）Lock可以让等待锁的线程响应中断，而synchronized却不行，使用synchronized时，等待的线程会一直等待下去，不能够响应中断；
- 4）通过Lock可以知道有没有成功获取锁，而synchronized却无法办到。
- 5）Lock可以提高多个线程进行读操作的效率。
  在性能上来说，如果竞争资源不激烈，两者的性能是差不多的，而当竞争资源非常激烈时（即有大量线程同时竞争），此时Lock的性能要远远优于synchronized。所以说，在具体使用时要根据适当情况选择。

## 1.4 Volatile 变量
### 1.4.1 一些特性

Volatile不具备原子特性
Volatile 变量可用于提供线程安全，但是只能应用于非常有限的一组用例子；多个变量之间或者某个变量的当前值与修改后值之间没有约束。因此，单独使用 volatile 还不足以实现计数器、互斥锁；在多个线程操作Volatile++就会出现问题

```
public class TestVolatile {
	public static volatile int numb = 0;
	public static void main(String[] args) throws Exception {
		for (int i = 0; i < 100; i++) {
			new Thread(new Runnable() {
				@Override
				public void run() {
					for (int i = 0; i < 1000; i++) {
						numb++;
					}
				}
			}).start();
		}
		Thread.sleep(2000);
		System.out.println(numb);
	}
}
```
volatile 变量不会像锁那样造成线程阻塞，因此也很少造成可伸缩性问题
要使 volatile 变量提供理想的线程安全，必须同时满足下面两个条件：
- 对变量的写操作不依赖于当前值。
- 该变量没有包含在具有其他变量的不变式中。
  第一个条件的限制使 volatile 变量不能用作线程安全计数器。虽然增量操作（x++）看上去类似一个单独操作，实际上它是一个由读取－修改－写入操作序列组成的组合操作，必须以原子方式执行，而 volatile 不能提供必须的原子特性。实现正确的操作需要使 x 的值在操作期间保持不变，而 volatile 变量无法实现这点。（然而，如果将值调整为只从单个线程写入，那么可以忽略第一个条件。）
### 1.4.2 volatile的适用场景
模式 #1：状态标志
也许实现 volatile 变量的规范使用仅仅是使用一个布尔状态标志，用于指示发生了一个重要的一次性事件，例如完成初始化或请求停机。
```
volatile boolean shutdownRequested;  
...
public void shutdown() {   
    shutdownRequested = true;   
}
public void doWork() {   
    while (!shutdownRequested) {   
        // do stuff  
    }  
}  
```

## 1.5 wait、notify、notifyAll
### 1.5.1 概念
- 1）wait()、notify()和notifyAll()方法是本地方法，并且为final方法，无法被重写。
- 2）调用某个对象的wait()方法能让当前线程阻塞，并且当前线程必须拥有此对象的monitor（即锁，或者叫管程）
- 3）调用某个对象的notify()方法能够唤醒一个正在等待这个对象的monitor的线程，如果有多个线程都在等待这个对象的monitor，则只能唤醒其中一个线程；
- 4）调用notifyAll()方法能够唤醒所有正在等待这个对象的monitor的线程
### 1.5.2 demo
- 代码
```
class NumberPrint implements Runnable{  
    private int number;  
    public byte res[];  
    public static int count = 5;  
    public NumberPrint(int number, byte a[]){  
        this.number = number;  
        res = a;  
    }  
    public void run(){  
        synchronized (res){  
            while(count-- > 0){  
                try {  
                    res.notify();//唤醒等待res资源的线程，把锁交给线程（该同步锁执行完毕自动释放锁）  
                    System.out.println(" "+number);  
                    res.wait();//释放CPU控制权，释放res的锁，本线程阻塞，等待被唤醒。  
                    System.out.println("------线程"+Thread.currentThread().getName()+"获得锁，wait()后的代码继续运行："+number);  
                } catch (InterruptedException e) {  
                    // TODO Auto-generated catch block  
                    e.printStackTrace();  
                }  
            }//end of while  
            return;  
        }//synchronized  
          
    }  
}  
public class WaitNotify {  
    public static void main(String args[]){  
        final byte a[] = {0};//以该对象为共享资源  
        new Thread(new NumberPrint((1),a),"1").start();  
        new Thread(new NumberPrint((2),a),"2").start();  
    }  
}  
```
- 执行结果
```
 1  
 2  
------线程1获得锁，wait()后的代码继续运行：1  
 1  
------线程2获得锁，wait()后的代码继续运行：2  
 2  
------线程1获得锁，wait()后的代码继续运行：1  
 1  
------线程2获得锁，wait()后的代码继续运行：2  
```
- 总结
  - wait()方法与notify()必须要与synchronized(resource)一起使用。
  - 也就是wait与notify针对已经获取了resource锁的线程进行操作，从语法角度来说就是Obj.wait(),Obj.notify必须在synchronized(Obj){...}语句块内。
  - 从功能上来说wait()线程在获取对象锁后，主动释放CPU控制权，主动释放对象锁，同时本线程休眠。直到有其它线程调用对象的notify()唤醒该线程，才能继续获取对象锁，并继续执行。相应的notify()就是对对象锁的释放操作。
  - 【因此，我们可以发现，wait和notify方法均可释放对象的锁，但wait同时释放CPU控制权，即它后面的代码停止执行，线程进入阻塞状态，而notify方法不立刻释放CPU控制权，而是在相应的synchronized(){}语句块执行结束，再自动释放锁。】
  - 释放锁后，JVM会在等待resoure的线程中选取一线程，赋予其对象锁，唤醒线程，继续执行。这样就提供了在线程间同步、唤醒的操作。
  - Thread.sleep()与Object.wait()二者都可以暂停当前线程，释放CPU控制权，主要的区别在于Object.wait()在释放CPU同时，释放了对象锁的控制，而在同步块中的Thread.sleep()方法并不释放锁，仅释放CPU控制权。
#2 java并发包
- JDK5.0 以后的版本都引入了高级并发特性，大多数的特性在java.util.concurrent 包中，是专门用于多线程发编程的，充分利用了现代多处理器和多核心系统的功能以编写大规模并发应用程序。主要包含原子量、并发集合、同步器、可重入锁，并对线程池的构造提供了强力的支持。

## 2.1 线程池
### 2.1.1 线程池的5中创建方式
```
/**
 * 列出并发包中的各种线程池
 * @author
 *
 */

public class ExecutorDemo {
	
	public static void main(String[] args) {
		ExecutorService newSingleThreadExecutor = Executors.newSingleThreadExecutor();
		ExecutorService newCachedThreadPool = Executors.newCachedThreadPool();
		
		int cpuNums = Runtime.getRuntime().availableProcessors();
		System.out.println(cpuNums);
		ExecutorService newFixedThreadPool = Executors.newFixedThreadPool(cpuNums);
		ScheduledExecutorService newScheduledThreadPool = Executors.newScheduledThreadPool(8);
		
		
		ScheduledExecutorService newSingleThreadScheduledExecutor = Executors.newSingleThreadScheduledExecutor();
	}
}
```
1. Single Thread Executor : 只有一个线程的线程池，因此所有提交的任务是顺序执行，
  代码： Executors.newSingleThreadExecutor()
2. Cached Thread Pool : 线程池里有很多线程需要同时执行，老的可用线程将被新的任务触发重新执行，如果线程超过60秒内没执行，那么将被终止并从池中删除，
  代码：Executors.newCachedThreadPool()
```
public class ThreadPoolWithRunable {
	/**
	 * 通过线程池执行线程
	 * @param args
	 */
	public static void main(String[] args) {
		//创建一个线程池
		ExecutorService pool = Executors.newCachedThreadPool();
		for(int i = 1; i < 5; i++){
			pool.execute(new Runnable() {
				@Override
				public void run() {
					System.out.println("thread name: " + Thread.currentThread().getName());
					try {
						Thread.sleep(1000);
					} catch (InterruptedException e) {
						e.printStackTrace();
					}
				}
			});
		}
		pool.shutdown();
	}
}
```
3. Fixed Thread Pool : 拥有固定线程数的线程池，如果没有任务执行，那么线程会一直等待，
  代码： Executors.newFixedThreadPool(4)
```
/**
 * callable 跟runnable的区别：
 * runnable的run方法不会有任何返回结果，所以主线程无法获得任务线程的返回值
 * 
 * callable的call方法可以返回结果，但是主线程在获取时是被阻塞，需要等待任务线程返回才能拿到结果
 * @author
 *
 */
public class ThreadPoolWithcallable {

	public static void main(String[] args) throws InterruptedException, ExecutionException {
		ExecutorService pool = Executors.newFixedThreadPool(4); 
		
		for(int i = 0; i < 10; i++){
			Future<String> submit = pool.submit(new Callable<String>(){
				@Override
				public String call() throws Exception {
					//System.out.println("a");
					Thread.sleep(5000);
					return "b--"+Thread.currentThread().getName();
				}			   
			   });
			//从Future中get结果，这个方法是会被阻塞的，一直要等到线程任务返回结果
			System.out.println(submit.get());
		} 
			pool.shutdown();
	}
}
```
在构造函数中的参数4是线程池的大小，你可以随意设置，也可以和cpu的核数量保持一致，获取cpu的核数量int cpuNums = Runtime.getRuntime().availableProcessors();

4. Scheduled Thread Pool : 用来调度即将执行的任务的线程池，可能是不是直接执行, 每隔多久执行一次... 策略型的
  代码：Executors.newScheduledThreadPool()
```
public class TestPool {

	public static void main(String[] args) throws Exception {
		Future<?> submit = null;
		Random random = new Random();
		
		//创建固定数量线程池
//		ExecutorService exec = Executors.newFixedThreadPool(4);
		
		//创建调度线程池
		ScheduledExecutorService exec = Executors.newScheduledThreadPool(4);
		
		//用来记录各线程的返回结果
		ArrayList<Future<?>> results = new ArrayList<Future<?>>();
		
		for (int i = 0; i < 10; i++) {
			//fixedPool提交线程，runnable无返回值，callable有返回值
			/*submit = exec.submit(new TaskRunnable(i));*/
			/*submit = exec.submit(new TaskCallable(i));*/
			
			//对于schedulerPool来说，调用submit提交任务时，跟普通pool效果一致
			/*submit = exec.submit(new TaskCallable(i));*/
			//对于schedulerPool来说，调用schedule提交任务时，则可按延迟，按间隔时长来调度线程的运行
			submit = exec.schedule(new TaskCallable(i), random.nextInt(10), TimeUnit.SECONDS);
			//存储线程执行结果
			results.add(submit);
			
		}
		//打印结果
		for(Future f: results){
			boolean done = f.isDone();
			System.out.println(done?"已完成":"未完成");  //从结果的打印顺序可以看到，即使未完成，也会阻塞等待
			System.out.println("线程返回future结果： " + f.get());
		}
		exec.shutdown();		
	}
}
```
5. Single Thread Scheduled Pool : 只有一个线程，用来调度任务在指定时间执行，代码：Executors.newSingleThreadScheduledExecutor()

## 2.1.2 测试以核数为准还是以线程为准
- 其实是以核数为准的，电脑是4核8线程，设置4线程为好
```
public class Test {
	public static void main(String[] args) {
		int num = Runtime.getRuntime().availableProcessors();
		System.out.println(num);
	}
}
```

## 2.2 java并发包消息队列
### 2.2.1 BlockingQueue操作
BlockingQueue也是java.util.concurrent下的主要用来控制线程同步的工具。
主要的方法是：put、take一对阻塞存取；add、poll一对非阻塞存取。

- 插入:
  1)add(anObject):把anObject加到BlockingQueue里,即如果BlockingQueue可以容纳,则返回true,否则抛出异常,不好
  2)offer(anObject):表示如果可能的话,将anObject加到BlockingQueue里,即如果BlockingQueue可以容纳,则返回true,否则返回false.
  3)put(anObject):把anObject加到BlockingQueue里,如果BlockQueue没有空间,则调用此方法的线程被阻断直到BlockingQueue里面有空间再继续, 有阻塞, 放不进去就等待
- 读取：
  4)poll(time):取走BlockingQueue里排在首位的对象,若不能立即取出,则可以等time参数规定的时间,取不到时返回null; 取不到返回null
  5)take():取走BlockingQueue里排在首位的对象,若BlockingQueue为空,阻断进入等待状态直到Blocking有新的对象被加入为止; 阻塞, 取不到就一直等
- 其他
  int remainingCapacity();返回队列剩余的容量，在队列插入和获取的时候，不要瞎搞，数据可能不准, 不能保证数据的准确性
  boolean remove(Object o); 从队列移除元素，如果存在，即移除一个或者更多，队列改变了返回true
  public boolean contains(Object o); 查看队列是否存在这个元素，存在返回true
  int drainTo(Collection<? super E> c); //移除此队列中所有可用的元素,并将它们添加到给定 collection 中。取出放到集合中
  int drainTo(Collection<? super E> c, int maxElements); 和上面方法的区别在于，指定了移动的数量; 取出指定个数放到集合

### 2.2.2 具体实现类 

BlockingQueue有四个具体的实现类,常用的两种实现类为：

1、ArrayBlockingQueue：一个由数组支持的有界阻塞队列，规定大小的BlockingQueue,其构造函数必须带一个int参数来指明其大小.其所含的对象是以FIFO(先入先出)顺序排序的。

2、LinkedBlockingQueue：大小不定的BlockingQueue,若其构造函数带一个规定大小的参数,生成的BlockingQueue有大小限制,若不带大小参数,所生成的BlockingQueue的大小由Integer.MAX_VALUE来决定.其所含的对象是以FIFO(先入先出)顺序排序的。 
- LinkedBlockingQueue 可以指定容量，也可以不指定，不指定的话，默认最大是Integer.MAX_VALUE,其中主要用到put和take方法，put方法在队列满的时候会阻塞直到有队列成员被消费，take方法在队列空的时候会阻塞，直到有队列成员被放进来。

### 2.3.3 区别
LinkedBlockingQueue和ArrayBlockingQueue区别：
- LinkedBlockingQueue和ArrayBlockingQueue比较起来,它们背后所用的数据结构不一样,导致LinkedBlockingQueue的数据吞吐量要大于ArrayBlockingQueue
- 但在线程数量很大时其性能的可预见性低于ArrayBlockingQueue.



# 3 java动态代理、反射
## 3.1 反射
- 通过反射的方式可以获取class对象中的属性、方法、构造函数等
```
public class MyReflect {
	public String className = null;
	@SuppressWarnings("rawtypes")
	public Class personClass = null;
	/**
	 * 反射Person类
	 * @throws Exception 
	 */
	@Before
	public void init() throws Exception {
		className = "cn.itcast_04_reflect.Person";
		personClass = Class.forName(className);
	}
	/**
	 *获取某个class文件对象
	 */
	@Test
	public void getClassName() throws Exception {
		System.out.println(personClass);
	}
	/**
	 *获取某个class文件对象的另一种方式
	 */
	@Test
	public void getClassName2() throws Exception {
		System.out.println(Person.class);
	}
	/**
	 *创建一个class文件表示的实例对象，底层会调用空参数的构造方法
	 */
	@Test
	public void getNewInstance() throws Exception {
		System.out.println(personClass.newInstance());
	}
	/**
	 *获取非私有的构造函数
	 */
	@SuppressWarnings({ "rawtypes", "unchecked" })
	@Test
	public void getPublicConstructor() throws Exception {
		Constructor  constructor  = personClass.getConstructor(Long.class,String.class);
		Person person = (Person)constructor.newInstance(100L,"zhangsan");
		System.out.println(person.getId());
		System.out.println(person.getName());
	}
	/**
	 *获得私有的构造函数
	 */
	@SuppressWarnings({ "rawtypes", "unchecked" })
	@Test
	public void getPrivateConstructor() throws Exception {
		Constructor con = personClass.getDeclaredConstructor(String.class);
		con.setAccessible(true);//强制取消Java的权限检测
		Person person2 = (Person)con.newInstance("zhangsan");
		System.out.println("**"+person2.getName());
	}
	/**
	 *访问非私有的成员变量
	 */
	@SuppressWarnings({ "rawtypes", "unchecked" })
	@Test
	public void getNotPrivateField() throws Exception {
		Constructor  constructor  = personClass.getConstructor(Long.class,String.class);
		Object obj = constructor.newInstance(100L,"zhangsan");
		
		Field field = personClass.getField("name");
		field.set(obj, "lisi");
		System.out.println(field.get(obj));
	}
	/**
	 *访问私有的成员变量
	 */
	@SuppressWarnings({ "rawtypes", "unchecked" })
	@Test
	public void getPrivateField() throws Exception {
		Constructor  constructor  = personClass.getConstructor(Long.class);
		Object obj = constructor.newInstance(100L);
		
		Field field2 = personClass.getDeclaredField("id");
		field2.setAccessible(true);//强制取消Java的权限检测
		field2.set(obj,10000L);
		System.out.println(field2.get(obj));
	}
	/**
	 *获取非私有的成员函数
	 */
	@SuppressWarnings({ "unchecked" })
	@Test
	public void getNotPrivateMethod() throws Exception {
		System.out.println(personClass.getMethod("toString"));
		
		Object obj = personClass.newInstance();//获取空参的构造函数
		Method toStringMethod = personClass.getMethod("toString");
		Object object = toStringMethod.invoke(obj);
		System.out.println(object);
	}
	/**
	 *获取私有的成员函数
	 */
	@SuppressWarnings("unchecked")
	@Test
	public void getPrivateMethod() throws Exception {
		Object obj = personClass.newInstance();//获取空参的构造函数
		Method method = personClass.getDeclaredMethod("getSomeThing");
		method.setAccessible(true);
		Object value = method.invoke(obj);
		System.out.println(value);

	}
	/**
	 *
	 */
	@Test
	public void otherMethod() throws Exception {
		//当前加载这个class文件的那个类加载器对象
		System.out.println(personClass.getClassLoader());
		//获取某个类实现的所有接口
		Class[] interfaces = personClass.getInterfaces();
		for (Class class1 : interfaces) {
			System.out.println(class1);
		}
		//反射当前这个类的直接父类
		System.out.println(personClass.getGenericSuperclass());
		/**
		 * getResourceAsStream这个方法可以获取到一个输入流，这个输入流会关联到name所表示的那个文件上。
		 */
		//path 不以’/'开头时默认是从此类所在的包下取资源，以’/'开头则是从ClassPath根下获取。其只是通过path构造一个绝对路径，最终还是由ClassLoader获取资源。
		System.out.println(personClass.getResourceAsStream("/log4j.properties"));
		System.out.println(personClass.getResourceAsStream("log4j.properties"));
		
		//判断当前的Class对象表示是否是数组
		System.out.println(personClass.isArray());
		System.out.println(new String[3].getClass().isArray());
		
		//判断当前的Class对象表示是否是枚举类
		System.out.println(personClass.isEnum());
		System.out.println(Class.forName("cn.itcast_04_reflect.City").isEnum());
		
		//判断当前的Class对象表示是否是接口
		System.out.println(personClass.isInterface());
		System.out.println(Class.forName("cn.itcast_04_reflect.TestInterface").isInterface());
	}
}
```

## 3.2 动态代理
### 3.2.1 应用场景
在之前的代码调用阶段，我们用action调用service的方法实现业务即可。
- 由于之前在service中实现的业务可能不能够满足当先客户的要求，需要我们重新修改service中的方法，但是service的方法不只在我们这个模块使用，在其他模块也在调用，其他模块调用的时候，现有的service方法已经能够满足业务需求，所以我们不能只为了我们的业务而修改service，导致其他模块授影响。
- 那怎么办呢？
- 可以通过动态代理的方式，扩展我们的service中的方法实现，使得在原油的方法中增加更多的业务，而不是实际修改service中的方法，这种实现技术就叫做动态代理。
- 动态代理：在不修改原业务的基础上，基于原业务方法，进行重新的扩展，实现新的业务。

### 3.2.2 代理实现流程
1、书写代理类和代理方法，在代理方法中实现代理Proxy.newProxyInstance
2、代理中需要的参数分别为：被代理的类的类加载器soneObjectclass.getClassLoader()，被代理类的所有实现接口new Class[] { Interface.class }，句柄方法new InvocationHandler()
3、在句柄方法中复写invoke方法，invoke方法的输入有3个参数Object proxy（代理类对象）, Method method（被代理类的方法）,Object[] args（被代理类方法的传入参数），在这个方法中，我们可以定制化的开发新的业务。
4、获取代理类，强转成被代理的接口
最后，我们可以像没被代理一样，调用接口的认可方法，方法被调用后，方法名和参数列表将被传入代理类的invoke方法中，进行新业务的逻辑流程
### 3.2.3 demo
- IBoss
```
/**
 * 这是一个业务的接口，这个接口中的业务就是返回衣服的价格
 * @author wilson
 *
 */
public interface IBoss {//接口
	int yifu(String size);
}
```
- Boss
```
/**
 * 实现了卖衣服的接口
 * 自定义了自己的业务，卖裤子
 * @author wilson
 *
 */
public class Boss implements IBoss{
	public int yifu(String size){
		System.err.println("天猫小强旗舰店，老板给客户发快递----衣服型号："+size);
		//这件衣服的价钱，从数据库读取
		return 50;
	}
	public void kuzi(){
		System.err.println("天猫小强旗舰店，老板给客户发快递----裤子");
	}
}
```
- SaleAction(老的业务方法)
```
public class SaleAction {
	/**
	 * 不使用代理，直接调用方法
	 * 方法中规定什么业务，就只能调用什么业务，规定什么返回值，就只能输出什么返回值
	 */
	@Test
	public void saleByBossSelf() throws Exception {
		IBoss boss = new Boss();
		System.out.println("老板自营！");
		int money = boss.yifu("xxl");// 老板自己卖衣服，不需要客服，结果就是没有聊天记录
		System.out.println("衣服成交价：" + money);
	}
}
```
- ProxyBoss
```
public class ProxyBoss {
	/**
	 * 对接口方法进行代理
	 */
	@SuppressWarnings("unchecked")
	public static <T> T getProxy(final int discountCoupon, final Class<?> interfaceClass, final Class<?> implementsClass) throws Exception {
		//被代理的类的类加载器,被代理类的所有实现接口,句柄方法
		return (T) Proxy.newProxyInstance(interfaceClass.getClassLoader(), new Class[] { interfaceClass }, new InvocationHandler() {
			public Object invoke(Object proxy, Method method,Object[] args) throws Throwable {
				Integer returnValue = (Integer) method.invoke(
						implementsClass.newInstance(), args);// 调用原始对象以后返回的值
				return returnValue - discountCoupon;
			}
		});
	}
}
```
- ProxySaleAction
```
/**
 * 什么是动态代理？ 简单的写一个模板接口，剩下的个性化工作，好给动态代理来完成！
 */
public class ProxySaleAction {
	
	/**
	 *使用代理，在这个代理中，只代理了Boss的yifu方法
	 *定制化业务，可以改变原接口的参数、返回值等
	 */
	@Test
	public void saleByProxy() throws Exception {
		IBoss boss = ProxyBoss.getProxy(10,IBoss.class,Boss.class);// 将代理的方法实例化成接口
		//IBoss boss = new Boss();// 将代理的方法实例化成接口
		System.out.println("代理经营！");
		int money = boss.yifu("xxl");// 调用接口的方法，实际上调用方式没有变
		System.out.println("衣服成交价：" + money);
	}
}
```
# 4. socket
## 4.1 demo
- 服务端
- ServiceServer
```
public class ServiceServer {
	public static void main(String[] args) throws Exception {

		// 创建一个serversocket，绑定到本机的8899端口上
		ServerSocket server = new ServerSocket();
		server.bind(new InetSocketAddress("localhost", 8899));

		// 接受客户端的连接请求;accept是一个阻塞方法，会一直等待，到有客户端请求连接才返回
		while (true) {
			Socket socket = server.accept();
			new Thread(new ServiceServerTask(socket)).start();
		}
	}
}
```
- ServiceServerTask
```
public class ServiceServerTask implements Runnable{
	Socket socket ;
	InputStream in=null;
	OutputStream out = null;
	
	public ServiceServerTask(Socket socket) {
		this.socket = socket;
	}

	//业务逻辑：跟客户端进行数据交互
	@Override
	public void run() {
		 try {
			//从socket连接中获取到与client之间的网络通信输入输出流 
			in = socket.getInputStream();
			out = socket.getOutputStream();
			
			
			BufferedReader br = new BufferedReader(new InputStreamReader(in));
			//从网络通信输入流中读取客户端发送过来的数据
			//注意：socketinputstream的读数据的方法都是阻塞的 
			String param = br.readLine();
			
			
			/**
			 * 作业：
			 * 将以下业务调用逻辑写成更加通用的：可以根据客户端发过来的调用类名、调用方法名、调用该参数来灵活调用
			 * 
			 * 《反射》
			 * 
			 */
			
			GetDataServiceImpl getDataServiceImpl = new GetDataServiceImpl();
			String result = getDataServiceImpl.getData(param);
			
			
			//将调用结果写到sokect的输出流中，以发送给客户端
			PrintWriter pw = new PrintWriter(out);
			pw.println(result);
			pw.flush();
			
			
			
		} catch (IOException e) {
			 
			e.printStackTrace();
		}finally{
			try {
				in.close();
				out.close();
				socket.close();
			} catch (IOException e) {
				e.printStackTrace();
			}
			
		}
		
	}
}
```
- GetDataServiceImpl
```
public class GetDataServiceImpl {
	public String getData(String param){
		return "ok-"+param;
	}
}
```
- 客户端
- ServiceClient
```
public class ServiceClient {

	public static void main(String[] args) throws Exception {
		
		/*ServiceIterface service = ProxyUtils.getProxy(ServiceIterface.class,"methodA",hostname,port);
		Result = service.methodA(parameters);*/
		
		// 向服务器发出请求建立连接
		Socket socket = new Socket("localhost", 8899);
		// 从socket中获取输入输出流
		InputStream inputStream = socket.getInputStream();
		OutputStream outputStream = socket.getOutputStream();

		PrintWriter pw = new PrintWriter(outputStream);
		pw.println("hello");
		pw.flush();

		BufferedReader br = new BufferedReader(new InputStreamReader(inputStream));
		String result = br.readLine();
		System.out.println(result);
		
		inputStream.close();
		outputStream.close();
		socket.close();
		
		
	}
}
```
# 5. nio

# 6. rpc

# 7. jvm

有道笔记中的文档 和 jvm相关的资料
jvm内存模型
垃圾回收机制
监控工具
测试案例



类的定义 放在方法区
类中的对象 放到堆内存（Head）
方法中的变量（局部变量）放到栈中（Stack），每个线程都有自己的Stack空间
程序计数器：程序执行到哪一行啦
Native Stack：调本地方法的，在window上调用window的，在linux中调用linux本地方法

