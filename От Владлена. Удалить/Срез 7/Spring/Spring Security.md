### Что такое Spring Security и для чего он используется?

Spring Security — это мощный и гибкий фреймворк для обеспечения безопасности в приложениях на основе Spring. Он предоставляет механизмы аутентификации, авторизации, защиту от атак и интеграцию с другими системами безопасности.  
Основные функции Spring Security:
 1. Аутентификация (Authentication)  
    - Проверка подлинности пользователя (логин/пароль, OAuth2, JWT, LDAP и др.).  
 2. Авторизация (Authorization)  
    - Контроль доступа к ресурсам на основе ролей (`ROLE_ADMIN`, `ROLE_USER`) или прав.  
    - Аннотации (`@PreAuthorize`, `@Secured`) и конфигурация в `SecurityConfig`.  
 3. Защита от атак  
    - CSRF (межсайтовая подделка запроса).  
 4. Интеграция  
    - Работа с OAuth2 (Google, GitHub, Keycloak).  
    - Поддержка JWT (JSON Web Tokens) для stateless-аутентификации.  
    - Совместимость с Spring Boot (автоконфигурация).  
Итог: Spring Security упрощает реализацию безопасности, избавляя от ручного написания сложных механизмов защиты."

### Как включить Spring Security в вашем Spring Boot приложении?

Включить Spring Security в Spring Boot приложении очень просто благодаря автоконфигурации.
1. Добавление зависимости  
 Достаточно добавить `spring-boot-starter-security` в `pom.xml` (Maven) или `build.gradle` (Gradle):
После этого Spring Boot автоматически защитит все эндпоинты базовой HTTP-аутентификацией.
2. Базовая защита ""из коробки""  
 После добавления зависимости:
 - Все endpoints требуют аутентификации.
 - Автоматически генерируется пароль для пользователя `user` (логится в консоль при запуске).  
 - Форма входа доступна по `/login` (если это MVC).  
 - REST API требует HTTP Basic Auth (логин/пароль в заголовке).
3. Настройка своей конфигурации  
 Чтобы переопределить стандартные настройки, создайте класс с аннотацией `@Configuration`:
 @Configuration
 public class SecurityConfig {

```java
     @Bean
     public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
         http
            .authorizeHttpRequests(auth -> auth
	            .requestMatchers(""/"", ""/public/"").permitAll()  // Разрешить без аутентификации
	            .anyRequest().authenticated()  // Остальное — только для авторизованных
             )
            .formLogin(form -> form           // Включить форму входа
                 .loginPage(""/login"")      // Кастомная страница входа (опционально)
                 .permitAll()
             );
         
         return http.build();
     }
 }
 
 @Bean
 public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
     http
         .authorizeHttpRequests(auth -> auth
             .anyRequest().permitAll()  // Разрешить всё без аутентификации
         )
         .csrf().disable();             // Отключить CSRF (не для прода!)
     
     return http.build();
 }
 ```

### Какие основные компоненты архитектуры Spring Security вы знаете?

1. `SecurityContext` и `SecurityContextHolder`
 - `SecurityContext` Хранит информацию об аутентификации (`Authentication` object) текущего пользователя.  
 - `SecurityContextHolder` Механизм хранения `SecurityContext` (по умолчанию — в `ThreadLocal` для веб-запросов).  
 Authentication auth = SecurityContextHolder.getContext().getAuthentication();
 String username = auth.getName();
2. `Authentication` (Аутентификация)  
 Объект, представляющий попытку аутентификации (или успешный вход):  
 - Principal (логин пользователя или объект `UserDetails`).  
 - Credentials (пароль/токен).  
 - Authorities (роли/права, например, `ROLE_ADMIN`).  
3. `UserDetails` и `UserDetailsService`  
 - `UserDetails`  Интерфейс для кастомной модели пользователя (логин, пароль, права).  
 - `UserDetailsService`  Загружает пользователя по имени (например, из БД). 
4. `FilterChain` и `SecurityFilterChain`  
 Spring Security работает как цепочка фильтров (сервлетных). Основные фильтры:  
 - `UsernamePasswordAuthenticationFilter` – обработка формы входа.  
 - `BasicAuthenticationFilter` – HTTP Basic Auth.  
 - `BearerTokenAuthenticationFilter` – для JWT/OAuth2.  
 - `ExceptionTranslationFilter` – обработка ошибок (перенаправление на `/login` при `403`).  

```java

 @Bean
 SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
     http
         .addFilterBefore(new CustomFilter(), BasicAuthenticationFilter.class)
         .authorizeHttpRequests(auth -> auth.anyRequest().authenticated());
     return http.build();
 }
 ```
 
5. `AuthenticationManager` и `ProviderManager`  
 - `AuthenticationManager` – главный интерфейс для аутентификации.  
 - `ProviderManager` – его реализация, делегирующая проверку `AuthenticationProvider`-ам.  

 Пример кастомного `AuthenticationProvider`:  
 ```java
 @Component
 public class CustomAuthProvider implements AuthenticationProvider {
     @Override
     public Authentication authenticate(Authentication auth) {
         String username = auth.getName();
         String password = auth.getCredentials().toString();
         // Ваша логика проверки
         return new UsernamePasswordAuthenticationToken(username, password, authorities);
     }

     @Override
     public boolean supports(Class<?> authentication) {
         return authentication.equals(UsernamePasswordAuthenticationToken.class);
     }
 }
 ```

`SecurityContextPersistenceFilter`  
 Восстанавливает `SecurityContext` из хранилища (например, из сессии или JWT).
Итог  
 Эти компоненты образуют гибкую систему безопасности:  
 1. Фильтры перехватывают запросы.  
 2. `AuthenticationManager` проверяет учетные данные.  
 3. `SecurityContext` хранит состояние аутентификации.  
 4. `UserDetailsService` загружает пользователей.  
 5. `HttpSecurity` настраивает правила доступа.  

### Как настроить аутентификацию пользователя с использованием базы данных в Spring Security?

Настройка аутентификации через базу данных в Spring Security включает несколько ключевых шагов: создание сущности пользователя, реализацию `UserDetailsService`, настройку `PasswordEncoder` и конфигурацию безопасности.
1. Подготовка базы данных
 Сущность пользователя (JPA Entit) 
 ```java
@Entity
 @Table(name = ""users"")
 public class User {
     @Id
     @GeneratedValue(strategy = GenerationType.IDENTITY)
     private Long id;
     
     @Column(unique = true, nullable = false)
     private String username;
     
     @Column(nullable = false)
     private String password;
     
     @Enumerated(EnumType.STRING)
     private Role role; // Роль (например, USER, ADMIN)

     // Геттеры и сеттеры
 }

 public enum Role {
     USER, ADMIN
 }
 ```

 Репозиторий (JPA Repository)
 ```java
 public interface UserRepository extends JpaRepository<User, Long> {
     Optional<User> findByUsername(String username);
 }
 ```

 2. Реализация `UserDetailsService`
 Spring Security использует этот интерфейс для загрузки пользователя из БД.

```java
 @Service
 public class DatabaseUserDetailsService implements UserDetailsService {

     @Autowired
     private UserRepository userRepository;

     @Override
     public UserDetails loadUserByUsername(String username) throws UsernameNotFoundException {
         User user = userRepository.findByUsername(username)
             .orElseThrow(() -> new UsernameNotFoundException(""User not found: "" + username));
         
         return org.springframework.security.core.userdetails.User.builder()
             .username(user.getUsername())
             .password(user.getPassword())
             .roles(user.getRole().name()) // Преобразует Role.ADMIN в ""ROLE_ADMIN""
             .build();
     }
 }
 ```

 3. Настройка `PasswordEncoder`
 Обязательно шифруйте пароли перед сохранением в БД!  
 Рекомендуется `BCryptPasswordEncoder`.

 ```java
 @Bean
 public PasswordEncoder passwordEncoder() {
     return new BCryptPasswordEncoder();
 }
 ```

 Пример регистрации пользователя (с шифрованием пароля):
 ```java
 @RestController
 @RequestMapping(""/auth"")
 public class AuthController {

     @Autowired
     private UserRepository userRepository;
     
     @Autowired
     private PasswordEncoder passwordEncoder;

     @PostMapping(""/register"")
     public String register(@RequestBody User user) {
         user.setPassword(passwordEncoder.encode(user.getPassword()));
         userRepository.save(user);
         return ""User registered!"";
     }
 }
 ```

 4. Конфигурация Spring Security
 Настройте `SecurityFilterChain` для работы с БД.

 ```java
 @Configuration
 @EnableWebSecurity
 public class SecurityConfig {

     @Autowired
     private DatabaseUserDetailsService userDetailsService;

     @Bean
     public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
         http
             .authorizeHttpRequests(auth -> auth
                 .requestMatchers(""/auth/register"").permitAll() // Регистрация открыта для всех
                 .requestMatchers(""/admin/"").hasRole(""ADMIN"")
                 .anyRequest().authenticated()
             )
             .formLogin(form -> form
                 .loginPage(""/login"")
                 .permitAll()
             )
             .userDetailsService(userDetailsService); // Подключаем наш UserDetailsService
         
         return http.build();
     }
 }
```

### Как реализовать JWT аутентификацию в Spring Security?

Реализация JWT-аутентификации в Spring Security требует настройки цепочки фильтров, генерации/валидации токенов и интеграции с механизмами безопасности Spring. 
 1. Добавление зависимостей
 Для работы с JWT потребуется библиотека `jjwt` (Maven/Gradle):
 2. Класс для работы с JWT Создайте утилитный класс для генерации и валидации токенов:

```java
 public class JwtUtils {
     private static final Key SECRET_KEY = Keys.secretKeyFor(SignatureAlgorithm.HS256);
     private static final long EXPIRATION_TIME = 86400000; // 24 часа

     public static String generateToken(String username) {
         return Jwts.builder()
             .setSubject(username)
             .setIssuedAt(new Date())
             .setExpiration(new Date(System.currentTimeMillis() + EXPIRATION_TIME))
             .signWith(SECRET_KEY)
             .compact();
     }

     public static boolean validateToken(String token) {
         try {
             Jwts.parserBuilder()
                 .setSigningKey(SECRET_KEY)
                 .build()
                 .parseClaimsJws(token);
             return true;
         } catch (Exception e) {
             return false;
         }
     }

     public static String extractUsername(String token) {
         return Jwts.parserBuilder()
             .setSigningKey(SECRET_KEY)
             .build()
             .parseClaimsJws(token)
             .getBody()
             .getSubject();
     }
 }
```

 3. JWT Authentication Filter
 Фильтр для обработки JWT-токенов в заголовках запросов:
 
```java
 public class JwtAuthFilter extends OncePerRequestFilter {
     @Override
     protected void doFilterInternal(
         HttpServletRequest request,
         HttpServletResponse response,
         FilterChain filterChain
     ) throws ServletException, IOException {
         String token = extractToken(request);
         if (token != null && JwtUtils.validateToken(token)) {
             String username = JwtUtils.extractUsername(token);
             // Создаем аутентифицированный объект
             UsernamePasswordAuthenticationToken auth = new UsernamePasswordAuthenticationToken(
                 username,
                 null,
                 Collections.emptyList() // Роли можно добавить здесь
             );
             SecurityContextHolder.getContext().setAuthentication(auth);
         }
         filterChain.doFilter(request, response);
     }

     private String extractToken(HttpServletRequest request) {
         String header = request.getHeader(""Authorization"");
         if (header != null && header.startsWith(""Bearer "")) {
             return header.substring(7);
         }
         return null;
     }
 }
```

4. Настройка Spring Security
 Подключите JWT-фильтр и настройте `SecurityFilterChain`:

```java
 @Configuration
 @EnableWebSecurity
 public class SecurityConfig {
     @Bean
     public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
         http
             .csrf().disable()
             .authorizeHttpRequests(auth -> auth
                 .requestMatchers(""/auth/login"").permitAll()
                 .anyRequest().authenticated()
             )
             .sessionManagement().sessionCreationPolicy(SessionCreationPolicy.STATELESS)
             .and()
             .addFilterBefore(new JwtAuthFilter(), UsernamePasswordAuthenticationFilter.class);
         
         return http.build();
     }

     @Bean
     public PasswordEncoder passwordEncoder() {
         return new BCryptPasswordEncoder();
     }
 }
 ```
 
 5. Контроллер для аутентификации
 Эндпоинт для входа и генерации токена:

 ```java
 @RestController
 @RequestMapping(""/auth"")
 public class AuthController {
     @Autowired
     private UserDetailsService userDetailsService;
     @Autowired
     private PasswordEncoder passwordEncoder;

     @PostMapping(""/login"")
     public ResponseEntity<String> login(@RequestBody LoginRequest request) {
         UserDetails user = userDetailsService.loadUserByUsername(request.getUsername());
         if (passwordEncoder.matches(request.getPassword(), user.getPassword())) {
             String token = JwtUtils.generateToken(request.getUsername());
             return ResponseEntity.ok(token);
         }
         throw new BadCredentialsException(""Invalid credentials"");
     }
 }

 public record LoginRequest(String username, String password) {}
 ```
Пользователь вводит логин и пароль. отправляет запрос на сервер. оттудда аксесс токен приходит. И теперь этот токен добавлчется в заголоки.
Ключевые моменты
 - Stateless: Сессии не используются (`STATELESS`).
 - Безопасность: Всегда передавайте токен через HTTPS.
 - Срок действия: Токены должны иметь ограниченное время жизни (реализуйте refresh-токены при необходимости).
 
### Как можно ограничить доступ к определенным URL в вашем приложении с использованием Spring Security?

Базовые правила доступа
 Используйте метод `authorizeHttpRequests()` для настройки правил:

 ```java
 @Configuration
 @EnableWebSecurity
 public class SecurityConfig {

     @Bean
     public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
         http
             .authorizeHttpRequests(auth -> auth
                 .requestMatchers(""/public/"").permitAll()       // Доступ без аутентификации
                 .requestMatchers(""/admin/"").hasRole(""ADMIN"")    // Только для ADMIN
                 .requestMatchers(""/user/"").hasAnyRole(""USER"", ""ADMIN"") // Для USER или ADMIN
                 .requestMatchers(""/api/"").authenticated()       // Для любых авторизованных
                 .anyRequest().denyAll()                           // Блокировать всё остальное
             )
```

2. Сопоставление URL (Ant/Regex Paths)
 Spring Security поддерживает разные стили паттернов:

 ```java
 .requestMatchers(""/resources/"", ""/static/"").permitAll()  // Ant-шаблоны
 .requestMatchers(RegexRequestMatcher.regexMatcher(""/v[0-9]/.*"")).permitAll() // Regex
 ```
 
3. Методы HTTP
 Можно ограничивать доступ по типу запроса (GET, POST и т.д.):

 ```java
 .requestMatchers(HttpMethod.POST, ""/api/users"").hasRole(""ADMIN"")
 .requestMatchers(HttpMethod.GET, ""/api/users"").hasAnyRole(""USER"", ""ADMIN"")
 ```
Примеры в контроллере:
```java
    @GetMapping(""/admin/dashboard"")
    @PreAuthorize(""hasRole('ADMIN')"")
    public String adminDashboard() { ... }

    @PostMapping(""/project/{id}"")
    @PreAuthorize(""@securityService.isProjectOwner(#id, authentication.name)"")
    public ResponseEntity<?> updateProject() { ... }
 ```

Важные нюансы
 1. Порядок правил: Spring проверяет правила сверху вниз. Первое совпадение — применяется.

### Как настроить многомерную аутентификацию (Multi-Factor Authentication, MFA) с использованием Spring Security?

Двухфакторная аутентификация (2FA) — это метод обеспечения безопасности, который требует от пользователей предоставления двух форм аутентификации для доступа к их учётным записям. Spring Security предоставляет несколько вариантов реализации 2FA, включая аутентификацию на основе SMS, аутентификацию на основе электронной почты и одноразовые пароли на основе времени (TOTP) аппаратные токены.

1. Выбор факторов аутентификации
Первый фактор (обычно пароль) – стандартная аутентификация через UsernamePasswordAuthenticationFilter.
Второй фактор (например, OTP через SMS, email, TOTP или push-уведомление).

2. Настройка Spring Security
Добавьте зависимости (если используете OTP, может понадобиться spring-security-otp или библиотека для работы с Google Authenticator).
Создайте кастомный AuthenticationProvider для обработки MFA.

3. Процесс аутентификации
После успешного ввода пароля пользователь переходит на второй этап аутентификации.
Генерируйте OTP (если используется) и отправляйте его пользователю (SMS/email).
Создайте отдельный эндпоинт для проверки OTP.

4. Кастомизация UserDetails
Расширьте UserDetails, добавив поле для хранения статуса MFA (например, boolean mfaCompleted).

5. Добавление кастомного фильтра или обработчика
Создайте фильтр (OncePerRequestFilter) или AuthenticationSuccessHandler, который после успешного первого этапа перенаправляет на страницу ввода OTP.
После успешной проверки OTF устанавливайте mfaCompleted = true и завершайте аутентификацию.

6. Конфигурация SecurityFilterChain
Настройте HttpSecurity так, чтобы:
Первый этап (/login) обрабатывался стандартно.
Второй этап (/verify-otp) требовал частичной аутентификации.
После успешной MFA выдавался полный доступ.

7. Хранение состояния MFA
Используйте SecurityContextHolder или сессию для хранения промежуточного состояния аутентификации.

Примерная логика потока:
Пользователь вводит логин/пароль → аутентифицируется (Authentication без полных прав).
Spring Security перенаправляет на /verify-otp.
Пользователь вводит OTP → система проверяет его.
Если OTP верный, аутентификация завершается (mfaCompleted = true), пользователь получает доступ.

Дополнительно:
Для TOTP (Google Authenticator) используйте библиотеку TOTP (например, com.warrenstrange:googleauth).
Для SMS/email можно интегрировать сервисы типа Twilio или Java Mail API

### Как использовать Spring Security для защиты REST API с помощью OAuth2?

Пример, парковщик. Мы даем ключи от машины - а он ее паркует. При этом мы бы хотели сделать так, чтоб не открывал бардачок.
1. Пользователь взаимодействует с приложением-клиентом. 
2. приложение-клиент переадресует браузер для авторизации
3. сервер авторизации запрашивает у пользователя согласие на авторизацию .
4. пользователь дает согласие.
5. Сервер авторизации выдает токен
6. После того приложение клиент берет токен и обращается к серверу ресурсов
7. 
Получает ответ
8. Добавление зависимостей `spring-security-oauth2-resource-server` и `spring-security-oauth2-jose`
 9. Базовая конфигурация (application.yml)spring:
   security:
     oauth2:
       resourceserver:
         jwt:
           issuer-uri: https://your-auth-server.com
           jwk-set-uri: https://your-auth-server.com/.well-known/jwks.json
10. Настройка SecurityConfig
 ```java
 @Configuration
 @EnableWebSecurity
 public class SecurityConfig {

     @Bean
     public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
         http
             .authorizeHttpRequests(auth -> auth
                 .requestMatchers(""/api/public/"").permitAll()
                 .requestMatchers(""/api/admin/"").hasAuthority(""SCOPE_admin"")
                 .anyRequest().authenticated()
             )
             .oauth2ResourceServer(oauth2 -> oauth2
                 .jwt(jwt -> jwt
                     .decoder(jwtDecoder())
                 )
                 // или для opaque-токенов:
                 // .opaqueToken(opaque -> opaque
                 //     .introspector(introspector())
                 // )
             )
             .csrf().disable();
         
         return http.build();
     }

     @Bean
     public JwtDecoder jwtDecoder() {
         return JwtDecoders.fromIssuerLocation(""https://your-auth-server.com"");
     }
 }
 ```

Работа с JWT Claims
 Spring автоматически парсит JWT и создает `JwtAuthenticationToken`. Доступ к claims:
 
```java
 @RestController
 @RequestMapping(""/api/user"")
 public class UserController {

     @GetMapping(""/profile"")
     public ResponseEntity<?> getUserProfile(JwtAuthenticationToken authentication) {
         String username = authentication.getName();
         Map<String, Object> claims = authentication.getTokenAttributes();
         return ResponseEntity.ok(claims);
     }
 }
 ```

 🔧 5. Кастомная конвертация JWT в Authorities
 Чтобы кастомизировать преобразование JWT claims в authorities:

 ```java
 @Bean
 public JwtAuthenticationConverter jwtAuthenticationConverter() {
     JwtGrantedAuthoritiesConverter converter = new JwtGrantedAuthoritiesConverter();
     converter.setAuthoritiesClaimName(""roles"");
     converter.setAuthorityPrefix(""ROLE_"");
     
     JwtAuthenticationConverter jwtConverter = new JwtAuthenticationConverter();
     jwtConverter.setJwtGrantedAuthoritiesConverter(converter);
     return jwtConverter;
 }
 ```

 И подключите в конфигурации:
 ```java
 .oauth2ResourceServer(oauth2 -> oauth2
     .jwt(jwt -> jwt
         .jwtAuthenticationConverter(jwtAuthenticationConverter())
     )
 )
```

### Как настроить собственный фильтр безопасности в Spring Security и в каких случаях это может быть необходимо?

Кастомные фильтры позволяют реализовать специфичную логику безопасности, не предусмотренную стандартными механизмами Spring Security

1. Создание фильтра
 Наследуйтесь от `OncePerRequestFilter` (для гарантированного однократного выполнения) или реализуйте `Filter`:
 ```java
public class CustomAuthFilter extends OncePerRequestFilter {
     
     @Override
     protected void doFilterInternal(
         HttpServletRequest request,
         ServletResponse response,
         FilterChain chain
     ) throws ServletException, IOException {
         
         // 1. Извлечение кастомного токена из заголовка
         String apiKey = request.getHeader(""X-API-KEY"");
         
         // 2. Валидация токена
         if (isValidApiKey(apiKey)) {
             // 3. Создание аутентифицированного объекта
             Authentication auth = new ApiKeyAuthentication(apiKey);
             SecurityContextHolder.getContext().setAuthentication(auth);
         }
         
         // 4. Продолжение цепочки фильтров
         chain.doFilter(request, response);
     }
     
     private boolean isValidApiKey(String apiKey) {
         // Логика проверки ключа (например, проверка в БД)
         return apiKey != null && apiKey.startsWith(""secret_"");
     }
 }
 ```
2. Кастомная аутентификация
 Создайте класс для хранения данных аутентификации:

 ```java
 import org.springframework.security.core.Authentication;

 public class ApiKeyAuthentication implements Authentication {
     private final String apiKey;
     private boolean authenticated;

     public ApiKeyAuthentication(String apiKey) {
         this.apiKey = apiKey;
         this.authenticated = false;
     }

     @Override
     public String getCredentials() {
         return apiKey;
     }

     @Override
     public Object getPrincipal() {
         return apiKey;
     }

     @Override
     public boolean isAuthenticated() {
         return authenticated;
     }

     @Override
     public void setAuthenticated(boolean isAuthenticated) {
         this.authenticated = isAuthenticated;
     }

     // Остальные методы (getAuthorities(), getName() и др.)
 }
 ```

3. Регистрация фильтра в Spring Security
 Добавьте фильтр в цепочку через `HttpSecurity`:

 ```java
 @Configuration
 @EnableWebSecurity
 public class SecurityConfig {

     @Bean
     public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
         http
             .addFilterBefore(
                 new CustomAuthFilter(),
                 UsernamePasswordAuthenticationFilter.class
             )
             .authorizeHttpRequests(auth -> auth
                 .anyRequest().authenticated()
             )
             .csrf().disable();
         
         return http.build();
     }
 }
 ```

Применение
Кастомные токены  
    - API-ключи, HMAC-подписи
    - Пример: `Authorization: Bearer custom-token`