# SOLID
**SOLID** - это набор из пяти принципов объектно-ориентированного проектирования (ООП), которые позволяют создать гибкий, поддерживаемый и масштабируемый код.
Принципы **SOLID** были впервые концептуализированы *Робертом К. Мартином* в его статье 2000 года *"Design Principles and Design Patterns"*. Эти концепции позже были развиты *Майклом Физерсом*, который познакомил нас с аббревиатурой **SOLID**.
## Аббревиатура SOLID расшифровывается так:
1. **S - Single Responsibility Principle**. Принцип единственной ответственности.
2. **O - Open-Close Principle**. Принцип открытости/закрытости.
3. **L - Lisdov Substitution Principle**. Принцип подстановки Барбары Лисков.
4. **I - Interface Segregation Principle.** Принцип разделения интерфейсов.
5. **D - Dependency Inversion Principle**. Принцип инверсии зависимостей.
# 1. Single Responsibility Principle (SRP)
**Один класс - одна ответственность**
## Суть:
Класс должен решать только одну задачу. Если у класса несколько причин для изменения - это нарушает *SRP*.
## Прмер:
❌ **Плохо:** Класс `Order` обрабатывает заказ, сохраняет его в БД и отправляет уведомления.
```java
class Order {
	public void calculateTotal() { ... }
	public void saveToDatabase() { ... }
	public void sendEmail() { ... }
}
```
✅ **Хорошо:** Разделение на 3 класса.
```java
class Order {
	public void calculateTotal() { ... }
}
class OrderRepository {
	public void saveToDatabase() { ... }
}
class NotificationService(){
	public void sendEmail() { ... }
}
```
# 2. Open-Closed Principle (OCP)
**Классы должны быть открыты для расширения, но закрыты для изменения.**
## Суть:
Новую функциональность нужно добавлять через расширение (наследование/композицию), а не изменять существующий код.
### Зачем нужен OCP?
1. **Уменьшение риска ошибок** - если код не меняется, меньше шансов что-то сломать.
2. **Гибкость архитектуры** - можно легко добавить новую функциональность.
3. **Упрощение тестирования** - не нужно переписывать тесты для старых частей кода.
## Примеры:
### 1.
❌ **Плохо:** Добавление нового типа оплаты требует изменения метода `processPayment()`.
```java
class PaymentProcessor {
	public void processPayment(String type) {
		if (type.equals("CreditCard")) { ... }
		else if (type.equals("PayPal")) { ... }
		// При добавлении нового типа нужно лезть в этот метод
	}
}
```
✅ **Хорошо:** Используем интерфейс и полиморфизм.
```java
interface PaymentMethod {
	void process();
}
class CreditCard implements PaymentMethod { ... }
class PayPal implements PaymentMethod { ... }

class PaymentProcessor {
	public void processPayment (PaymentMethod method) {
		method.process();
	}
}
```
**📌** **Польза:** Код становится устойчивее к изменениям.
### 2.
❌ **Плохо:** При добавлении новой фигуры (например, `Triangle`) нужно изменять AreaCalculator.
❌ **Плохо:** Нарушается *SRP (Single Responsibility Principle)*, так как `AreaCalculator` знает обо всех фигурах.
❌ **Плохо:** Трудно тестировать, так как логика для всех фигур в одном методе.
```java
class AreaCalculator {
	public double calculateArea(Object shape) {
		if (shape instanceof Circle) {
			Circle circle = (Circle) shape;
			return Math.PI * circle.radius * circle.radius;
		} else if (shape instanceof Square) {
			Square square = (Square) shape;
			return square.side * square.side;
		}
		throw new IllegalArgumentException("Unknown shape");
	}
}

class Circle {
	public double radius;
}

class Square {
	public double side;
}
```
✅ **Хорошо:** Используем абстракции и полиморфизм.
```java
interface Shape {
	double calculateArea();
}

class Circle implements Shape {
	private double radius;

	public Circle(double radius) {
		this.radius = radius;
	}

	@Override
	public double calculateArea() {
		return Math.PI * radius * radius;
	}
}

class Square implements Shape {
	private double side;

	public Square(double side) {
		this.side = side;
	}

	@Override
	public double calculateArea() {
		return side * side;
	}
}

class AreaCalculator {
	public double calculateArea(Shape shape) {
		return shape.calculateArea();
	}
}
```
**📌** **Польза:** Теперь `AreaCalculator` не нужно изменять при добавлении новой фигуры.
**📌** **Польза:** Каждая фигура сама отвечает за свою логику расчета площади.
**📌** **Польза:** Можно легко добавить `Triangle`, просто реализовав `Shape`:
```java
class Triangle implements shape {
	private double base;
	private double height;

	public Triangle(double base, double height) {
		this.base = base;
		this.height = height;
	}

	@Override
	public double calculateArea() {
		return 0.5 * base * height;
	}
}
```
## Способы соблюдения OCP
1. **Паттерн "Стратегия"** - если поведение может меняться.
2. **Декораторы** - если нужно динамически добавлять функциональность.
3. **Использование абстрактных классов** - если есть общая логика.
# 3. Liskov Sustitution Principle (LSP)
**Подклассы должны заменять свои родительские классы без изменения поведения программы.**
## Суть:
Наследник не должен нарушать контракт родителя (например выбрасывать новые исключения или возвращать несовместимые типы).
## Пример:
❌ **Плохо:** Класс `Square` нарушает *LSP*, если наследуется от `Rectangle` (изменяет поведение сеттеров).
```java
class Rectangle {
	protected int width, height;

	public void setWidth(int w) { width = w; }
	public void setHeight(int h) { height = h; }
	public int getArea() { return width * height; }
}

class Square extends Rectangle {
	@Override
	public void setWidth(int w) {
		width = height = w; // Нарушение LSP
	}
}
```
✅ **Решение:** Не связывать `Square` и `Rectangle` наследованием.
**📌** **Польза:** Предотвращает неожиданные баги при работе с полиморфизмом.
# 4. Interface Segregation Principle (ISP)
 **Клиенты не должны зависеть от интерфейсов, которые они не используют.**
## Суть:
Лучше много маленьких интерфейсов, чем один большой.
## Пример:
❌ **Плохо:** Интерфейс `Worker` заставляет всех реализовывать ненужные методы.
```java
interface Worker {
	void work();
	void eat();
}

class Robot implements Worker {
	@Override
	public void work() { ... }
	@Override
	public void eat() { ... } // метод не нужен
}
```
✅ **Хорошо:** Разделяем интерфейсы.
```java
interface Workable {
	void work();
}

interface Eatable {
	void eat();
}

class Robot implements Workable { ... }
class Human implements Workable, Eatable { ... }
```
**📌** **Польза:** избегаем пустых реализаций методов.
# 5. Dependency Inversion Principle (DIP)
**Зависимости должны строиться на абстракциях, а не на конкретных классах.**
## Суть:
Модули верхнего уровня не должны зависеть от модулей нижнего уровня - оба должны зависеть от абстракций.
## Пример:
❌ **Плохо:** Класс `ReportGenerator` зависит от конкретной БД.
```java
class MySQLDatabase {
	public void saveData(String data) { ... }
}

class ReportGenerator {
	private MySQLDatabase database;

	public ReportGenerator() {
		this.database = new MySQLDatabase(); // Жесткая зависимость
	}
}
```
✅ **Хорошо:** Внедряем зависимость через интерфейс.
```java
interface Database {
	void saveData(String data);
}

class MySQLDatabase implements Database { ... }
class MongoDB implements Database { ... }

class ReportGenerator {
	private Database database;

	public ReportGenerator(Database db) {
		this.database = db;
	}
}
```
**📌** **Польза:** Упрощает тестирование (можно подменить реализацию).
**📌** **Польза:** Облегчает переход на новые технологии.

[^1]: 
