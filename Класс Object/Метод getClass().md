Метод **getClass()** возвращает объект типа **Class**, который содержит метаинформацию о классе объекта, включая:
- Имя класса
- Модификаторы доступа
- Поля, методы и конструкторы
- Родительские классы и интерфейсы
- Аннотации и другие метаданные
### Синтаксис:
```java
public final Class<?> getClass();
```
- метод является **final**, поэтому его нельзя переопределить.
- возвращает объект **Class\<?>**, где **?** означает, что тип класса не известен на этапе компиляции.
### Примеры использования:
1. Получение класса объекта:
```java
String str = "Hello";
Class<?> strClass = str.getClass;
System.out.println(strClass.getName()); //Выведет: java.lang.String
```
2. Проверка типа объекта во время выполнения:
```java
Object obj = "Test";
if(ojb.getClass() == String.class){
	System.out.println("Это строка!");
} else {
	System.out.println("Это не строка!");
}
// Выведет: "Это строка!"
```
3. Получение родительского класса:
```java
Class Parent{}
Class Child extends Parent{}

Child child = new Child();
Class<?> parentClass = child.getClass().getSuperClass();
System.out.println(parentClass.getname()) //Выведет: "Parent"
```
4. Использование в рефлексии
```java
Class<?> clazz = "Example".getClass();
Method[] methods = clazz.getMethods(); //Получаем все публичные методы
for (Method method : methods){
	System.out.println(method.getName());
}
```
### Отличие от .class
- **getClass()** вызывается на объекте и возвращает его класс во время выполнения.
- **.class** используется на имени класса и возвращает **Class** на этапе компиляции.