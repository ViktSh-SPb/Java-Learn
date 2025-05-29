SOLID - это набор из пяти принципов объектно-ориентированного проектирования (ООП), которые позволяют создать гибкий, поддерживаемый и масштабируемый код.
## Аббревиатура SOLID расшифровывается так:
1. S - Single Responsibility Principle. Принцип единственной ответственности.
2. O - Open-Close Principle. Принцип открытости/закрытости.
3. L - Lisdov Substitution Principle. Принцип подстановки Барбары Лисков.
4. I - Interface Segregation Principle. Принцип разделения интерфейсов.
5. D - Dependency Inversion Principle. Принцип инверсии зависимостей
# 1. Single Responsibility Principle (SRP)
Один класс - одна ответственность
## Суть:
Класс должен решать только одну задачу. Если у класса несколько причин для изменения - это нарушает SRP.
## Прмер:
❌ Плохо: Класс Order обрабатывает заказ, сохраняет его в БД и отправляет уведомления.
```java
class Order {
	public void calculateTotal() { ... }
	public void saveToDatabase() { ... }
	public void sendEmail() { ... }
}
```
✅ Хорошо: Разделение на 3 класса.
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
Классы должны быть открыты для расширения, но закрыты для изменения.
## Суть:
Новую функциональность нужно добавлять через расширение (наследование/композицию), а не изменять существующий код.
## Пример:
❌ Плохо: Добавление нового типа оплаты требует изменения метода processPayment().
```java
class PaymentProcessor {
	public void processPayment(String type) {
		if (type.equals("CreditCard")) { ... }
		else if (type.equals("PayPal")) { ... }
		// При добавлении нового типа нужно лезть в этот метод
	}
}
```
✅ Хорошо: Используем интерфейс и полиморфизм.
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
Польза: Код становится устойчивее к изменениям.
