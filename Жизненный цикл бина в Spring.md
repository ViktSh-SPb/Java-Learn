# Жизненный цикл бина в Spring
Жизненный цикл бина в Spring - это путь объекта от момента, когда контейнер о нем узнал, до его уничтожения.
### 1. Загрузка и анализ конфигурации
Spring стартует и:
- читает конфигурацию (@Configuration, XML, auto-config)
- сканирует классы (@Component, @Service, @Repository, @Controller)
- Не создает бины, а формирует BeanDefinition
На этом этапе Spring знает:
	- класс
	- scope (singleton, prototype, ...)
	- зависимости
	- init/destroy методы
### 2. Создание бина (Instantiation)
Когда контейнеру нужен бин (или при старте для singleton):
```java
@Service
public class UserService {
	public UserService() {
		System.out.println("1. Конструктор");
	}
}
```
- создается пустой объект
- зависимости еще не внедрены
### 3. Внедрение зависимостей (Dependency Injection)