Класс **Duration** является частью *Java Date-Time API* (пакет *java.time*), введенного в *Java 8* для современной работы с датой и временем. Он представляет продолжительность или количество времени между двумя моментами в секундах и наносекундах.
## Основные характеристики
1. **Точность** - хранит время с точностью до наносекунды
2. **Неизменяемость** - все методы возвращают новые объекты
3. **Потокобезопасность** - может использоваться в многопоточной среде.
4. **Совместимость** - работает с другими классами java.time
## Создание Duration
```java
// С помощью фабричных методов
Duration d1 = Duration.ofHours(2); // 2 часа
Duration d2 = Duration.ofMinutes(30); // 30 минут

// Через разницу между временными точками
Instant start = Instant.now();
// ... выполнение кода ...
Instant end = Instant.now();
Duration elapsed = Duration.between(start, end);

// Парсинг из строки (ISO-8601 формата)
Duration d3 = Duration.parse("PT1H30M"); // 1 час, 30 минут
```
## Основные операции
### 1. Арифметические операции:
```java
Duration sum = d1.plus(d2); // Сложение
Duration diff = d1.minus(d2); // Вычитание
Duration scaled = d1.multipledBy(3); // Умножение
```
### 2. Сравнение:
```java
boolean isGreater = d1.compareTo(d2) > 0;
boolean isEqual = d1.equals(d2);
```
### 3. Получение значений
```java
long seconds = d1.getSeconds(); // Общее количество секунд
int nanos = d1.getNano(); // Дополнительные наносекунды
long hours = d1.toHours(); // Конвертация в часы
```
## Практическое применение
### 1. Измерение времени выполнения
```java
Instant start = Instant.now();
// ... выполняемый код ...
Duration elapsed = Duration.between(start, Instant.now());
System.out.printf("Выполнение заняло %d мс%n", elapsed.toMillis());
```
### 2. Таймауты и задержки
```java
// Использование с Thread.sleep()
Thread.sleep(duration.toMillis());

// Использование с CompletableFuture
CompletableFuture.supplyAsync(() -> ...).orTimeout(duration.toSeconds(), TimeUnit.SECONDS);
```
### 3. Работа с временными интервалами
```java
LocalTime start = LocalTime.of(9, 0);
LocalTime end = start.plus(Duration.ofHours(8)); // 17:00
```
## Особенности
1. **Формат строки использует стандарт ISO-8601:**
	- `PT5M` - 5 минут
	- `PT1H30M` - 1 час, 30 минут
	- `PT0.5S` - 500 миллисекунд
2. **Максимальные значения:**
	- Максимальная продолжительность: ~292 миллиарда лет
	- Минимальная продолжительность: ~-292 миллиарда лет
3. **Отрицательные значения возможны** и указывают на обратное направление времени
## Отличия от похожих классов:
1. **Duration vs Period:**
	- **Duration** - для точного времени (часы, минуты, секунды)
	- **Period** - для календарных дат (годы, месяцы, дни)
2. **Duration vs Instant:**
	- **Instant** - конкретный момент на временной шкале
	- **Duration** - промежуток между двумя моментами
## Советы по использованию:
1. Для работы с человеко-читаемым форматом лучше использовать:
```java
long hours = duration.toHours();
long minutes = duration.minusHours(hours).toMinutes();
System.out.printf("%d часов %d минут", hours, minutes);
```
2. При работе с устаревшим API можно конвертировать:
```java
// В старые TimeUnit
long seconds = duration.getSeconds();

// В миллисекунды (с потерей точности)
long millis = duration.toMillis();
```