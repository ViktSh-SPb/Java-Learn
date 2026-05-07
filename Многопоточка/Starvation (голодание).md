# Starvation (голодание)
**Starvation** - это ситуация в многопоточном программировании, когда один или несколько потоков не могут получить доступ к общим ресурсам или не могут выполнить работу из-за того, что другие потоки постоянно монополизируют эти ресурсы.
## Ключевые характеристики Starvation
- Поток жив и не заблокирован
- Поток хочет работать, но не может
- Нет прогресса в выполнении работы
- Ресурсы несправедливо распределяются
## Причины возникновения Starvation
- Высокоприоритетные потоки монополизируют ресурсы
- Неправильная настройка планировщика
- "Жадные" потоки не отпускают ресурсы
- Неправильная политика синхронизации
## Примеры Starvation
### 1. Низкоприоритетный поток голодает
```java
public class PriorityStarvation {  
  
    public static void main(String[] args) {  
        Thread highPriorityThread = new Thread(() -> {  
            while (true) {  
                // Монополизирует CPU  
                System.out.println("High priority working...");  
            }  
        });  
          
        Thread lowPriorityThread = new Thread(() -> {  
            while (true) {  
                // Этот поток почти никогда не выполняется  
                System.out.println("Low priority got chance!");  
            }  
        });  
          
        highPriorityThread.setPriority(Thread.MAX_PRIORITY);  
        lowPriorityThread.setPriority(Thread.MIN_PRIORITY);  
          
        highPriorityThread.start();  
        lowPriorityThread.start();  
    }  
}
```
### 2. Starvation из-за synchronized
```java
public class SynchronizedStarvation {  
    public static final Object lock = new Object();  
    public static int counter = 0;  
  
    public static void main(String[] args) {  
        // Жадный поток  
        Thread greedyThread = new Thread(() -> {  
            while (true) {  
                synchronized (lock) {  
                    counter++;  
                    System.out.println("Greedy: " + counter);  
                    // Долгая работа в synchronized блоке  
                    try {  
                        Thread.sleep(1000);  
                    } catch (InterruptedException e) {}  
                }  
            }  
        });  
          
        // "Вежливый" поток  
        Thread politeThread = new Thread(() -> {  
            while (true) {  
                synchronized (lock) {  
                    counter++;  
                    System.out.println("Polite: " + counter);  
                    // Короткая работа  
                }  
                try {  
                    Thread.sleep(10);  
                } catch (InterruptedException e) {}  
            }  
        });  
          
        greedyThread.start();  
        politeThread.start();  
    }  
}
```
## Разница между Starvation и другими проблемами

| Проблема       | Состояние                              | Причина                                   |
| -------------- | -------------------------------------- | ----------------------------------------- |
| **Starvation** | Поток активен, но не может работать    | Несправедливое распределение ресурсов     |
| **DeadLock**   | Потоки заблокированы навсегда          | Взаимное ожидание ресурсов                |
| **LiveLock**   | Потоки активны, но бесполезно работают | Постоянная реакция на действия друг друга |
## Реальные сценарии Starvation
### 1. База данных с неправильными блокировками
```java
public class DatabaseStarvation {  
  
    private final ReentrantLock lock = new ReentrantLock();  
  
    public void longTransaction() {  
        lock.lock();  
        try {  
            // Долгая транзакция (5 минут)  
            Thread.sleep(30000);  
        } catch (InterruptedException e) {  
            Thread.currentThread().interrupt();  
        } finally {  
            lock.unlock();  
        }  
    }  
  
    public void shortTransaction() {  
        // Этот поток будет ждать пока longTransaction завершится  
        lock.lock();  
        try {  
            // Быстрая операция  
        } finally {  
            lock.unlock();  
        }  
    }  
}
```
### 2. Web server с неправильным планированием
```java
public class WebServerStarvation {  
  
    private final ExecutorService executor = Executors.newFixedThreadPool(10);  
  
    public void handleRequest(Request request) {  
        executor.submit(() -> {  
            if (request.isHighPriority()) {  
                // Обработка высокоприоритетного запроса  
                processHighPriority(request);  
            } else {  
                // Низкоприоритетные запросы могут долго ждать  
                processLowPriority(request);  
            }  
        });  
    }  
  
    private void processHighPriority(Request request) {  
        // Долгая обработка  
        try {  
            Thread.sleep(5000);  
        } catch (InterruptedException e) {  
            throw new RuntimeException(e);  
        }  
    }  
  
    private void processLowPriority(Request request) {  
        // Быстрая обработка, но может никогда не начаться  
    }  
}
```
## Способы обнаружения Starvation
### 1. Мониторинг времени ожидания
```java
public class WaitTimeMonitor {  
  
    private final Map<Long, Long> threadWaitStart = new ConcurrentHashMap<>();  
    private final long starvationThreshold = 30000; // 30 секунд  
  
    public void recordWaitStart() {  
        threadWaitStart.put(Thread.currentThread().getId(), System.currentTimeMillis());  
    }  
  
    public void recordWaitEnd() {  
        threadWaitStart.remove(Thread.currentThread().getId());  
    }  
  
    public void checkForStarvation() {  
        long currentTime = System.currentTimeMillis();  
        threadWaitStart.forEach((threadId, startTime) -> {  
            if (currentTime - startTime > starvationThreshold) {  
                System.out.println("Starvation detected for thread: " + threadId);  
            }  
        });  
    }  
}
```
### 2. Статистика выполнения
```java
public class ExecutionStats {  
  
    private final AtomicLong executionCount = new AtomicLong(0);  
    private final AtomicLong waitTime = new AtomicLong(0);  
    private final long threadId = Thread.currentThread().getId();  
  
    public void recordExecution(long duration) {  
        executionCount.incrementAndGet();  
    }  
  
    public void recordWait(long duration) {  
        waitTime.addAndGet(duration);  
    }  
  
    public double getWaitToExecutionRatio() {  
        return (double) waitTime.get() / executionCount.get();  
    }  
}
```
## Способы предотвращения Starvation
### 1. Справедливые блокировки (Fair Lock)
```java
public class FairLockExample {  
	// Используется конструктор с включенным FAIR  
    private final ReentrantLock fairLock = new ReentrantLock(true);
    public void fairMethod(){  
        fairLock.lock();  
        try {  
            // Критическая секция  
        } finally {  
            fairLock.unlock();  
        }  
    }  
}
```
### 2. Использование ReadWriteLock
```java
public class ReadWriteLockExample {  
  
    private final ReadWriteLock rwLock = new ReentrantReadWriteLock(true);  
    private final Lock readLock = rwLock.readLock();  
    private final Lock writeLock = rwLock.writeLock();  
  
    public void readOperation() {  
        readLock.lock();  
        try {  
            // Много читателей могут работать одновременно  
        } finally {  
            readLock.unlock();  
        }  
    }  
  
    public void writeOperation() {  
        writeLock.lock();  
        try {  
            // Только один писатель  
        } finally {  
            writeLock.unlock();  
        }  
    }  
}
```
### 3. Приоритизация с помощью Semaphore
```java
public class PrioritySemaphore {  
  
    private final Semaphore semaphore = new Semaphore(1, true); // FAIR semaphore  
  
    public void highPriorityTask() {  
        if (semaphore.tryAcquire()) {  
            try {  
                // Высокоприоритетная задача  
            } finally {  
                semaphore.release();  
            }  
        } else {  
            // Альтернативная логика  
        }  
    }  
  
    public void lowPriorityTask() {  
        try {  
            semaphore.acquire(); // Будет ждать своей очереди  
            try {  
                // Низкоприоритетная задача  
            } finally {  
                semaphore.release();  
            }  
        } catch (InterruptedException e) {  
            Thread.currentThread().interrupt();  
        }  
    }  
}
```
### 4. Использование условий с таймаутами
```java
public class TimeoutExample {  
  
    private final Lock lock = new ReentrantLock(true);  
    private final Condition condition = lock.newCondition();  
  
    public boolean tryDoWork(long timeout, TimeUnit unit) {  
        lock.lock();  
        try {  
            if (condition.await(timeout, unit)) {  
                // Выполнить работу  
                return true;  
            } else {  
                // Таймаут - возможно Starvation  
                return false;  
            }  
        } catch (InterruptedException e) {  
            Thread.currentThread().interrupt();  
            return false;  
        } finally {  
            lock.unlock();  
        }  
    }  
}
```
### 5. Работа с Thread Pool
```java
public class FairThreadPool {  
  
    private final ExecutorService executor = new ThreadPoolExecutor(  
        10, // Минимальное число потоков  
        10, // Максимальное число потоков (равно минимальному => пул фиксированный)  
        0L, // Время жизни лишних потоков (здесь не используется, т.к. пул фиксированный)  
        TimeUnit.MILLISECONDS, // Единица измерения времени жизни  
        new LinkedBlockingQueue<>(100), // Очередь задач (до 100 задач в ожидании)  
        Executors.defaultThreadFactory(), // Фабрика потоков (создает обычные потоки)  
        new ThreadPoolExecutor.CallerRunsPolicy() // Политика при перевыполнении: задача выполняется в вызывающем потоке  
    );  
  
    public void submitTasks(List<Runnable> tasks) {  
        for (Runnable task : tasks) {  
            executor.submit(task);  
        }  
    }  
}
```
## Практические примеры решения
### 1. Справедливый доступ к ресурсу
```java
public class FairResourceAccess {  
  
    private final ReentrantLock fairLock = new ReentrantLock(true);  
  
    public void accessResource(String threadName) {  
        System.out.println(threadName + " waiting for lock");  
        fairLock.lock();  
        try {  
            System.out.println(threadName + " acquired lock");  
            // Имитация работы  
            Thread.sleep(1000);  
        } catch (InterruptedException e) {  
            Thread.currentThread().interrupt();  
        } finally {  
            fairLock.unlock();  
            System.out.println(threadName + " released lock");  
        }  
    }  
  
    public static void main(String[] args) {  
        FairResourceAccess resource = new FairResourceAccess();  
  
        // Создаем несколько потоков  
        for (int i = 0; i < 5; i++) {  
            final String name = "Thread-" + i;  
            new Thread(() -> {  
                while (true) {  
                    resource.accessResource(name);  
                }  
            }).start();  
        }  
    }  
}
```
### 2. Приоритетная обработка с fairness
```java
public class PriorityWithFairness {  
  
    private final ReentrantLock lock = new ReentrantLock(true);  
    private final Condition highPriorityCondition = lock.newCondition();  
    private final Condition normalPriorityCondition = lock.newCondition();  
    private boolean highPriorityWorking = false;  
  
    public void highPriorityWork() {  
        lock.lock();  
        try {  
            while (highPriorityWorking) {  
                highPriorityCondition.await();  
            }  
            highPriorityWorking = true;  
  
            //Выполнение высокоприоритетной работы  
            System.out.println("High priority work started");  
            Thread.sleep(500);  
            System.out.println("High priority work completed");  
        } catch (InterruptedException e) {  
            Thread.currentThread().interrupt();  
        } finally {  
            highPriorityWorking = false;  
            normalPriorityCondition.signalAll();  
            lock.unlock();  
        }  
    }  
  
    public void normalPriorityWork() {  
        lock.lock();  
        try {  
            while (highPriorityWorking) {  
                normalPriorityCondition.await();  
            }  
  
            //Выполнение обычной работы  
            System.out.println("Normal priority work started");  
            Thread.sleep(100);  
            System.out.println("Normal priority work completed");  
        } catch (InterruptedException e) {  
            Thread.currentThread().interrupt();  
        } finally {  
            lock.unlock();  
        }  
    }  
}
```
## Инструменты для диагностики
### 1. JStack и JVisualVM
- Анализ состояния потоков
- Выявление потоков в состоянии waiting
### 2. Профилировщики производительности
- Анализ времени ожидания
- Выявление "горячих" критических секций
### 3. Кастомные метрики
```java
public class StarvationDetector {  
  
    private final Map<String, Long> lastExecutionTime = new ConcurrentHashMap<>();  
    private final long detectionThreshold = 60000; // 1 минута  
  
    public void recordExecution(String threadName) {  
        lastExecutionTime.put(threadName, System.currentTimeMillis());  
    }  
  
    public void checkForStarvation() {  
        long currentTime = System.currentTimeMillis();  
        lastExecutionTime.forEach((threadName, lastTime) -> {  
            if (currentTime - lastTime > detectionThreshold) {  
                System.out.println("Possible starvation: " + threadName);  
            }  
        });  
    }  
}
```