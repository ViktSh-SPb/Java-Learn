Оператор **switch** позволяет выполнять разные участки кода в зависимости от значения переменной или выражения. Это альтернатива длинным цепочкам *if-else*, когда нужно проверить одно значение на множество вариантов.
# Основной синтаксис
```java
switch (выражение) {
	case значение1:
		// код для значения1
		break;
	case значение2:
		// код для значения2
		break;
	// ...
	default:
		// код, если ни один case не подошел
}
```
# Особенности
1. Выражение в **switch** может быть:
	- Целочисленным (*byte, short, int, char*)
	- Строкой (*String*, начиная с Java 7)
	- Перечислением (*enum*)
	- Обертками примитивных типов (*Byte, Short, Integer, Character*)
2. Ключевое слово **break** прерывает выполнение **switch**. Если его нет, выполнение "проваливается" (*fall-through*) в следующий *case*.
3. **default** секция выполняется, если ни один *case* не совпал. Она не обязательна.
# Примеры
## 1. Пример с числами
```java
int day = 3;
String dayName;

switch (day){
	case 1:
		dayName = "Понедельник";
		break;
	case 2:
		dayName = "Вторник";
		break;
	case 3:
		dayName = "Среда";
		break;
	// ...
	default:
		dayName = "Неизвестный день";
}
System.out.println(dayName); // Выведет "Среда"
```
## 2. Пример со строками
```java
String fruit = "Яблоко";

switch (fruit) {
	case "Банан":
		System.out.println("Желтый");
		break;
	case "Яблоко":
		System.out.println("Красное или зеленое");
		break;
	default:
		System.out.println("Неизвестный фрукт");
}
```
## 3. Пример с fall-through
```java
int month = 2;
int year = 2020;
int numDays = 0;

switch (month){
	case 1: case 3: case 5: case 7: case 8: case 10: case 12:
		numDays = 31;
		break;
	case 4: case 6: case 9: case 11:
		numDays = 30;
		break;
	case 2:
		if(((year % 4 == 0) && !(year % 100 == 0)) || (year % 400 == 0))
			numDays = 29;
		else
			numDays = 28;
		break;
	default:
		System.out.println("Неверный месяц");
}
```
## Switch expressions (Java 12+)
Начиная с Java 12, появилась улучшенная форма switch - **switch expressions**:
```java
String dayType = switch (day) {
	case 1, 2, 3, 4, 5 -> "Будний день";
	case 6, 7 -> "Выходной день";
	default -> "Неизвестный день";
};
```
Особенности:
- Используется **->** вместо **:**
- Нет необходимости в *break* (нет *fall-through*)
- Может возвращать значение
- Поддерживает множественные case-метки