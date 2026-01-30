# Race Condition (Состояние гонки)
Race Condition (состояние гонки) - это ошибка в многопоточном программировании, когда поведение программы зависит от относительного порядка выполнения потоков, который непредсказуем и может меняться от запуска к запуску.
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
Операция count++ состоит из трех шагов:
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