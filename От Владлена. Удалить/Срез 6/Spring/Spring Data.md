### Что такое Spring Data?

Spring Data — это часть экосистемы Spring, которая упрощает работу с различными хранилищами данных (базами данных, NoSQL, поисковыми системами и др.). Она предоставляет абстракции и репозитории для автоматического создания реализаций методов доступа к данным, что значительно сокращает объем шаблонного кода и ускоряет разработку.

### Каковы основные преимущества использования Spring Data в проектах?

- Автоматическая реализация репозиториев: достаточно объявить интерфейс, и Spring Data создаст его реализацию.
- Поддержка различных хранилищ данных: SQL (JPA, JDBC), NoSQL (MongoDB, Cassandra), поисковые движки (Elasticsearch) и др.
- Упрощение работы с запросами: возможность писать запросы через методы или аннотации.
- Пагинация и сортировка: встроенная поддержка.
- Интеграция с Spring Boot: автоматическая настройка.
- Расширяемость: возможность добавлять свои реализации и расширять функциональность.

### Какие основные интерфейсы предоставляет Spring Data для работы с базами данных?

- Repository: базовый интерфейс для всех репозиториев.
- CrudRepository: предоставляет CRUD операции.
- PagingAndSortingRepository: добавляет поддержку пагинации и сортировки.
- JpaRepository: расширяет `CrudRepository` и `PagingAndSortingRepository`, включает дополнительные методы для JPA.
- MongoRepository, CassandraRepository, ElasticsearchRepository и др.: для работы с конкретными хранилищами.

### Как реализовать пагинацию и сортировку в Spring Data?

Используйте параметры `Pageable` или `Sort` в методах репозитория:

```java
Page<Entity> findAll(Pageable pageable);
```

Пример:

```java
PageRequest pageRequest = PageRequest.of(0, 10, Sort.by("name").ascending());
Page<Entity> page = repository.findAll(pageRequest);
```

Это автоматически выполнит пагинацию и сортировку по указанным параметрам.

### Как использовать аннотацию @Query для создания пользовательских запросов в Spring Data?

Аннотация `@Query` позволяет писать собственные JPQL или SQL-запросы:

```java
@Query("SELECT e FROM Entity e WHERE e.name = :name")
List<Entity> findByName(@Param("name") String name);
```

или для нативных SQL-запросов:

```java
@Query(value = "SELECT * FROM entity_table WHERE name = :name", nativeQuery = true)
List<Entity> findByNameNative(@Param("name") String name);
```

Это удобно для сложных запросов или оптимизации.

### Как настроить кэширование запросов в Spring Data?

Spring Data сам по себе не реализует кэширование, но его можно интегрировать с механизмами кэширования Spring:

1. Включите кэширование в конфигурации (`@EnableCaching`).
2. Используйте аннотации `@Cacheable`, `@CachePut`, `@CacheEvict` на методах репозиториев:

```java
@Cacheable("entities")
List<Entity> findByName(String name);
```

1. Настройте провайдер кэша (например, Ehcache, Redis).

Это позволит сохранять результаты запросов и ускорять повторные обращения.

### Как использовать Spring Data REST для создания RESTful сервисов?

Spring Data REST автоматически создает REST API на основе репозиториев:

- Просто добавьте зависимость `spring-boot-starter-data-rest`.
- Объявите репозитории как обычно (`extends JpaRepository`).
- После запуска приложения автоматически появятся эндпоинты типа `/entities`, `/entities/{id}`.

Можно настраивать маршруты, фильтры, безопасность через конфигурацию.

### Какие стратегии оптимистичного и пессимистичного блокирования поддерживает Spring Data JPA, и как их применять?

Оптимистичное блокирование:

- Использует версионное поле (`@Version`) в сущности.
- При сохранении проверяется версия; если она изменилась — выбрасывается исключение.

Пример:

```java
@Entity
public class MyEntity {
    @Version
    private Long version;
}
```

Используется при конкурентных сценариях без блокировок базы.

Пессимистичное блокирование:

- Блокирует строку/таблицу при чтении или обновлении.

Пример:

```java
entityManager.find(Entity.class, id, LockModeType.PESSIMISTIC_WRITE);
```

или через репозиторий:

```java
repository.findById(id, LockModeType.PESSIMISTIC_WRITE);
```

Это обеспечивает более строгую синхронизацию при необходимости.

### Как настроить аудит сущностей в Spring Data JPA?

Используйте встроенную поддержку аудита:

1. Включите аудит в конфигурации:

```java
@Configuration
@EnableJpaAuditing
public class Config { }
```

1. В сущностях добавьте поля с аннотациями:

```java
@Entity
public class MyEntity {

    @CreatedDate
    private LocalDateTime createdDate;

    @LastModifiedDate
    private LocalDateTime lastModifiedDate;

    @CreatedBy
    private String createdBy;

    @LastModifiedBy
    private String lastModifiedBy;
}
```

1. Обеспечьте реализацию `AuditorAware` для определения текущего пользователя.

Это автоматизирует ведение истории создания/изменения данных.