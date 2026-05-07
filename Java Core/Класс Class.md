**Класс java.lang.Class** - это один из ключевых классов в Java, который представляет собой метаданные о классах и интерфейсах во время выполнения (*Runtime*), позволяет получать описание их важнейших свойств. Экземпляры этого класса создаются автоматически исполняемой средой Java для каждого типа из числа загруженных классов и используемых интерфейсов. Каждый объект в Java (экземпляр класса, массив, примитив) имеет связанный с ним объект **Class**, который предоставляет информацию о:
- Имени класса
- Модификаторах (public, final, abstract и т.д.)
- Полях
- Методах
- Конструкторах
- Аннотациях
- Суперклассе и интерфейсах
### Особенности:
- У каждого примитива *(int, double*) тоже есть свой *Class* (например `int.class`).
- У массива свой *Class* (например, `String[].class`).
- Класс *Class* является дженериком (`Class<T>`), что позволяет безопасно приводить типы.
## Как получить объект Class?
Есть три основных способа:
1. Через `.class` (если тип известен на этапе компиляции)
```java
Class<String> stringClass = String.class;
Class<Integer> intClass = Integer.class;
Class<Double> arrayClass = double[].class;
```
2. Через `getClass()` (если есть экземпляр объекта)
```java
String str = "Hello";
Class<?> strClass = str.getClass(); // Вернет Class<String>
```
3. Через `Class.forName()` (динамическая загрузка класса по имени)
```java
Class<?> clazz = Class.forName("java.lang.String"); // Полное имя класса
```
**⚠️** Если класс не найден, выбрасывается *ClassNotFoundException*.
## Основные методы класса Class
```java
String getName();                   // Полное имя класса (с пакетом)
String getSimpleName();             // Короткое имя класса (без пакета)
int getModifiers();                 // Модификаторы класса (public, final и т.д.)
Class<?> getSuperClass();           // Родительский класс
Class<?>[] getInterfaces();         // Массив реализуемых интерфейсов
Field getField(String name);        // Поле с указанным именем
Field[] getFields();                // Публичные поля
Method[] getMethods();              // Публичные методы
Constructor<?>[] getConstructors(); // Конструкторы
Field[] getDeclaredFields();        // Все поля
Method[] getDeclaredMethods();      // Все методы
boolean isArray();                  // Проверяет, является ли массивом
boolean isInterface();              // Проверяет, является ли интерфейсом
boolean isPrimitive();              // Проверяет, является ли примитивом
```
## Примеры использования
### Пример 1. Получение информации о классе
```java
Class<?> strClass = String.class;

System.out.println("Имя класса: " + strClass.getName()); // java.lang.String
System.out.println("Простое имя: " + strClass.getSimpleName()); // String
System.out.println("Это интерфейс? " + strClass.isInterface()); // false
System.out.println("Родитель: " + strClass.getSuperClass()); // java.lang.Object
```
### Пример 2. Создание объекта через рефлексию
```java
Class<?> clazz = Class.forName("java.lang.String");
String str = (String) clazz.getConstructor(String.class).newInstance("Hello");
System.out.println(str); // Hello
```
### Пример 3. Получение всех методов класса
```java
Class<?> listClass = List.class;
Method[] methods = listClass.getMethods();

for(Method method : methods) {
	System.out.println(method.getName()); // add, remove, get, size, ...
}
```
## Где используется Class?
- Рефлексия (анализ и изменение структуры классов в Runtime).
- Создание объектов динамически (например в фреймворках Spring, Hibernate).
- Аннотации и обработка их во время выполнения (например, @Test в JUnit).
- Загрузка классов через ClassLoader