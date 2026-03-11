# LiveLock (Динамическая взаимная блокировка)
**Livelock** - это ситуация в многопоточном программировании, когда потоки не заблокированы, но не могут прогрессировать, потому что постоянно реагируют на действия друг друга, не выполняя полезной работы.
## Ключевые характеристики LiveLock
- Потоки активны и не заблокированы
- Потоки реагируют на изменения состояния
- Нет прогресса в выполнении работы
- Напоминает бесконечный цикл вежливости
## Классическая аналогия
Два человека встречаются в узком корридоре:
- Один двигается влево, другой вправо - все еще блокировка
- Оба двигаются в одну сторону - снова блокировка
- Процесс повторяется бесконечно
## Пример LiveLock
```java
public class LiveLockExample {  
  
    static class Spoon {  
  
        private Dinner owner;  
  
        public Spoon(Dinner d) {  
            owner = d;  
        }  
  
        public synchronized void setOwner(Dinner d) {  
            owner = d;  
        }  
  
        public synchronized Dinner getOwner() {  
            return owner;  
        }  
  
        public synchronized void use() {  
            System.out.println(owner.name + " is eating!");  
        }  
    }  
  
    static class Dinner {  
  
        private String name;  
        private boolean isHungry;  
  
        public Dinner(String n) {  
            name = n;  
            isHungry = true;  
        }  
  
        public void eatWith(Spoon spoon, Dinner spouce) {  
            while (isHungry && !Thread.currentThread().isInterrupted()) {  
                // Если ложка не у меня, жду  
                if (spoon.getOwner() != this) {  
                    try {  
                        Thread.sleep(1);  
                    } catch (InterruptedException e) {  
                        break;  
                    }  
                }  
  
                // Если супруг голоден, отдаю ложку  
                if (spouce.isHungry) {  
                    System.out.println(name + ": You eat first, " + spouce.name);  
                    spoon.setOwner(spouce);  
                    continue;  
                }  
  
                // Ем  
                spoon.use();  
                isHungry = false;  
                System.out.println(name + "I'm done, " + spouce.name);  
                spoon.setOwner(spouce);  
            }  
        }  
    }  
  
    public static void main(String[] args) {  
        final Dinner husband = new Dinner("Husband");  
        final Dinner wife = new Dinner("Wife");  
        final Spoon spoon = new Spoon(husband);  
  
        Thread husbandThread = new Thread(() -> {  
           husband.eatWith(spoon, wife);  
        });  
  
        Thread wifeThread = new Thread(() -> {  
            wife.eatWith(spoon, husband);  
        });  
  
        husbandThread.start();  
        wifeThread.start();  
  
        // Через время остановим, чтобы не бежало вечно  
        try {  
            Thread.sleep(5000);  
            husbandThread.interrupt();  
            wifeThread.interrupt();  
            System.out.println("LiveLock detected! Thread interrupted.");  
        } catch (InterruptedException e) {  
            e.printStackTrace();  
        }  
    }  
}
```
## Разница между DeadLock и LiveLock

| Аспект            | DeadLock          | LiveLock                     |
| ----------------- | ----------------- | ---------------------------- |
| Состояние потоков | Заблокированы     | Активны, но не прогрессируют |
| Использование CPU | Нет               | Да (100% загрузка)           |
| Поведение         | Ничего не делают  | Бесполезная работа           |
| Причина           | Ожидание ресурсов | Чрезмерная "вежливость"      |
## Реальные сценарии LiveLock
### 1. Система с retry логикой
```java
public class RetryLivelock {
	private int resource = 0;
	private final Object lock = new Object();
	
	public void worker1() {
		while (true) {
			synchronized (lock) {
				if (resoruce == 0) {
					System.out.println("Worker1: Resource is 0, waiting...");
					try {
						Thread.sleep(100);
					} catch (InterruptedException e) {}
					continue;
				}
				// Работа с ресурсом
				break;
			}
		}
	}
	
	public void worker2() {
		while (true) {
			synchronized (lock) {
				if (resource != 0) {
					System.out.println("Worker2: Resource not 0, waiting...");
					try {
						Thread.sleep(100);
					} catch (InterruptedException e) {}
					continue;
				}
				// Установка ресурса
				resource = 1;
				break;
			}
		}
	}
}
```
### 2. Retry с откатом
```java
public class RetryLivelockExample {

    private final AtomicInteger value = new AtomicInteger(0);

    public void worker() {
        while (true) {
            int current = value.get();

            if (value.compareAndSet(current, current + 1)) {
                System.out.println(Thread.currentThread().getName() + " updated");
                break;
            } else {
                System.out.println(Thread.currentThread().getName() + " retry");
                // слишком агрессивный retry без backoff
            }
        }
    }
}
```
Если оба потока:
- читают одно значение
- одновременно пытаются обновить
- оба проигрывают CAS
- оба мгновенно повторяют
Получается активная борьба без прогресса.
В реальных системах это решают:
- backoff
- random delay
- exponential retry
## Способы обнаружения LiveLock
### 1. Мониторинг прогресса
```java
public class ProgressMonitor {
	private long lastProgressTime = System.CurrentTimeMillis();
	private static final long TIMEOUT = 5000; // 5 секунд
	
	public void checkProgress() {
		if (System.currentTimeMillis() - lastProgressTime > TIMEOUT) {
			System.out.println("Possible livelock detected!");
			// Логика восстановления
		}
	}
	
	public void recordProgress() {
		lastProgressTime = System.currentTimeMillis();
	}
}
```
### 2. Счетчики операций
```java
public class OperationCounter {
	private final AtomicLong operationCount = new AtomicLong(0);
	private long lastCount = 0;
	private static final long CHECK_INTERVAL = 1000;
	
	public void monitor() {
		ScheduledExecutorService scheduler = Executors.newScheduledThreadPool(1);
		scheduler.scheduleAtFixedRate(() -> {
			long currentCount = operationCount.get();
			if (currentCount == lastCount) {
				System.out.println("No progress - possible livelock");
			}
			lastCount = currentCount;
		}, CHECK_INTERVAL, CHECK_INTERVAL, TimeUnit.MILLISECONDS);
	}
	
	public void increment() {
		operationCount.incrementAndGet();
	}
}
```
## Способы предотвращения LiveLock
### 1. Рандомизация времени ожидания
```java
public class RandomizedBackoff {
	private final Random random = new Random();
	
	public void performOperation() {
		int attempts = 0;
		while (true) {
			if (tryOperation()) {
				return; // Успех
			}
			
			// Экспоненциальная рандомизация backoff
			long delay = (long) (Math.pow(2, attempts) * random.nextDouble());
			try {
				Thread.sleep(Math.min(delay, 1000)); // Максимум 1 секунда
			} catch (InterruptedException e) {
				Thread.currentThread().interrupt();
				break;
			}
			attempts++;
		}
	}
	
	private boolean tryOperation() {
		// Попытка выполнить операцию
		return false; // Заглушка
	}
}
```
### 2. Ограничение количества попыток
```java
public class LimitedAttempts {
	private static final int MAX_ATTEMPTS = 10;
	
	public boolean executeWithRetry() {
		int attempts = 0;
		while (attempts < MAX_ATTEMPTS) {
			if (tryExecute()) {
				return true;
			}
			attempts++;
			try {
				Thread.sleep(100 * attempts);
			} catch (InterruptedException e) {
				Thread.currentThread().interrupt();
				break;
			}
		}
		return false;
	}
}
```
### 3. Приоритизация потоков
```java
public class PrioritizedExecution {
	private final AtomicBoolean highPriorityActive = new AtomicBoolean(false);
	
	public void highPriorityTask() {
		highPriorityActive.set(true);
		try {
			// Выполнение высокоприоритетной задачи
		} finally {
			highPriorityActive.set(false);
		}
	}
	
	public void lowPriorityTask() {
		while (highPriorityActive.get()) {
			// Ждем завершения высокоприоритетной задачи
			try {
				Thread.sleep(50);
			} catch (InterruptedException e) {
				break;
			}
			// Выполняем низкоприоритетную задачу
		}
	}
}
```
### 4. Использование таймаутов
```java
public class TimeoutExecution {
	public void executeWithTimeout() {
		ExecutorService executor = Executors.newSingleThreadExecutor();
		Future<?> future = executor.submit(() -> {
			// Код, который может привести к Livelock
		});
		try {
			future.get(5, TimeUnit.SECONDS); // Таймаут 5 секунд
		} catch (TimeoutException e) {
			future.cancel(true);
			System.out.println("Operation timed out - possible livelock");
		} catch (Exception e) {
			// Обработка других исключений
		} finally {
			executor.shutdown();
		}
	}
}
```
## Практический пример решения
### Исходная проблема с "вежливыми" философами
```java
public class FixedPhilosopher implements Runnable {  
  
    private final Spoon spoon;  
    private final Spoon neighbourSpoon;  
    private final String name;  
    private final Random random = new Random();  
    private int failedAttempts = 0;  
  
    public FixedPhilosopher(Spoon spoon, Spoon neighbourSpoon, String name) {  
        this.spoon = spoon;  
        this.neighbourSpoon = neighbourSpoon;  
        this.name = name;  
    }  
  
    @Override  
    public void run() {  
        while (true) {  
            think();  
            eat();  
        }  
    }  
  
    private void think() {  
        System.out.println(name + " is thinking");  
        try {  
            Thread.sleep(random.nextInt(1000));  
        } catch (InterruptedException e) {}  
    }  
  
    private void eat() {  
        // После нескольких неудач становимся "эгоистичными"  
        if (failedAttempts > 3) {  
            synchronized (spoon) {  
                synchronized (neighbourSpoon) {  
                    System.out.println(name + " is eating (selfish mode)");  
                    failedAttempts = 0; // Сброс счетчика  
                }  
            }  
            return;  
        }  
  
        // Обычный режим - вежливый  
        if (spoon.tryAcquire(100, TimeUnit.MICROSECONDS)) {  
            try {  
                if (neighbourSpoon.tryAcquire(100, TimeUnit.MICROSECONDS)) {  
                    try {  
                        System.out.println(name + " is eating");  
                        failedAttempts = 0; // Успех - сброс счетчика  
                    } finally {  
                        neighbourSpoon.release();  
                    }  
                } else {  
                    failedAttempts++;  
                }  
            } finally {  
                spoon.release();  
            }  
        } else {  
            failedAttempts++;  
        }  
    }  
}
```
## Лучшие практики предотвращения LiveLock
1. ✅Используйте рандомизированные backoff стратегии
2. ✅Ограничивайте количество повторных попыток
3. ✅Вводите приоритеты для критических операций
4. ✅Используйте таймауты для всех операций ожидания
5. ✅Мониторьте прогресс выполнения операций
6. ✅Избегайте симметричного поведения потоков
7. ✅Проектируйте систему с асимметричными ролями
## Инструменты для диагностики
### 1. JStack и JVisualVM
- Анализ состояния потоков
- Выявление активных но не прогрессирующих потоков
### 2. Профилировщики CPU
- Показывают методы, которые потребляют CPU без результата
### 3. Кастомные метрики