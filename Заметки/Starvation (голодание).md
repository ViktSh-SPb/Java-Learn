# Starvation (голодание)
Starvation - это ситуация в многопоточном программировании, когда один или несколько потоков не могут получить доступ к общим ресурсам или не могут выполнить работу из-за того, что другие потоки постоянно монополизируют эти ресурсы.
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