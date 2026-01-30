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
			for (int i = 0; i < 1000; i++) {
				counter++;                    // Race Condition здесь
			}
		};
	}
}
```