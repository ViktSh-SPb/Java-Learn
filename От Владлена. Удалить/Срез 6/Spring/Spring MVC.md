### Что такое Spring MVC?

Spring MVC — это компонент фреймворка Spring, реализующий архитектуру Model-View-Controller (MVC) для разработки веб-приложений. Он обеспечивает структуру для обработки HTTP-запросов, связывания данных, выбора представлений и управления потоком выполнения, делая разработку веб-интерфейсов более организованной и тестируемой.

### Какова основная роль аннотации @Controller в Spring MVC?

Аннотация `@Controller` обозначает класс как контроллер, который обрабатывает входящие HTTP-запросы. Внутри такого класса определяются методы, помеченные аннотациями вроде `@RequestMapping`, `@GetMapping`, `@PostMapping`, которые сопоставляют URL-адреса с логикой обработки. Контроллер взаимодействует с моделью и возвращает представление (view) для отображения пользователю.

### Какие шаги необходимо выполнить для создания простого веб-приложения на Spring MVC?

1. Настройка проекта: Создайте проект с зависимостями Spring Web.
2. Конфигурация диспетчера сервлетов: Обеспечьте конфигурацию `DispatcherServlet` (может быть через Java-конфигурацию или XML).
3. Создание контроллера: Создайте класс с аннотацией `@Controller` и методами с `@RequestMapping`.
4. Определение представлений: Создайте JSP или другие шаблоны для отображения данных.
5. Настройка ViewResolver: Укажите, где искать представления.
6. Запуск приложения: Запустите сервер (например, Tomcat) и протестируйте работу.

### Как в Spring MVC реализуется обработка запросов на стороне сервера?

Обработка запросов происходит через контроллеры:

- Клиент отправляет HTTP-запрос.
- `DispatcherServlet` перехватывает запрос и ищет подходящий метод в контроллерах по URL и методу.
- Метод выполняет бизнес-логику, заполняет модель данными.
- Возвращается имя представления (например, JSP).
- ViewResolver определяет путь к файлу представления.
- Представление рендерится и отправляется обратно клиенту.

### Какие преимущества предоставляет использование ViewResolver в Spring MVC?

- Абстракция поиска представлений: позволяет легко менять шаблонные движки или структуру папок без изменения бизнес-кода.
- Гибкость конфигурации: можно настроить разные ViewResolver для разных типов представлений.
- Поддержка нескольких форматов: JSP, Thymeleaf, FreeMarker и др.
- Упрощение маршрутизации: автоматический поиск нужных файлов по логике приложения.

### Как можно обработать исключения в приложении Spring MVC?

Используйте:

- Аннотацию `@ExceptionHandler` внутри контроллера для обработки конкретных исключений.

```java
@ExceptionHandler(SomeException.class)
public String handleException() {
    return "errorPage";
}
```

- Глобальные обработчики через класс с аннотацией `@ControllerAdvice`.

```java
@ControllerAdvice
public class GlobalExceptionHandler {
    @ExceptionHandler(Exception.class)
    public String handleAllExceptions() {
        return "error";
    }
}
```

Это позволяет централизованно управлять ошибками и показывать пользовательские страницы ошибок.

### Как реализовать международную поддержку (i18n) в приложении Spring MVC?

1. Настройте `MessageSource` — компонент для загрузки сообщений на разных языках:

```java
@Bean
public ResourceBundleMessageSource messageSource() {
    ResourceBundleMessageSource source = new ResourceBundleMessageSource();
    source.setBasename("messages");
    return source;
}
```

1. Создайте файлы сообщений (`messages.properties`, `messages_ru.properties` и др.).
2. Вьюшки используют `${msg['key']}` или `<spring:message code="key"/>`.
3. Для переключения языка используйте интерцептор `LocaleChangeInterceptor`, который реагирует на параметр запроса (`?lang=ru`).

### Какие стратегии можно применить для оптимизации производительности веб-приложения на Spring MVC?

- Использование кэширования (например, кеширование результатов запросов).
- Минимизация объема передаваемых данных (сжатие ответов).
- Использование асинхронных методов (`@Async`) для тяжелых операций.
- Оптимизация работы с базой данных (ленивая загрузка, индексы).
- Использование CDN для статических ресурсов.
- Внедрение CDN или балансировщиков нагрузки.
- Минимизация количества запросов за счет объединения ресурсов.

### Как в Spring MVC реализовать аутентификацию и авторизацию с использованием Spring Security?

1. Добавьте зависимость `spring-security`.
2. Создайте класс конфигурации безопасности:

```java
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.authorizeRequests()
            .antMatchers("/admin/**").hasRole("ADMIN")
            .antMatchers("/user/**").hasAnyRole("USER", "ADMIN")
            .anyRequest().authenticated()
            .and()
            .formLogin()
            .loginPage("/login")
            .permitAll()
            .and()
            .logout().permitAll();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        auth.inMemoryAuthentication()
            .withUser("user").password("{noop}password").roles("USER")
            .and()
            .withUser("admin").password("{noop}admin").roles("ADMIN");
    }
}
```

1. Создайте страницы входа (`/login`) и выхода.
2. Защитите нужные маршруты по ролям.

Это обеспечит безопасный доступ к ресурсам вашего приложения.