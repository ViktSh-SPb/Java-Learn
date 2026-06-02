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
  
    public int hashCode() {  
        return 1;  
    }  
  
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

```