# Race Condition (Состояние гонки)
**Race Condition (состояние гонки)** - это ошибка в многопоточном программировании, когда поведение программы зависит от относительного порядка выполнения потоков, который непредсказуем и может меняться от запуска к запуску.
## Простой пример
```java
pulic class Counter {
	private int count = 0;
	
	public void increment() {
		count++;             // НЕ атомарная операция!
	}
	
	public int getCount() {
		return count;
	}
}
```
### Почему возникает проблема?
Операция `count++` состоит из трех шагов:
1. Чтение значения count из памяти.
2. Увеличение значения на 1
3. Запись нового значения обратно в память
Два потока могут выполнить эти шаги вперемешку:
```java
Поток 1: читает count = 0
Поток 2: читает count = 0
Поток 1: увеличивает до 1
Поток 2: увеличивает до 1
Поток 1: записывает 1
Поток 2: записывает 1      // должно быть 2, но получилось 1
```
### Пример возникновения Race Condition
```java
public class RaceConditionDemo {  
    private static int counter = 0;  
  
    public static void main(String[] args) throws InterruptedException{  
        Runnable task = () -> {  
            for (int i = 0; i < 10000; i++) {  
                counter++;                    // Race Condition здесь  
            }  
        };  
  
        Thread thread1 = new Thread(task);  
        Thread thread2 = new Thread(task);  
  
        thread1.start();  
        thread2.start();  
  
        thread1.join();  
        thread2.join();  
  
        System.out.println("Ожидаемый результат: 20000");  
        System.out.println("Фактический результат: " + counter);  
        // результат может быть меньше из-за Race Condition  
    }  
}
```
## Типы Race Conditions
### 1. Check-then-act
```java
if (map.containsKey(key)) {       // check
	String value = map.get(key);  // act - может быть удалено другим потоком
	System.out.println(value);
}
```
### 2. Read-modify-write
```java
counter++;   // чтение - изменение - запись
```
### 3. Публикация объектов
```java
public class UnsafePublication {
	private SomeObject obj;
	
	public void initialize() {
		obj = new SomeObject(); // Другой поток может увидеть частично созданный объект
	}
}
```
## Реальные примеры проблем
### 1. Банковский счет
```java
pulic class BankAccount {
	private double balance;
	
	public void withdraw(double amount) {
		if (balance >= amount) {               // Check
			balance = balance - amount;        // Act - race condition
		}
	}
}
```
### 2. Кэширование
```java
public class Cache {
	private Map<String, String> cache = new HashMap<>();
	
	public String get(String key) {
		if (!cache.containsKey(key)) {       // Check
			cache.put(key, loadFromDB(key)); // Act - другой поток может добавить
		}
		return chache.get(key);
	}
}
```
## Способы предотвращения race condition
### 1. Синхронизация
```java
public class SafeCounter {
	private int count = 0;
	
	public synchronized void increment() {
		count++;
	}
	
	public int getCount() {
		return count;
	}
}
```
### 2. Atomic классы
```java
import java.util.concurrent.atomic.AtomicInteger;

public class AtomicCounter {
	private AtomicInteger count = new AtomicInteger(0);
	
	public void increment() {
		count.incrementAndGet();    // Атомарная операция
	}
	
	public int getCount() {
		return count.get();
	}
}
```
### 3. Lock объекты
```java
import java.util.concurrent.locks.Lock;
import java.util.concurrent.locks.ReentrantLock;

public class LockCounter {
	private int count = 0;
	private Lock lock = new ReentrantLock();
	
	public void increment() {
		lock.lock();
		try {
			count++;
		} finally {
			lock.unlock();
		}
	}
}
```
### 4. Thread-local переменные
```java
public class ThreadLocalExample {
	private static ThreadLocal<Integer> count = ThreadLoal.withInitial(() -> 0);
	
	public void increment() {
		threadLocalCount.set(threadLocalCount.get() + 1);
	}
}
```
### 5. Неизменяемые объекты
```java
public final class ImmutableValue {
	private final in value;
	
	public ImmutableValue(int value) {
		this.value = value;
	}
	
	public int getValue() {
		return value;
	}
	
	public ImmutableValue add(int valueToAdd) {
		return new ImmutableValue(this.value + valueToAdd);
	}
}
```
## Правильные паттерны
### 1. Double-checked locking (для Singleton)
```java
public class Singleton {
	private static volatile Singleton instance;
	
	public static Singleton getInstance() {
		if (instance == null) {                 // Первая проверка
			synchronized (Singleton.class) {
				if (instance == null) {         // Вторая проверка
					instance = new Singleton();
				}
			}
		}
	}
}
```
### 2. Thread-safe коллекции
**ConcurrentHashMap**, **CopyOnWriteArrayList**
## Обнаружение race condition
### 1. Статический анализ
- **FindBugs**, **PMD**, **SpotBugs**
- **CheckStyle**
### 2. Динамический анализ
- **Thread sanitizer (TSan)**
- Стресс-тестирование
- JUnit с многопоточными тестами
### 3. Визуальный осмотр кода
Ищите:
- Несинхронизированные изменяемые состояния
- Совместно используемые ресурсы
- Операции check-then-act