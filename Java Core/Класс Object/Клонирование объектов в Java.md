
## Что такое клонирование?
Иногда необходимо на основе существующего объекта создать второй такой же - т.е. создать его клон. Этот процесс в Java называется **клонированием**.
Для клонирования объекта в Java можно воспользоваться тремя способами:
1. Переопределение метода clone() и реализация интерфейса Cloneable().
2. Использование конструктора копирования.
3. Использовать для клонирования механизм сериализации.
## Переопределение метода clone()
Класс Object определяет метод **clone()**, который создает копию объекта. Если вы хотите, чтобы экземпляр вашего класса можно было клонировать, необходимо переопределить этот метод и реализовать интерфейс **Cloneable**. Интерфейс **Cloneable** - это *интерфейс маркер*, он не содержит ни методов, ни переменных. Интерфейс маркер просто определяет поведение классов.

**Object.clone()** выбрасывает исключение  **CloneNotSupportedException** при попытке клонировать объект, не реализующий интерфейс **Cloneable**. 

Метод **clone()** в родительском классе **Object** является **protected** , поэтому желательно переопределить его как **public**. Реализация по умолчанию метода **Object.clone()** выполняет <u>неполное/поверхностное</u> **(shallow)** копирование. Рассмотрим пример:
### Пример поверхностного клонирования:
```Java
public class Car implements Cloneable{
	private String name;
	private Driver driver;
	
	public Car(String name, Driver driver){
		this.name = name;
		this.driver = driver;
	}

	public String getName(){
		return name;
	}

	public void setName(String name){
		this.name = name;
	}

	public Driver getDriver(){
		return driver;
	}

	public void setDriver(Driver driver){
		this.driver = driver;
	}

	@Override
	public Car clone() throws CloneNotSupportedException{
		return (Car) super.clone();
	}
}

public class Driver implements Cloneable{
	private String name;
	private int age;

	public Driver(String name, int age){
		this.name = name;
		this.age = age;
	}

	public String getName(){
		return name;
	}

	public void setName(String name){
		this.name = name;
	}

	public int getAge(){
		return age;
	}

	public void setAge(int age){
		this.age = age;
	}

	@Override
	public Driver clone() throws CloneNotSupportedException{
		return (Driver) super.clone();
	}
}

public class CloneCarExample{
	public static void main(String[] args) throws CloneNotSupportedException{
		Car car = new Car("Грузовик", new Driver("Василий", 45));
		Car clonedCar = car.clone();
		System.out.println("Оригинал:\t" + car);
		System.out.println("Клон:\t" + clonedCar);
		Driver clonedCarDriver = clonedCar.getDriver();
		clonedCarDriver.setName("Вася");
		System.out.println("Оригинал после изменения имени водителя:\t" + car);
		System.out.println("Клон после изменения имени водителя:\t" + clonedCar);
	}
}
```
В этом примере клонируется объект класса **Car**. Выполняется <u>поверхностное клонирование</u> - новый объект **clonedCar** содержит ссылку на тот же объект класса **Driver**, что и объект  **car**. Если вас это не устраивает, то необходимо самим написать <u>"глубокое" клонирование</u> - создать новый объект класса **Driver**. Перепишем метод **clone()** класса **Car**:
### Пример глубокого клонирования:
```Java
@Override
public  Car clone() throws CloneNotSupportedException{
	Car newCar = (Car) super.clone();
	Driver driver = this.getDriver().clone;
	newCar.setDriver(driver);
	return newCar;
}
```
## Конструктор копирования
Еще один вариант клонирования объекта - это конструктор копирования. Создается конструктор, принимающий на вход объект того же класса, который необходимо клонировать:
### Пример конструктора копирования с поверхностным клонированием:
```Java
public class Car implements Cloneable{
	private String name;
	private Driver driver;

	public Car(String name, Driver driver){
		this.name = name;
		this.driver = driver;
	}
	
	/*
	 * Конструктор копирования.
	 *
	 * @param otherCar
	 */
	public Car(Car otherCar){
		this(otherCar.getName(), otherCar.getDriver());
	}

	public String getName(){
		return name;
	}

	public void setName(String name){
		this.name = name;
	}

	public Driver getDriver(){
		return driver;
	}

	public void setDriver(Driver driver){
		this.driver = dirver;
	}
}
```
Опять же - пример показывает неглубокое клонирование. Перепишем конструктор для реализации "глубокого" копирования:
### Пример конструктора копирования "глубоким" клонированием:
```Java
public Car(Car otherCar) throws CloneNotSupportedException{
	this(otherCar.getName(), otherCar.getDriver().clone());
}
```