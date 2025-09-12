# Метод equals() в Java
В Java сравнение объектов производится с помощью метода **equals()** класса **Object**. Этот метод сравнивает содержимое объектов и выводит значение типа **boolean**. Значение *true* - если содержимое эквивалентно и *false* - если нет. 

Операция **\==** не рекомендуется для сравнивания объектов в Java. Дело в том, что при сравнении объектов, операция **\==** вернет *true* лишь в одном случае - когда ссылки указывают на один и тот же объект. В данном случае не учитывается содержимое переменных класса.

При создании пользовательского класса, принято переопределять метод **equals()** таким образом, чтобы учитывались переменные объекта.

Рассмотрим пример использования метода **equals()**. В классе **Person** определены три переменные: **fullName**, **age**, **retired**. В переопределенном методе **equals()** все они участвуют в проверке. Если вы не хотите учитывать какую-то переменную при проверке объектов на равенство, вы имеете право не проверять ее в методе **equals()**.
```Java
public class Person{
	private String fullName;
	private int age;
	private boolean retired;

	@Override
	public boolean equals(Object o){
		if(this == o){
			return true;
		}
		if(o==null || getClass() != o.getClass()){
			return false;
		}
		Person person = (Person) o;
		if(getAge() != person.getAge()){
			return false;
		}
		if(isRetired() != person.isRetired()){
			return false;
		}
		return getFullName() != null
			? getFullName().equals(person.getFullName())
			: person.getFullName() == null;
	}
}
```
Определим два объекта **person1** и **person2** типа **Person** с одинаковыми значениями. При их сравнении с помощью операции **\==** вернется значение *false*, так как это разные объекты. Если же сравнивать их методом **equals()**, то результат будет равен *true*. Также в этом примере объявлена переменная **person3**, которой присвоена ссылка из переменной **person1**. Вот сравнение **person1 == person3** вернет значение *true*, так как переменные указывают на один объект. При сравнении **person1** и **person3** с помощью метода **equals()**, тоже вернется значение *true*. В методе **equals()** первой строкой проверяются ссылки сравниваемых объектов - **this == о**, и если они равны, сразу же возвращается значение *true*.
```Java
public class PersonExample2{
	public static void main(String[] args){
		Person person1 = new Person("Петров Иван Иванович", 56, false);
		Person person2 = new Person("Петров Иван Иванович", 56, false);
		Person person3 = person1;

		System.out.println("person1 == person2? " + (person1 == person2));
		System.out.println("person1 == person3? " + (person1 == person3));

		System.out.println("person1.equals(person2)? " + person1.equals(person2));
		System.out.println("person1.equals(person3)? " + person1.equals(person3));
	}
}
```
Результат выполнения:
```bash
person1 == person2? false
person1 == person3? true
person1.equals(person2)? true
person1.equals(person3)? true
```
## Контракт equals()
1. Рефлексивность. Для любого ненулевого x - x.equals(x) возвращает true.
2. Симметричность. Для любых x и y: x.equals(y) ⇔ y.equals(x).
3. Транзитивность. Если x.equals(y) = true  и y.equals(z) = true, то x.equals(z) = true.
4. Согласованность. Многократные вызовы x.equals(y) при неизменных объектах всегда дают один и тот же результат.
5. Сравнение с null. Для любого x - x.equals(null) = false.
## Практические советы
- Используйте @Override над equals.
- Сравнивайте типы осторожно: getClass() (строгая типовая эквивалентность) вместо instanceof (разрешает сравнение с подклассами). Выбор зависит от семантики класса.
- Для полей используйте Objects.equals(a, b) (безопасно для null) и Float.compare() / Double.compare() для чисел с плавающей точкой.
- Если класс изменяемый и его поля участвуют в equals, аккуратно с изменением полей - это может нарушить поведение в коллекциях, например в HashSet.
- Рассмотрите record (Java 16+) - он автоматически реализует equals(), hashCode(), toString.
- Генерируйте equals() и hashCode() через IDE - обычно правильно и быстро.
## Примеры
### Простой неизменяемый класс (рекомендуемый вариант)
```java
import java.util.Objects;

public final class Person {
	private final String name;
	private final int age;
	
	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}
	
	@Override
	public boolean equals(Object o) {
		if (this == o) return true;                                // тот же объект
		if (0 == null || getClass != o.getClass()) return false;   // строгая проверка
		Person person = (Person) o;
		return age == person.age && Objects.equals(name, person.name);
	}
	
	@Override
	public int hashCode(){
		return Objects.hash(name, age);
	}
}
```
### Случай, когда нужен instanceof() (поддержка подклассов)
```java
import java.util.Objects

public class Point {
	private final int x, y;
	
	pubilc Point(int x, int y) {
		this.x = x;
		this.y = y;
	}
	
	@Override
	public boolean equals(Object o) {
		if (this == o) return true;
		if (!(o instanceof Point)) return false          // разрешаем подклассы
		Point p = (Point) o;
		return x == p.x && y == p.y;
	}
	
	@Override
	pubilc int hashCode() {
		return Objects.hash(x, y);
	}
}
```
Если разрешаете сравнение с подклассами, учтите возможные нарушения симметричности, если подкласс расширяет состояние. Для безопасного сравнения с подклассами используйте шаблон canEqual.
### Шаблон canEqual (для безопасного equals() в иерархиях)
```java
public class A {
	private final int a;
	
	public boolean canEqual(Object other) {
		return other instanceof A;
	}
	
	@Override
	public boolean equals(Object o) {
		if (this == o) return true;
		if (!(o instanceof A)) return false;
		A other = (A) o;
		if (!other.canEqual(this)) return false;
		reutrn this.a == other.a;
	}
	
	@Override
	public int hashCode() {
		return Integer.hashCode(a);
	}
}

public class B extends A {
	private final int b;
	
	@Override
	public boolean canEqual(Object other) {
		return other instanceof B;
	}
	
	@Override
	public boolean equals(Object o) {
		if (this == o) return true;
		if (!(o instanceof B)) return false;
		if (!super.equals(o)) return false;
		B other = (B) o;
		return this.b == other.b;
	}
}
```
