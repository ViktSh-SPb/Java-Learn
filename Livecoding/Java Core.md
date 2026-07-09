# Java Core
### Задача 1
#### Условие:
Что произойдет? Что произойдет, если строку инкрементируем несколько раз?
```java
public class Solution {  
  
    public static void main(String[] args) {  
        Integer a = 0;  
        int b = 0;  
        AtomicInteger c = new AtomicInteger(0);  
        String d = "0";  
  
        increment(a);  
        increment(b);  
        increment(c);  
        increment(d);  
  
        System.out.println(a);  
        System.out.println(b);  
        System.out.println(c);  
        System.out.println(d);  
    }  
  
    public static void increment(Integer a) {  
        ++a;  
    }  
  
    public static void increment(int b) {  
        ++b;  
    }  
  
    public static void increment(AtomicInteger c) {  
        c.incrementAndGet();  
    }  
  
    public static void increment(String d) {  
        d = String.valueOf(Integer.parseInt(d) + 1);  
    }  
}
```
#### Решение:
```java
```
### Задача 2
#### Условие:
Что будет выведено?
Атомики являются иммутабельными?
Что внутри AtomicInteger?
```java
public class Main {  
  
    public static void main(String[] args) {  
  
        Map<SomeKey, String> test = new HashMap<>();  
  
        SomeKey key1 = new SomeKey("firstKey");  
        SomeKey key2 = new SomeKey("secondKey");  
  
        test.put(key1, "firstValue");  
        test.put(key2, "secondValue");  
  
        System.out.println(test.get(key1));  
        SomeKey key3 = new SomeKey("secondKey");  
        System.out.println(test.get(key3));  
        key2.value = "";  
        System.out.println(test.get(key2));  
    }  
}  
  
class SomeKey {  
  
    public String value;  
  
    SomeKey(String val) {  
        this.value = val;  
    }  
  
	@Override
    public int hashCode() {  
        return 1;  
    }  
  
	@Override
    public boolean equals(Object obj) {  
        if (obj instanceof SomeKey) {  
            return value.equals(((SomeKey) obj).value);  
        }  
        return false;  
    }  
}
```
#### Решение:
```java
```
### Задача 3
#### Условие:
Все ли тут верно?
```java
public class GenericExceptions {  
  
    static class GenericException<T> extends RuntimeException {  
  
        private T info;  
  
        public GenericException(String message, T info) {  
            super(message);  
            this.info = info;  
        }  
    }  
  
    public static void main(String[] args) {  
        try {  
            doLogic();  
        } catch (Exception unexpectedEx) {  
            handleUnexpectedException(unexpectedEx);  
        } catch (GenericException<DbConnectionInfo> |
				GenericException<NotFoundInfo> |  
                GenericException<HttpInfo> genericEx) {  
            handleGenericException(genericEx);  
        }  
    }  
}
```
#### Решение:
```java
```
### Задача 4
#### Условие:
Насколько корректен этот код?
Какова иерархия исключений?
Если в методе выбрасывается checked исключение, что будет с транзакцией? Что будет с данными, которые были записаны до IOException?
Throwable - это какой тип данных?
```java
public class GenericExceptions {  
  
    static class GenericException extends RuntimeException {  
  
        private Object info;  
  
        public GenericException(String message, Object info) {  
            super(message);  
            this.info = info;  
        }  
    }  
  
    public static void main(String[] args) {  
        try {  
            doLogic();  
        } catch (Exception unexpectedEx) {  
            handleUnexpectedException(unexpectedEx);  
        } catch (GenericException genericEx) {  
            handleGenericException(genericEx);  
        }  
    }  
}
```
```java
public class OomTask {  
  
    static Optional<byte[]> tryAllocateImageBuffer(int size) {  
        try {  
            return Optional.of(new byte[size]);  
        } catch (OutOfMemoryError error) {  
            error.printStackTrace();  
            return Optional.empty();  
        }  
    }  
  
    public static void main(String[] args) {  
        if (tryAllocateImageBuffer(Integer.MAX_VALUE).isPresent()) {  
            System.out.println("Image buffer has been allocated");  
        } else {  
            System.out.println("Image buffer hasn't been allocated");  
        }  
    }  
}
```
#### Решение:
```java
```
### Задача 5
#### Условие:
Выведется ли последняя строчка?
```java
public class Main {  
  
    public static void main(String[] args) {  
        byte[] arr;  
        try {  
            arr = new byte[Integer.MAX_VALUE * Integer.MAX_VALUE * Integer.MAX_VALUE  
                * Integer.MAX_VALUE * Integer.MAX_VALUE];  
        } finally {  
            System.out.println("Finally block");  
        }  
        System.out.println("done");  
    }  
}
```
#### Решение:
```java
```
### Задача 6
#### Условие:
Что выведет следующий код?
```java
public class Main {  
  
    public static void main(String[] args) {  
        fun(4);  
    }  
  
    public static void fun(int x) {  
        if (x > 0) {  
            fun(--x);  
            System.out.print(x + " ");  
            fun(--x);  
        }  
    }  
}
```
### Задача 7
#### Условие:
Почему здесь нежелательно выполнять бизнес-логику в конструкторе? Какие последствия?
```java
@Service  
public class NotificationService {  
  
    private String token;  
  
    @Autowired  
    private ExternalAuthService externalAuthService;  
  
    public NotificationService() {  
        this.token = externalAuthService.getToken();  
        externalAuthService.doSomething();  
    }  
}
```
#### Решение:
```java
```
### Задача 8
#### Условие:
Что будет выведено на экран? Почему?
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);  
numbers.stream()  
    .filter(n -> n % 2 == 0)  
    .map(n -> n * 2);  
System.out.println(numbers);
```
#### Решение:
```java
code
```
### Задача 9
#### Условие:
Что будет напечатано?
```java
List<String> list = Arrays.asList("A", "B", "C");  
list.stream().peek(System.out::println);
```
#### Решение:
```java
code
```
### Задача 10
#### Условие:
Что будет напечатано?
```java
List<Optional<String>> optionals = List.of(Optional.of("foo"), Optional.empty(), Optional.of("bar"));  
List<String> result = optionals.stream()  
    .flatMap(opt -> opt.stream())  
    .collect(Collectors.toList());  
System.out.println(result);
```
#### Решение:
```java
code
```
### Задача 11
#### Условие:
Что произойдет?
```java
public static void main(String[] args) {  
    List<String> list = new ArrayList<>();  
    list.add("A");  
    list.add("B");  
    list.add("C");  
    list.add("D");  
  
    for (String item : list) {  
        if ("B".equals(item)) {  
            list.remove(item);  
        }  
    }  
    System.out.println(list);  
}
```
#### Решение:
```java
code
```
### Задача 12
#### Условие:
Есть ли в следующем коде проблемы? Что будет выведено?
```java
public class Main {  
    public static void main(String[] args) {  
        Map<SomeKey, String> test = new HashMap<>();  
        SomeKey key1 = new SomeKey("firstKey");  
        SomeKey key2 = new SomeKey("secondKey");  
        test.put(key1, "firstValue");  
        test.put(key2, "secondValue");  
        System.out.println(test.get(key1));  
        SomeKey key3 = new SomeKey("secondKey");  
        System.out.println(test.get(key3));  
        key2.value = "";  
        System.out.println(test.get(key2));  
    }  
}  
  
class SomeKey {  
    public String value;  
  
    SomeKey(String val) {  
        this.value = val;  
    }  
  
    public boolean equals(Object obj) {  
        if (obj instanceof SomeKey someKey) {  
            return value.equals(someKey.value);  
        }  
        return false;  
    }  
  
    @Override  
    public int hashCode() {  
        ...  
    }  
}
```
#### Решение:
```java
code
```
### Задача 13
#### Условие:
Будет ли что-то выведено? Если будет, то что?
```java
public class Main {  
    public static void main(String[] args) {  
        List<Integer> ints = Arrays.asList(1, 1);  
        List raw = ints;  
        List<String> strings = raw;  
        System.out.println(Objects.equals(strings.get(0), strings.get(1)));  
    }  
}
```
#### Решение:
```java
code
```
### Задача 14
#### Условие:
Что будет выведено на экран?
```java
public class Solution {  
    public static void main(String[] args) {  
        Integer a = 0;  
        int b = 0;  
        String d = "0";  
  
        increment(a);  
        increment(b);  
        increment(d);  
  
        System.out.println(a);  
        System.out.println(b);  
        System.out.println(d);  
    }  
  
    public static void increment(Integer a) {  
        ++a;  
    }  
  
    public static void increment(int b) {  
        ++b;  
    }  
  
    public static void increment(String d) {  
        d = String.valueOf(Integer.parseInt(d) + 1);  
    }  
}
```
#### Решение:
```java
code
```
### Задача 15
#### Условие:
Как отработает следующий код?
```java
public class Main {  
    public static void main(String[] args) {  
        byte[] arr;  
        try {  
            arr = new byte[Integer.MAX_VALUE Integer.MAX_VALUE Integer.MAX_VALUE];  
        } finally {  
            System.out.println("finally block");  
        }  
        System.out.println("done");  
    }  
}
```
#### Решение:
```java
code
```
### Задача 16
#### Условие:
Есть ли ошибки в следующем коде? Если изменить список на List\<?> fruits, как это повлияет на поведение?
```java
public class Main {  
    public static void main(String[] args) {  
        List<? extends Fruit> fruits = new ArrayList<>();  
        fruits.add(new Apple());  
        fruits.add(new Orange());  
    }  
      
    class Fruit {}  
    class Apple extends Fruit {}  
    class Orange extends Fruit {}  
}
```
#### Решение:
```java
code
```