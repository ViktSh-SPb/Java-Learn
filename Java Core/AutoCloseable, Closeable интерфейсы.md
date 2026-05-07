# Общее назначение
Оба интерфейса предназначены для работы с ресурсами, которые требуют освобождения после использования (например, файлы, сетевые соединения, базы данных).
## Closeable
Интерфейс **Closeable** появился в Java 5.
```java
public interface Closeable extends AutoCloseable {
	void close() throws IOException;
}
```
### Особенности:
- Более старый интерфейс, изначально созданный для операция ввода-вывода.
- Метод *close()* может выбрасывать только *IOException*.
- Повторные вызовы *close()* должны быть безопасны (идемпотентны).
- Реализации обычно используются для потоков ввода-вывода *(InputStream*, *OutputStream* и т.д.).
## AutoCloseable
Интерфейс **AutoCloseable** появился в Java 7
```java
public interface AutoCloseable {
	void close() throws Exception;
}
```
### Особенности:
- Более новый и общий интерфейс.
- Метод *close()* может выбрасывать любое исключение (*Exception*).
- Предназначен для использования с *try-with-resources*.
- Используется для более широкого круга ресурсов (не только *IO*)
## try-with resources
С Java 7 появилась конструкция *try-with-resources*, которая автоматически вызывает метод close()
```java
try (BufferedReader br = new BufferedReader(new FileReader("file.txt"))) {
	// Работа с ресурсом
} // Здесь br.close() вызовется автоматически
```
## Различия:
1. **AutoCloseable** более общий, **Closeable** ориентирован на *IO*
2. **Closeable.close()** бросает только *IOException*, **AutoCloseable.close()** - любое *Exception*
3. **Closeable** требует идемпотентности, **Autocloseable** - нет (но, рекомендуется)
4. Все классы, реализующие **Closeable**, автоматически совместимы с *try-with-resources* (т.к. **Closeable** наследует **AutoCloseable**)
## Рекомендации по использованию:
1. Для новых классов, работающих с ресурсами, лучше реализовать **AutoCloseable**
2. Старые классы, работающие с *IO*, обычно реализуют **Closeable**
3. Всегда используйте *try-with-resources* вместо ручного вызова *close()*
4. Реализуя *close()*, делайте его идемпотентным (безопасным для многократного вызова)