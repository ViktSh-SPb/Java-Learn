## **PECS = Producer Extends, Consumer Super**
### ✅ Producer — `extends`
Если **читаем** данные из коллекции:
```java
List<? extends Number> numbers = List.of(1, 2, 3);

Number n = numbers.get(0); // OK
// numbers.add(4);        // ❌ нельзя
```
➡ Коллекция **производит** (`produce`) значения.
### ✅ Consumer — `super`
Если **кладём** данные в коллекцию:
```java
List<? super Integer> numbers = new ArrayList<Number>();

numbers.add(10); // OK
numbers.add(20); // OK

Object n = numbers.get(0); // только Object
```
➡ Коллекция **потребляет** (`consume`) значения.
### ❌ Без wildcard — если и читать, и писать
```java
List<Integer> list = new ArrayList<>();

list.add(1);
Integer i = list.get(0);
```
## 🧠 Шпаргалка

|Задача|Что использовать|
|---|---|
|Только читать|`? extends T`|
|Только писать|`? super T`|
|Читать и писать|`T`|
## 💡 Одной строкой
> **Если метод получает — `extends`, если отдаёт — `super`**