# DeadLock (Взаимная блокировка)
DeadLock - это ситуация в многопоточном программировании, когда два или более потоков заблокированы навсегда, ожидая ресурсы, которые удерживают друг друга.
## Классический пример "обедающих философов"
```java
public class DiningPhilosophers {
	private static class Philosopher implements Runnable {
		private final Object leftFork;
		private final Object rightFork;
		
		public Philosopher(Object leftFork, Object rightFork) {
			this.leftFork = leftFork;
			this.rightFork = rightFork;
		}
		
		public void run() {
			while (true) {
				// Думает
				synchronized (leftFork) {
					synchronized (rightFork) {
						// Ест
					}
				}
			}
		}
	}
	
	public static void main(String[] args) {
		Object[] forks = new Object[5];
		for (int i = 0; i < 5; i++) {
			forks[i] = new Object();
		}
		
		for (int i = 0; i < 5; i++) {
			Object leftFork = forks[i];
			Object rightFork = forks[(i+1) % 5];
			new Thread(new Philosopher(leftFork, rightFork)).start();
		}
	}
}
```
## Условия возникновения DeadLock
Для возникновения DeadLock необходимо одновременное выполнение четырех условий:
### 1. Взаимное исключение (mutual exclusion)
- Ресурсы не могут быть разделены
- Только один поток может использовать ресурс одновременно
### 2. Удержание и ожидание (hold and wait)
- Поток удерживает один ресурс и ждет другой
### 3. Отсутствие вытеснения (no preemption)
- Ресурсы нельзя отнять у потока
- Только сам поток может освободить ресурс
### 4. Круговое ожидание (circular wait)
- Поток А ждет ресурс, который у В
- Поток В ждет ресурс, который у А
## Простой пример DeadLock
```java
public class SimpleDeadLock {
	private static final Object lock1 = new Object();
	private static final Object lock2 = new Object();
	
	public static void main(String[] args) {
		Thread thread1 = new Thread(() -> {
			synchronized (lock1) {
				System.out.println("Thread 1: Holding lock 1...");
				try {
					Thread.sleep(100);
				} catch (InterruptedException e) {}
				System.out.println("Thread 1: Waiting for lock 2...");
				synchronized (lock2) {
					System.out.println("Thread 1: Acquired both locks!");
				}
			}
		});
		
		Thread thread2 = new Thread(() -> {
			synchronized (lock2) {
				System.out.println("Thread 2: Holding lock 2...");
				try {
					Thread.sleep(100);
				} catch (InterruptedException e) {}
				System.out.println("Thread 2: Waiting for lock 1...");
				synchronized (lock2) {
					System.out.println("Thread 2: Acquired both locks!");
				}
			}
		});
		
		thread1.start();
		thread2.start();
	}
}
```
## Обнаружение DeadLock
### 1. Jstack utility