Конструкция **try-catch-finally**  в Java - это механизм обработки исключений, который позволяет:
- **try** - выполнить код, который может вызвать исключение.
- **catch** - обработать исключение, если оно возникло.
- **finally** - выполнить код в любом случае (даже если исключение не было поймано или было выброшено повторно).
# Синтаксис
```java
try{
	// Код, который может вызвать исключение
} catch (Тип_исключения имя_переменной){
	// Обработка исключения
} finally {
	// Код, который выполнится в любом случае
}
```
# Как это работает?
## 1. Блок try
Содержит код, который может вызвать исключение. Если исключение возникает, выполнение блока **try** прерывается.
## 2. Блок catch
- Ловит исключение указанного типа (или его подклассы).
- Можно указать несколько блоков **catch** для разных типов исключений.
- Если исключение не поймано, оно передается выше по стеку вызовов.
## 3. Блок finally
- Выполняется всегда, даже если:
	- в **try** или **catch** был **return;**
	- в **catch** возникло новое исключение
	- исключение не было поймано
- Часто используется для освобождения ресурсов (закрытие файлов, соединений и т.д.).
# Примеры
## Пример 1. Базовое использование
```java
try {
	int result = 10 / 0; // ArithmeticException
} catch (ArithmeticException e){
	System.out.println("Деление на ноль!");
} finally {
	System.out.println("Это выполнится всегда");
}
```
### Вывод:
```bash
Деление на ноль!
Это выполнится всегда
```
## Пример 2. Несколько catch-блоков
```java
try {
	String str = null;
	System.out.println(str.length()); // NullPointerException
} catch (NullPointerException e) {
	System.out.println ("Null!");
} catch (Exception e) {
	System.out.println ("Другое исключение: " + e);
} finally {
	system.out.println ("Finally!");
}
```
### Вывод:
```bash
Null!
Finally!
```
## Пример 3. finally с return
```java
public static int test() {
	try {
		return 1;
	} catch (Exception e) {
		return 2;
	} finally {
		return 3; // Переопределяет возвращаемое значение!
	}
}
```
### Результат:
Метод вернет 3, а не 1!
⚠️ **return** в **finally** перезаписывает возвращаемые значения из **try** и **catch**. Лучше избегать этого!
## Пример 4. Освобождение ресурсов
```java
FileInputStream file = null;
try {
	file = new FileInputStream ("file.txt");
	// чтение файла
} catch (IOException e) {
	System.out.println ("Ошибка при чтении файла: " + e);
} finally {
	if (file != null) {
		try {
			file.close(); // закрываем файл в любом случае
		} catch (IOException e) {
			System.out.println ("Ошибка при закрытии файла: " + e);
		}
	}
}
```
C **Java 7+** лучше использовать **try-with-resources**:
```java
try (FileInputStream file = new FileInputStream("file.txt")) {
	// Автоматическое закрытие файла
} catch (IOException e) {
 System.out.println ("Ошибка: " + e);
}
```
# Когда использовать?
- **try-catch** - для обработки ожидаемых исключений (например ввод пользователя, работа с сетью, файлами)
- **finally** - для обязательных действий (закрытие ресурсов, откат транзакций)
# Важные нюансы
### 1. Порядок блоков
- Сначала **try**, затем **catch**, потом **finally**
- **catch** и **finally** не могут существовать без **try**
### 2. catch без исключения - ошибка компиляции
```java
try { ... }
catch { ... } // Ошибка!
```
### 3. Исключения в finally переопределяют исключения из try/catch
### 4. try-with-resources (Java 7+) - лучшая альтернатива для работы с ресурсами
# Конструкция try-catch с ресурсами
Конструкция *try-with-resoruces* была введена в *Java 7* для упрощения работы с ресурсами, которые необходимо закрывать после использования (такими как потоки, соединения, и т.д.).
## Основные особенности
1. **Автоматическое закрытие ресурсов:** ресурсы, объявленные в *try*, автоматически закрываются по завершении блока, даже если возникло исключение.
2. **Упрощение кода:** избавляет от необходимости писать блоки *finally* для закрытия ресурсов.
3. **Требование:** ресурсы должны реализовать интерфейс *AutoCloseable* или *Closeable*.
## Синтаксис
```java
try (Объявление_ресурса_1; Объявление_ресурса_2; ...) {
	// Код, работающий с ресурсами
} catch (ExceptionType e) {
	// Обработка исключений
}
```
## Примеры
### 1. Простой пример с FileInputStream
```java
try (FileInputStream fis = new FileInputStream ("file.txt")) {
	int data = fis.read();
	while (data != -1) {
		System.out.print((char) data);
		data = fis.read();
	}
} catch (IOException e) {
	e.printStackTrace();
}
```
### 2. Несколько ресурсов
```java
try (FileInputStream fis = new FileInputStream ("source.txt");
	FileOutputStream fos = new FileOutputStream ("dest.txt")) {
		byte[] buffer = new byte[1024];
		int length;
		while ((length = fis.read(buffer)) > 0) {
			fos.write (buffer, 0, length);
		}
	} catch (IOExcepton e) {
		e.printStackTrace ();
	}
```
## Как это работает
1. Ресурсы объявляются в круглых скобках после *try*.
2. Компилятор неявно добавляет блок *finally*, в котором вызывает метод *close()* для каждого ресурса.
3. Ресурсы закрываются в обратном порядке их объявления.
4. Если возникают исключения как в основном блоке, так и при закрытии, основное исключение добавляется в подавленные исключения.
## Пользовательские ресурсы
Можно создавать свои классы, которые можно использовать в *try-with-resources*, реализовав интерфейс *AutoCloseable*:
```java
class MyResource implements AutoCloseable {
	publuc void doSomething() throws Exception {
		System.out.println ("Doing something");
	}

	@Override
	public void close() throws Excepiton {
		System.out.println ("Resource closed");
	}
}

// Использование
try (MyResource res = new MyResource()) {
	res.doSomething();
} catch (Exception e) {
	e.printStackTrace();
}
```
## Преимущества перед традиционным try-catch-finally
1. Более компактный и читаемый код
2. Автоматическая обработка нескольких ресурсов
3. Лучшая обработка исключений (подавленные исключения)
4. Защита от ошибок при ручном закрытии ресурсов
# Подавленные исключения (Suppressed Exceptions)

**Подавленные исключения** — это механизм в Java, который позволяет сохранять вторичные исключения, возникающие при обработке основного исключения (обычно в блоках `try-with-resources` или `finally`).

## Как это работает

Когда в Java возникает несколько исключений:
1. **Основное исключение** — возникает в блоке `try`
2. **Подавленное исключение** — возникает при закрытии ресурсов (в `close()` методе)

Вместо потери одного из исключений Java сохраняет второе исключение как подавленное к первому.

## Пример с try-with-resources

```java
class Resource implements AutoCloseable {
    public void doWork() throws Exception {
        throw new Exception("Ошибка в работе");
    }
    
    @Override
    public void close() throws Exception {
        throw new Exception("Ошибка при закрытии");
    }
}

public class Main {
    public static void main(String[] args) {
        try (Resource res = new Resource()) {
            res.doWork();
        } catch (Exception e) {
            System.out.println("Поймано исключение: " + e.getMessage());
            
            // Получаем подавленные исключения
            Throwable[] suppressed = e.getSuppressed();
            for (Throwable t : suppressed) {
                System.out.println("Подавленное исключение: " + t.getMessage());
            }
        }
    }
}
```

Вывод:
```
Поймано исключение: Ошибка в работе
Подавленное исключение: Ошибка при закрытии
```

## Ключевые особенности

1. **Доступ через getSuppressed()** — метод класса `Throwable` возвращает массив подавленных исключений
2. **Только в try-with-resources** — автоматическое подавление работает только в этой конструкции
3. **Порядок важен** — если исключение возникает и в `try` и в `finally` без try-with-resources, сохраняется только последнее

## Зачем это нужно?

Без этого механизма:
- При возникновении исключения в `try` и `finally` терялось бы одно из исключений
- Было сложно диагностировать проблемы при закрытии ресурсов
- Не было возможности обработать все ошибки

Подавленные исключения особенно полезны при работе:
- С файловыми операциями
- С сетевыми соединениями
- С базами данных
- Любыми другими ресурсами, требующими обязательного закрытия
## Подавленные исключения
Подавленные исключения - это механизм, который позволяет сохранять вторичные исключения, возникающие при обработке основного исключения (обычно в блоках *try-with-resources* или *finally*).
### Как это работает
Когда возникает несколько исключений:
1. Основное исключение - возникает в блоке *try*
2. Подавленное исключение - возникает при закрытии ресурсов (в `close()` методе).
Вместо потери одного из исключений Java сохраняет второе исключение как подавленное первым.
### Пример с try-with-resources
```java
class Resource implements AutoCloseable {
	public void doWork() throws Exception {
		throw new Exception ("Ошибка в работе");
	}

	@Override
	public void close() throws Exception {
		throw new Exception("Ошибка при закрытии");
	}
}

public class Main {
	public static void main(String[] args) {
		try (Resource res = new Resource()) {
			res.doWork();
		} catch (Exception e) {
			System.out.println("Поймано исключение: " + e.getMessage());
			
			// Получаем подавленные исключения
			Throwable[] suppressed = e.getSuppredded();
			for (Throwable t : suppressed) {
				System.out.println("Подавленное исключение: " + t.getMessage());
			}
		}
	}
}
```
Вывод:
```bash
Поймано исключение: Ошибка в работе
Подавлено исключение: Ошибка при закрытии
```
### Ключевые особенности
1. **Доступ через `getSupposed()`** - метод класса *Throwable* возвращает массив подавленных исключений.
2. **Только в try-with-resources** - автоматическое подавление работает только в этой конструкции.
3. **Порядок важен** - если исключение возникает и в *try*  и в *finally* без *try-with-resources*, сохраняется только последнее.
### Зачем это нужно?
#### Без этого механизма:
- При возникновении исключения в *try* и *finally* терялось бы одно из исключений
- Было бы сложно диагностировать проблемы при закрытии ресурсов
- Не было бы возможности обработать все ошибки
#### Подавленные исключения особенно полезны при работе с:
- Файловыми операциями
- Сетевыми соединениями
- Базами данных
- Любыми другими ресурсами, требующими обязательного закрытия
# Случай с несколькими блоками и порядком обработки
## Общий вид try с несколькими catch-блоками
```java
try {
	//Код, который может выбросить разные исключения
	int[] numbers = {1, 2, 3};
	int result = 10 / numbers[4]; // Может быть ArithmeticException или ArrayIndexOutOfBoundsException
} catch (ArithmeticException e) {
	System.out.println("Ошибка деления: " + e.getMessage());
} catch (ArrayIndexOutOfBoundsException e) {
	System.out.println("Ошибка индекса массива: " + e.getMessage());
} catch (Exception e) {
	// Ловит все остальные исключения
	System.out.println("Общая ошибка: " + e.getMessage());
}
```
## Порядок catch-блоков:
### 1. От частного к общему
Сначала ловятся конкретные исключения (например, *ArithmeticException*), затем их родительские классы (*Exception*).
### 2. Если нарушить порядок, компилятор выдаст ошибку
```java
catch (Exception e) {} // ОШИБКА! Этот блок должен быть последним
catch (ArithmeticException e) {}
```
## Пример с пользовательскими исключениями
```java
class CustomException extendx Exception {}
class ChildCustomException extends CustomException {}

try {
	throw new ChildCustomException();
} catch (ChildCustomException e) {
	System.out.println ("Дочернее исключение");
} catch (CustomException e) {
	System.out.println ("Родительское исключение");
}
```
