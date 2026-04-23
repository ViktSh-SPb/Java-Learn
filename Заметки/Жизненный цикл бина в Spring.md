# Жизненный цикл бина в Spring
**Жизненный цикл бина в Spring** - это путь объекта от момента, когда контейнер о нем узнал, до его уничтожения.
```mermaid
---
config:
 theme: base
 themeVariables:
  primaryColor: "#c3d3ea"     # Угольно-черный
  primaryBorderColor: "#3d3d3d" # Серая граница
  primaryTextColor: "#000001"  # Золотой текст
  lineColor: "#5a4d41"        # Коричневые стрелки
 flowchart:
  useMaxWidth: true
---
flowchart TD
	A[BeanDefinition]-->B[Конструктор]-->C["<nobr>Dependency Injection (Autowired)</nobr>"]-->D[Aware-интерфейсы]-->E["<nobr>BeanPostProcessor (before initialization)</nobr>"]-->F[@PostConstruct]-->G["<nobr>BeanPostProcessor (after initialization)</nobr>"]-->H[<nobr>Бин готов к использованию</nobr>]-->I["@PreDestroy (shutdown)"]
```
### 1. Загрузка и анализ конфигурации
Spring стартует и:
- читает конфигурацию (*@Configuration, XML, auto-config*)
- сканирует классы (*@Component, @Service, @Repository, @Controller*)
- Не создает бины, а формирует *BeanDefinition*
На этом этапе Spring знает:
	- класс
	- scope (*singleton*, *prototype*, ...)
	- зависимости
	- *init/destroy* методы
### 2. Создание бина (Instantiation)
Когда контейнеру нужен бин (или при старте для *singleton*):
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
Spring:
- заполняет поля (*@Autowired*)
- вызывает сеттеры
- передает аргументы в конструктор
```java
@Autowired
private UserRepository repository;
```
После этого бин собран, но еще не инициализирован.
### 4. Aware-интерфейсы (опционально)
Если бин реализует специальные интерфейсы, Spring передает ему контекст:
```java
public class MyBean implements BeanNameAware, ApplicationContextAware {
	@Override
	public void setBeanName(String name) {}
	
	@Override
	public void setApplicationContext(ApplicationContext ctx) {}
}
```
Часто используемые:
- *BeanNameAware*
- *BeanFactoryAware*
- *ApplicationContextAware*
- *EnvironmentAware*
### 5. BeanPostProcessor - до инициализации
Все *BeanPostProcessor* получают бин:
```java
postProcessBeforeInitialization(bean, beanName)
```
Здесь часто:
- подготавливаются прокси
- проверяются аннотации
### 6. Инициализация бина
#### Варианты инициализации (по порядку):
##### 6.1 @PostConstruct
```java
@PostConstruct
public void init() {
	System.out.println("2. @Postconstruct");
}
```
##### 6.2 InitializingBean
```java
@Override
public void afterPropertiesSet() {}
```
##### 6.3 initMethod
```java
@Bean(initMethod = "init")
```
*@Postconstruct* - самый популярный и рекомендуемый вариант
### 7. BeanPostProcessor - после инициализации
```java
postProcessAfterInitialization(bean, beanName)
```
Именно здесь:
- создаются *AOP-прокси*
- бин может быть заменен на прокси объект
### 8. Бин готов к использованию
- для *singleton* - живет до остановки контекста
- для *prototype* - Spring больше его не контролирует
### 9. Уничтожение бина (Destroy)
При *shutdown* контекста (`context.close()`):
#### Варианты:
##### 9.1 @PreDestroy
```java
@PreDestroy
public void destroy() {}
```
##### 9.2 DisposableBean
```java
@Override
public void destroy() {}
```
##### 9.3 destroyMethod
```java
@Bean(destroyMethod = "close")
```
⚠️ Для *prototype destroy* не вызывается