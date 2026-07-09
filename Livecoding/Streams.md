# Streams

### Задача 1
#### Условие:
Отсортируйте список строк по длине, по возрастанию:
```java
List<String> items = List.of("pear", "banana", "apple", "kiwi");  
```
#### Решение:
```java
```
### Задача 2
#### Условие:
Подсчитайте, сколько раз каждое слово встречается в списке:
```java
List<String> words = List.of("apple", "banana", "apple", "orange", "banana", "apple");
```
#### Решение:
```java
```
### Задача 3
#### Условие:
Есть список пользователей. Разделите их по возрасту.
```java
record User(String name, int age) {}

List<User> users = List.of(
    new User("Alice", 30),
    new User("Bob", 25),
    new User("Charlie", 30),
    new User("David", 25)
);
```
#### Решение:
```java
```
### Задача 4:
#### Условие:
Найдите второе по величине число в списке:
```java
List<Integer> numbers = List.of(5, 3, 9, 1, 9, 7);
```
#### Решение:
```java

```
### Задача 5
#### Условие:
Есть список имён. Определите, есть ли имя, начинающееся с буквы A.
```java
List<String> names = List.of("Bob", "Charlie", "Alice", "David");
```
#### Решение:
```java

```
### Задача 6
#### Условие:
Из списка чисел оставить только квадраты нечётных чисел:
```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5);
```
#### Решение:
```java

```
### Задача 7
#### Условие:
Есть список слов. Объедините их в одну строку, разделяя запятыми.
```java
List<String> words = List.of("Java", "Stream", "API");
```
#### Решение:
```java

```
### Задача 8
#### Условие:
Дан список слов. Найти все уникальные символы, которые встречаются в этих словах.
```java
List<String> words = List.of("hello", "world");
```
#### Решение:
```java

```
### Задача 9
#### Условие:
Разбейте слова по первой букве:
```java
List<String> words = List.of("apple", "apricot", "banana", "blueberry", "cherry");
```
#### Решение:
```java

```
### Задача 10
#### Условие:
Подсчитайте суммарную длину всех слов в списке:
```java
List<String> words = List.of("Java", "Streams", "Are", "Powerful");
```
#### Решение:
```java

```
### Задача 11
#### Условие:
Получить второго по длине имени пользователя (уникального):
```java
List<String> names = List.of("Anna", "Bob", "Catherine", "Daniel", "Anna", "Eve");
```
### Задача 12
#### Условие:
```java
Stream.of("d2", "a2", "b1", "b3", "c")  
    .map(s -> {  
        System.out.println("map: " + s);  
        return s.toUpperCase();  
    })  
    .anyMatch(s -> {  
        System.out.println("anyMatch: " + s);  
        return s.startsWith("A");  
    });
```
Что будет напечатано в консоль?
#### Решение:
```bash

```
### Задача 13
#### Условие:
Посчитать количество повторов каждой буквы:
```java
String string = "d,d,b,b,b,a,v,s"
```
#### Решение:
```java
```
### Задача 14
#### Условие:
Удалить дубликаты:
```java
String strings[] = {"u", "z", "c", "a", "a", "b"};
```
#### Решение:
```java

```
### Задача 15
#### Условие:
Что будет выведено?
```java
public static void main(String[] args) {  
    List<Integer> list = List.of(5, 2, 6, 1, 3, 4);  
    list.stream()  
        .filter(num -> {  
            System.out.println("Filter - " + num);  
            return num % 2 == 0;  
        })  
        .peek(num -> System.out.println("Peek - " + num))  
        .sorted()  
        .forEach(num -> System.out.println("ForEach - " + num));  
}
```
#### Решение:
```java

```
### Задача 16
#### Условие:
Что будет выведено на экран? Как изменится вывод, если между `.peek(System.out::println)`  и `.filter(s -> s.startsWith("2")) ` добавить `sorted()`?
```java
public class Phones {  
    public static void main(String[] args) {  
        Stream.of(new Phone(1, "1"), new Phone(2, "2"))  
                .map(Object::toString)  
                .peek(System.out::println)  
                .peek(System.out::println)  
                .filter(s -> s.startsWith("2"))  
                .forEach(System.out::println);  
    }  
}  
  
class Phone {  
    private int code;  
    private String number;  
  
    public Phone(int code, String number) {  
        this.code = code;  
        this.number = number;  
    }  
  
    @Override  
    public String toString() {  
        return number;  
    }  
}
```
#### Решение:
```java
code
```
### Задача 17
#### Условие:
Условие
```java
code
```
#### Решение:
```java
code
```
### Задача 18
#### Условие:
Условие
```java
code
```
#### Решение:
```java
code
```
### Задача 19
#### Условие:
Условие
```java
code
```
#### Решение:
```java
code
```
### Задача 20
#### Условие:
Условие
```java
code
```
#### Решение:
```java
code
```
### Задача 21
#### Условие:
Условие
```java
code
```
#### Решение:
```java
code
```