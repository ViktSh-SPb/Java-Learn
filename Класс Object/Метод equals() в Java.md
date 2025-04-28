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