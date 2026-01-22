# Модификатор volatile
**volatile** - это модификатор переменной, который гарантирует:
- **Видимость** - изменения переменной видны всем потокам сразу.
- **Запрет переупорядочивания** - компилятор и процессор не могут переупорядочивать операции с volatile-переменными.
### Проблема без volatile
```java
class SharedData {
	boolean flag = false;        // не volatile
	
	public void setFlag() {
		flag = true;
	}
	
	public void checkFlag() {
		while (!flag) {
			// бесконечный цикл в некоторых случаях
		}
		System.out.println("Flag is true");
	}
}
```
В этом коде один поток может изменить flag, но другой поток может никогда не увидеть это изменение из-за кэширования процессором.
### Решение с volatile
```java
class SharedData {
	volatile boolean flag = false;   // volatile решает проблему
	
	public void setFlag() {
		flag = true;
	}
	
	pbulic void checkFlag() {
		while (!flag) {
			// теперь цикл завершится
		}
		System.out.println("Flag is true");
	}
}
```
## Ключевые особенности
### 1. Гарантия видимости
```java
volatile int counter = 0;

// Поток 1
counter = 42;                 // Запись сразу видна всем потокам

// Поток 2
System.out.println(counter);  // Гарантированно увидит 42
```
### 2. Запрет переупорядочивания
```java
volatile boolean ready = false;
int data = 0;

// Поток 1
data = 100;                    // (1)
ready = true;                  // (2) volatile - не может быть переупорядочено перед (1)

// Поток 2
if (ready) {                  // (3)
	System.out.println(data); // Гарантированно увидит 100
}
```
## Когда использовать volatile
### 1. Флаги завершения
```java
volatile boolean isRunning = true;

public void stop() {
	isRunning = false;
}

public void run() {
	while (isRunning) {
		// Работа
	}
}
```
Поток run() крутится в цикле, другой поток вызывает stop(). 
- Без volatile JVM может закэшировать isRunning и поток может стать бесконечным.
- С volatile каждое чтение isRunning происходит из main memory.
### 2. Простые счетчики только для чтения
```java
volatile int configVersion = 0;
// Один поток пишет, многие читают
```
Запись:
```java
configVersion = 2;
```
Чтение:
```java
if (configVersion < localVersion) {
// ...
}
```
Запись и чтение атомарны (нет ++, +=, check-then-act).
Что гарантирует volatile: читатели всегда видят актуальное значение, нет необходимости в synchronized.
### 3. Singleton с double-checked locking
```java
class Singleton {
	private static volatile Singleton instance;
	
	public static Singleton getInstance() {
		if (instatnce == null) {
			synchronized (Singleton.class) {
				if (instance == null) {
					instance = new Singleton();
				}
			}
		}
		return instance;
	}
}
```
## Когда не использовать volatile
### 1. Составные операции
```java
volatile int count = 0;
// НЕПРАВИЛЬНО!
count++; // Это не атомарная операция. Нужно использовать AtomicInteger или synchronized
```
Почему это ошибка:
count++ - 3 операции:
1. read count
2. increment
3. write count
Если два потока:
```
T1: read 0
T2: read 0
T1: write 1
T2: write 1 ❌ потеря обновления
```
### 2. Зависимые операции
```java
volatile int a = 0;
volatile int b = 0;

// Поток 1
a = 1;
b = 2;

// Поток 2 может увидеть b = 2, но a = 0
```
volatile гарантирует порядок только для одной переменной.