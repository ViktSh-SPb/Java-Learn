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
# Parallel Stream
**Parallel Stream** - это механизм **Java Stream API** для параллельной обработки данных, позволяющий автоматически распределять операции между несколькими потоками (ядрами CPU) для ускорения вычислений.
## Основные особенности
- **Автоматическое распараллеливание** (использует ForkJoinPool под капотом).
- **Ускоряет обработку больших данных** (но может замедлять работу на малых коллекциях из-за накладных расходов).
- **Потокобезопасность не гарантируется** (если используются небезопасные операции или изменяемые состояния).
- **Порядок элементов может нарушаться** (если не используются forEachOrdered).
## Как создать Parallel Stream?
Есть два способа:
### 1. Из коллекции через parallelStream()
```java
List<String> list = Arrays.asList("a", "b", "c");
Stream<String> parallelStream = list.parallelStream(); // Параллельный поток
```
### 2. Преобразовать обычный Stream в параллельный через parallel()
```java
Stream<String> stream = Stream.of("a", "b", "c");
Stream<String> parallelStream = stream.parallel(); // Делаем поток параллельным
```
## Как работает Parallel Stream?
1. Данные разбиваются на части (*splitting*)
2. Каждая часть обрабатывается в отдельном потоке (*ForkJoinPool.commonPool()*)
3. Результаты объединяются (*combining*).
## Пример:
```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

// Последовательный поток (обычный Stream)
long seqTime = System.currentTimeMillis();
numbers.stream()
	.map(n -> n * 2)
	.forEach(System.out.println);
System.out.println("Sequential time: " + (System.currentTimeMillis()- seqTime) + " ms");

// Параллельный поток (Parallel Stream)
long parTime = System.currentTimeMillis();
numbers.parallelStream()
	.map(n -> n * 2)
	.forEach(System.out::println);
System.out.println("Parallel time: " + (System.curentTimeMillis() - parTime) + " ms");
```
**🖝 Вывод может быть в разном порядке, так как потоки выполняются параллельно.**
### Когда использовать Parallel Stream?
- <span style="color:green">🗹</span> **Большие объемы данных** (от 10 000 элементов)
- <span style="color:green">🗹</span>**Вычислительно-сложные операции** (например, математические рассчеты).
- <span style="color:green">🗹</span>**Независимые операции** (нет shared mutable state).
### Когда НЕ стоит использовать?
- <span style="color:red">🗵</span>**Маленькие коллекции** (накладные расходы на распараллеливание превысят выгоду).
- <span style="color:red">🗵</span>**Операции с блокировками** (синхронизация, IO-операции).
- <span style="color:red">🗵</span>**Когда важен порядок элементов** (если только не используется forEachOrdered).
## Примеры использования
### 1. Фильтрация и обработка
```java
List<Integer> numbers = List.of(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);

List<Integer> evenSquares = numbers.parallelStream()
	.filter(n -> n % 2 == 0)
	.map(n -> n * n)
	.collect(Collectors.toList());
System.out.println(evenSquares); // [4, 16, 36, 64, 100]
```
### 2. Суммирование (reduce в параллельном режиме)
```java
int sum = numbers.parallelStream()
	.reduce(0, Integer::sum); // 55
```
### 3. Группировка (groupingByConcurrent для параллельных потоков)
```java
Map<Integer, List<String>> grouped = words.parallelStream()
	.collect(Collectors.groupingByConcurrent(String::length)); // Потокобезопасный аналог groupingBy
```
## Проблемы и подводные камни
### 1. Состояние гонки (Race Condition)
Если используется изменяемое состояние (например внешняя переменная), возможны ошибки:
```java
List<Integer> list = new ArrayList<>();
IntStream.range(0, 1000)
	.parallel()
	.forEach(list::add); // Может вызвать ConcurrentModificationException
```
Решение: Использовать потокобезопасные коллекции (*ConcurrentHashMap, CopyOnWriteArrayList*) или *collect()*:
```java
List<Integer> safeList = IntStream.range(0, 1000)
	.parallel()
	.boxed()
	.collect(Collectors.toList());
```
### 2. Порядок элементов
```java
List<Integer> nums = List.of(1, 2, 3, 4, 5);
nums.parallelStream().forEach(System.out::print); // Может вывести "3 1 5 2 4"
```
Если порядок важен:
```java
nums.parallelStream().forEachOrdered(System.out::print); // Гарантирует порядок: 1 2 3 4 5
```
### 3. Производительность
- **Параллельные потоки не всегда быстрее** (из-за накладных расходов на создание потоков).
- **Зависит от количества ядер CPU** (на 2-ядерном CPU ускорение будет меньше, чем 16-ядерном).
## Настройка Parallel Stream
### 1. Использование своего ForkJoinPool
По умолчанию *Parallel Stream* использует *ForkJoinPool.commonPool()*.
Если нужно задать свой пул потоков:
```java
ForkJoinPool customPool = new ForkJoinPool(4); // Пул из 4 потоков
customPool.submit(() -> numbers.parallelStream()
	.map(n -> n * 2)
	.forEach(System.out::println)
).get();
```
### 2. Изменение уровня параллелизма
```java
System.setProperty("java.util.concurrent.ForkJoinPool.common.parallelism", "8"); // 8 потоков
```
# Statless и Stateful операции
В Java Stream API операции делятся на два основных типа: statless (без состояния) и stateful (с состоянием). Это различие важно для понимания производительности и поведения потоков.
## Stateless Operations (без состояния)
Stateless операции не зависят от состояния других элементов потока при обработке каждого элемента. Каждый элемент обрабатывается независимо.
### Характеристики:
- Могут обрабатывать элементы параллельно без дополнительных накладных расходов
- Не требуют хранения информации о предыдущих элементах
- Обычно более эффективны
### Примеры stateless операций:
- filter() - фильтрация элементов
- map() - преобразование элементов
- flatMap() - "разворачивание" потоков
- peek() - выполнение действия для каждого элемента
```java
list.stream()
	.filter(x -> x > 10) // stateless
	.map(x -> x * 2) // stateless
	.forEach(System.out::println);
```
## Stateful Operations (с состоянием)
Stateful операции зависят от состояния других элементов потока и требуют знания о других элементах для выполнения своей работы.
### Характеристики:
- Могут требовать обработки всего потока перед выполнением операции
- Часто требуют дополнительной памяти для хранения состояния
- Могут снижать производительность, особенно в параллельных потоках
### Примеры stateful операций:
- distinct() - удаление дубликатов
- sorted() - сортировка элементов
- limit() - ограничение количества элементов
- skip() - пропуск первых элементов
```java
list.stream()
	.distinct() // stateful
	.sorted() // stateful
	.limit(5) // stateful
	.forEach(System.out.println);
```
## Особенности использования
1. Порядок операций: Оптимально располагать stateless операции перед stateful, чтобы уменьшить объем данных для stateful обработки.
2. Параллельные потоки: Stateful операции могут значительно снизить производительность параллельных потоков из-за необходимости синхронизации.
3. Повторное использование: Потоки нельзя использовать повторно, особенно после stateful операций.