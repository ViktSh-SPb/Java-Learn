# DeadLock (Взаимная блокировка)
**DeadLock** - это ситуация в многопоточном программировании, когда два или более потоков заблокированы навсегда, ожидая ресурсы, которые удерживают друг друга.
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
Для возникновения **DeadLock** необходимо одновременное выполнение четырех условий:
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
**Jstack** - это утилита из *JDK* для диагностики потоков *JVM*
#### Что делает
1. Снимает *thread dump* (дамп всех потоков *JVM*)
2. Показывает:
	- Состояние каждого потока (`RUNNABLE, BLOCKED, WAITING, TIMED_WAITING`)
	- stack trace каждого потока
	- кто держит монитор (locked <...>)
	- deadlocks
#### Зачем используют
- JVM "висит", но CPU ≈ 0
- Приложение не отвечает
- Подозрение на deadlock
- Потоки заблокированы на synchronized
- Анализ проблем в проде (без перезапуска)
#### Примеры использования
```bash
# 1.  Узнать PID Java-процесса
jps

# 2. Снять дамп
jstack <pid>

# Или сразу в файл
jstack <pid> threaddump.txt

# Подробная информация о локах
jstack -l <pid>

# Принудительно (если процесс завис)
jstack -F <pid>
```
#### Что искать в дампе
- **BLOCKED** → борьба за synchronized
- **WAITING / TIMED_WAITING** → wait, sleep, park
- **Одинаковые stack trace** → возможный deadlock
- **Found one Java-level deadlock** → обнаружен deadlock
#### Когда НЕ поможет
- **Утечки памяти** → jmap
- **Высокий CPU** → jstack + top
- **GC-проблемы** → jstat, GC-логи
### 2. JConsole или VisualVM
Показывают информацию о потоках и блокировках
#### JConsole
- Встроенный GUI-инструмент *JDK*
- Подключается к *JVM* по *JMX*
- Вкладка **Threads** → кнопка **Detect Deadlock**
- Показывает, какие потоки и на каких мониторах зависли
- Подходит для быстрой проверки (локально / тест)
#### VisualVM
- Более мощный GUI-инструмент
- **Threads → Deadlock** (автоматически подсвечивает)
- Показывает граф зависимостей потоков
- Можно делать thread dump, анализировать историю
- Удобнее для глубокого анализа
### 3. Программное обнаружение
```java
ThreadMXBean bean = ManagementFactory.getThreadMXBean();
long[] threadIds = bean.findDeadlockedThreads();
if (threadIds != null) {
	System.out.println("Deadlock detected!");
}
```
**`ThreadMXBean`** — это *JMX-интерфейс* для мониторинга и управления потоками *JVM*.
#### Что он даёт
Через **ThreadMXBean JVM** позволяет заглянуть внутрь потоков во время работы:
- список всех потоков
- состояние потоков (`RUNNABLE`, `BLOCKED`, `WAITING`…)
- stack trace потоков
- кто держит какие локи
- обнаружение deadlock
- CPU-время потоков (если включено)
#### Откуда он берётся
`ThreadMXBean bean = ManagementFactory.getThreadMXBean();`
`ManagementFactory` — фабрика стандартных **MXBean’ов JVM**  
`ThreadMXBean` — один из них.
#### Пример возможностей
```java
bean.getThreadCount();          // сколько потоков
bean.getAllThreadIds();         // ID всех потоков
bean.getThreadInfo(id);         // инфо о конкретном потоке
bean.findDeadlockedThreads();   // поиск deadlock
```
## Способы предотвращения DeadLock
### 1. Упорядочивание блокировок
```java
public class BankAccount {
	private int balance;
	private final Object lock = new Object();
	
	public BankAccount(int balance) {
		this.balance = balance;
	}
	
	public void deposit(int amount) {
		synchronized (lock) {
			balance += amount;
		}
	}
	
	public void withdraw(int amount) {
		synchronized (lock) {
			balance -= amount;
		}
	}
	
	public int getBanalce() {
		synchronized (lock) {
			return balance;
		}
	}
	
	public Object getLock() {
		return lock;
	}
	
	public static void transfer(BankAccount a, BankAccount b, int amount) {
		Object first = getFirstLock(a.getLock(), b.getLock());
		Object second = getSecondLock(a.getLock(), b.getLock());
		
		synchronized (first) {
			synchronized (second) {
				if (a.getBalance() >= amount) {
					a.withdraw(amount);
					b.deposit(amount);
					System.out.println("Transferred" + amount);
				}
			}
		}
	}
	
	private static Object getFirstLock(Object a, Object b) {
		return System.identityHashCode(a) < System.identityHashCode(b) ? a : b;
	}
	
	private static Object getSecondLock(Object a, Object b) {
		return System.identityHashCode(a) < System.identityHashCode(b) ? b : a;
	}
}
```
- Поток 1 вызывает: `transfer(account1, account2, 100)`
- Поток 2 вызывает: `transfer(account2, account1, 50)`
Если бы мы просто писали:
```java
synchronized(a.getLock()) {
	synchronized(b.getLock()){ ... }
}
```
потоки могли бы захватывать локи в разном порядке. Возник бы DeadLock.
Используя `getFirstLock` / `getSecondLock` для любых двух счетов порядок локов фиксирован по *identityHashCode*. DeadLock невозможен.
### 2. Использование tryLock с таймаутом
```java
public class AvoidDeadlock {
	// Два независимых замка. Каждый защищает свой ресурс. ReentrantLock дает больше    контроля, чем synchronized (таймауты, попытки, прерывания)
	private final Lock lock1 = new ReentrantLock();
	private final Lock lock2 = new ReentrantLock();
	
	public void method1() {
		// Бесконечный цикл. Повторяется, пока не получит 2 замка и не дойдет до return
		while (true) {
			// Попытка захватить lock1. Не блокируемся, если замок занят. Если не получилось, идем в следующую итерацию цикла.
			if (lock1.tryLock()) {
				try {
					// Пытаемся получить lock2. Ждем не более 100мс. Если не получилось, считаем попытку неудачной
					if (lock2.tryLock(100, TimeUnit.MILLISECONDS)) {
						try {
							// Работа с обоими ресурсами
							return;
						// finally гарантирует освобождение замков.
						} finally {
							lock2.unlock();
						}
					}
				} catch (InterruptedException e) {
					Thread.currentThread().interrupt();
				} finally {
					lock1.unlock();
				}
			}
		}
	}
}
```
### 3. Использование ReadWriteLock
```java
ReadWriteLock rwLock = new ReentrantReadWriteLock();

// Много читателей одновременно
public String read() {
	rwLock.readLock().lock();
	try {
		return data;
	} finally {
		rwLock.readLock().unlock();
	}
}

// Только один писатель
public void write(String newData) {
	rwLock.writeLock().lock();
	try {
		data = newData;
	} finally {
		rwLock.writeLock().unlock();
	}
}
```
### 4. Использование единой блокировки
```java
public class SingleLock {
	private final Object globalLock = new Object();
	private Resource resource1;
	private Resource resource2;
	
	public void method() {
		synchronized (globalLock) {
			// Работа с resource1 и resource2
		}
	}
}
```
### 5. Отказ от блокировок
- Использование *Concurrent* коллекций
```java
Map<String, String> map = new ConcurrentHashMap<>();
```
- Использование атомарных операций
```java
AtomicInteger counter = new AtomicInteger(0);
```