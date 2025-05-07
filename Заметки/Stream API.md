**Stream API** - это мощный инструмент для обработки коллекций и данных в функциональном стиле, появившийся в Java 8. Он позволяет писать лаконичный и выразительный код для операций фильтрации, преобразования, агрегации и других манипуляций с данными.
# Основные особенности Stream API
- Не изменяет исходную коллекцию (работает с копией данных)
- Ленивые вычисления (многие операции выполняются только при вызове терминальной операции)
- Поддержка параллельной обработки (*parallelStream()*)
- Функциональный стиль (использование *лямбда-выражений* и *method references*)
# Создание Stream (источники)
Stream можно создавать из разных источников:
## 1. Из коллекций
```java
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> stream = list.stream(); // Последовательный поток
Stream<String> pStream = list.parallelStream() // Параллельный поток
```
## 2. Из массива
```java
String[] array = {"a", "b", "c"};
Stream<String> stream = Arrays.stream(array);
```
## 3. Из значений (Stream.of())
```java
Stream<String> stream = Stream.of("a", "b", "c");
```
## 4. Из файлов (Files.lines())
```java
Stream<String> lines = Files.lines(Paths.get("file.txt"));
```
## 5. Бесконечные потоки (Stream.iterate(), Stream.generate())
```java
Stream<Integer> infiniteNumbers = Stream.iterate(0, n -> n + 1); // 0, 1, 2, ...
Stream<Double> randomNumbers = Stream.generate(Math::random); // Случайные числа
```
# Промежуточные операции (Intermediate Operations)
Не выполняются сразу, а формируют "конвейер" операций.

| Операция              | Описание                                                                  | Пример                                    |
| --------------------- | ------------------------------------------------------------------------- | ----------------------------------------- |
| **filter(Predicate)** | Фильтрация элементов  по условию                                          | `stream.filter(s -> s.length() > 3`       |
| **map(Function)**     | Преобразование каждого элемента                                           | `stream.map(String::toUpperCase)`         |
| **flatMap(Function)** | "Разворачивает" вложенные структуры (например, *List\<List> → List*)      | `stream.flatMap(List::stream)`            |
| **distinct()**        | Удаляет дубликаты                                                         | `stream.distinct()`                       |
| **sorted()**          | Сортировка (можно с компаратором)                                         | `stream.sorted(Comparator.reverseOrder()` |
| **peek(Consumer)**    | Позволяет выполнить действие для каждого элемента (например, логирование) | `stream.peek(System.out::println)`        |
| **limit(n)**          | Ограничивает количество элементов                                         | `stream.limit(5)`                         |
| **skip(n)**           | Пропускает первые n элементов                                             | `stream.skip(2)`                          |
# Терминальные операции (Terminal operations)
Запускают выполнение всего конвейера и возвращают результат.

| Операция                                  | Описание                                                  | Пример                                   |
| ----------------------------------------- | --------------------------------------------------------- | ---------------------------------------- |
| **forEach(Consumer)**                     | Выполняет действие для каждого элемента                   | `stream.forEach(System.out::println`     |
| **collect(Collector)**                    | Преобразует поток в коллекцию (*List, Set, Map*, и т.д.)  | `stream.collect(Collectors.toList())`    |
| **toArray()**                             | Преобразует поток в массив                                | `stream.toArray(String[]::new)`          |
| **reduce()**                              | Агрегирует элементы (например, сумма, конкатенация)       | `stream.reduce(0, Integer::sum)`         |
| **min() / max()**                         | Находит минимальный/максимальный элемент (с компаратором) | `stream.max(Comparator.naturalOrder())`  |
| **count()**                               | Возвращает количество элементов                           | `stream.count()`                         |
| **anyMatch() / allMatch() / noneMatch()** | Проверяет, удовлетворяют ли элементы условию              | `stream.anyMatch(s -> s.startWith("a"))` |
| **findFirst() / findAny()**               | Находит первый/любой подходящий элемент                   | `stream.findFirst()`                     |
# Примеры использования
## 1. Фильтрация и преобразование
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie", "Anna");

List<String> filtered = names.stream()
	.filter(name -> name.startWith("A"))    // ["Alice", "Anna"]
	.map(String::toUpperCase)               // ["ALICE", "ANNA"]
	.collect(Collectors.toList());          // Собираем в List
```
## 2. Работа с числами (сумма, среднее)
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);
int sum = numbers.stream().reduce(0, Integer::sum);                 // 15
double avg = numbers.stream().mapToInt(i -> i).average().orElse(0); // 3.0
```
## 3. Группировка (Collectors.groupingBy)
```java
List<String> words = Arrays.asList("apple", "banana", "cherry", "avocado");

Map<Character, List<String>> grouped = words.stream()
	.collect(Collectors.groupingBy(s -> s.charAt(0)));
// {'a' = ["apple", "avocado"], 'b' = ["banana"], 'c' = ["cherry"]}
```
## 4. Параллельная обработка
```java
List<Integer> bigList = /* огромный список */
long count = bigList.parallelStream() // Использует ForkJoinPool
	.filter(i -> i % 2 == 0)
	.count();
```
# Ленивые вычисления (Lazy Evaluation) в Java Stream API
**Ленивые вычисления** - это стратегия, при которой операции не выполняются сразу, а откладываются до момента, когда результат действительно нужен. В Java Stream API большинство промежуточных операций (*intermediate operations*) являются ленивыми (*lazy*), а терминальные (*terminal operations*) - жадными (*eager*).
## Как это работает?
1. Промежуточные операции (например, *filter, map, sorted*) просто добавляются в конвейер, но не выполняются сразу.
2. Терминальная операция, (например, *collect, forEach, count*) запускает весь контейнер, и только тогда данные начинают обрабатываться.
### Пример:
```java
List<String> names = Arrays.asList("Alice", "Bob", "Charlie");

// Промежуточные операции (ленивые)
Stream<String> stream = names.stream()
	.filter(name -> {
		System.out.println("Фильтрация: " + name); // Не выполнится сразу
		return name.length() > 3;
	})
	.map(name -> {
		System.out.println("Маппинг: " + name); // Тоже не выполнится
		return name.toUpperCase();
	});

// Терминальная операция (активирует выполнение)
List<String> result = stream.collect(Collectors.toList())
```
Вывод:
```bash
Фильтрация: Alice
Маппинг: Alice
Фильтрация: Bob
Фильтрация: Charlie
Маппинг: Charlie
```
🖝 Почему `Bob` не прошел в `map`? Потому что `filter` отсеял его (длина ≤ 3), и дальше обработка не пошла.
## Преимущества ленивых вычислений
### 1. Оптимизация производительности
- Не выполняются лишние операции (например, если после *filter* стоит *limit(1)*, то *Stream* обработает только нужные элементы).
- Позволяет избегать обработки больших данных, если результат уже получен (например, *findFirst()*).
### 2. Бесконечные потоки
Ленивость позволяет работать с бесконечными *Stream* (например, *Stream.iterate()*), так как элементы генерируются только по мере необходимости.
```java
Stream<Integer> infiniteNumbers = Stream.iterate(0, n -> n + 1);
infiniteNumbers
	.filter(n -> n > 100)
	.limit(5) // Возьмем только 5 чисел после 100
	.forEach(System.out::println); // 101, 102, 103, 104, 105
```
### 3. Совместимость с параллельными операциями
Ленивые операции позволяют эффективно распределять задачи между потоками при использовании *parallelStream()*.
## Примеры ленивого поведения
### 1. filter + findFirst (короткое замыкание)
```java
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5);

Optional<Integer> firstEven = numbers.stream()
	.filter(n -> {
		System.out.println("Проверка: " + n); // Выполнится только для 1, 2
		return n % 2 == 0;
	})
	.findFirst(); // Нашел 2 и остановился
System.out.println(firstEven.get()); // 2
```
Вывод:
```bash
Проверка: 1
Проверка: 2
2
```
### 2. limit + map (не обрабатывает лишнее)
```java
Stream.of("a", "b", "c", "d")
	.map(s -> {
		System.out.println("Маппинг: " + s); // Только a, b, c
		return s.toUpperCase();
	})
	.limit(3) // Ограничивает до 3 элементов
	.forEach(System.out::println);
```
Вывод:
```bash
Маппинг: a
A
Маппинг: b
B
Маппинг: с
C
```
Элемент `d` не обрабатывается, так как `limit(3)` уже выполнен.
## Когда ленивость не работает?
Ленивые вычисления не применяются для:
- Терминальных операций (*collect, forEach, count*  и др.) - они всегда выполняются немедленно.
- Некоторые оптимизации JVM (например, примитивные стримы *IntStream* могут обрабатываться иначе).
## Вывод
- Ленивые вычисления в *Stream API* позволяют откладывать выполнение операций до вызова терминальной операции.
- **Плюсы:** оптимизация производительности, работа с бесконечными потоками, эффективная параллельная обработка.
- **Минусы:** может быть неочевидным для новичков (например, если забыть вызвать терминальную операцию, поток не выполнится).