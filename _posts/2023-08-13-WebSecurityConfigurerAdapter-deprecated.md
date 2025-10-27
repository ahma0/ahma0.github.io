---
title: Spring Security - WebSecurityConfigurerAdapter deprecated 대응
author: dnya0
date:   2023-08-13 23:39:00 +0900
categories: [StudyGroup, Spring]
tag: [StudyGroup, Spring, Study, Java, KNU-Spring-Study]
math: true
mermaid: true
image:
  path: https://user-images.githubusercontent.com/84761609/220085479-19ee260a-d709-4a47-b788-416275d8a2d8.png
  alt: Spring Image
---

## 📌 WebSecurityConfigurerAdapter is deprecated

스프링부트 2.4버전에 만들어진 JWT 강의를 보면서 최신 버전의 프로젝트로 작업하고 있는데 바뀐게 꽤나 많다. Spring Security 6.0 버전 기준으로 WebSecurityConfigurerAdapter를 완전 사용할 수 없게 됐다. 그래서 어떻게 바꿔야 하는지 정리하는 겸 작성해보았다.

기존엔 WebSecurityConfigurerAdapter를 extends한 후에, configure 메소드를 오버라이딩 하는 방식을 사용하였다. 그러나 바뀐 방식은 개발자가 직접 커스텀 할 설정들을 @Bean으로 등록하여 사용해야 한다.

이 코드의 기준은 스프링부트 3.1.2이다.

<br>

## 🥑 HttpSecurity 설정

- 기존 방식

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig extends WebSecurityConfigurerAdapter {
    
    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http
                .authorizeHttpRequests((authz) -> authz
                        .anyRequest().authenticated()
                )
                .httpBasic(withDefaults());
    }
}
```


- 변경된 방식

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
                .authorizeHttpRequests((authz) -> authz
                        .requestMatchers(new AntPathRequestMatcher("/api/hello")).permitAll()
                        .anyRequest().authenticated()
                )
                .httpBasic(withDefaults());

        return http.build();
    }
}

```

<br>

## 🎈 WebSecurity 설정

- 기존 방식

```java
@Configuration
@EnableWebSecurity
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Override
    public void configure(WebSecurity web) {
        web.ignoring().antMatchers("/ignore1", "/ignore2");
    }

}
```


- 변경된 방식

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public WebSecurityCustomizer webSecurityCustomizer() {
        return (web) -> web.ignoring()
                .requestMatchers(
                        new AntPathRequestMatcher("/h2-console/**"),
                        new AntPathRequestMatcher("/favicon.ico"));
    }
}
```

<br>

## JDBC 설정

- 기존 방식

```java
@Configuration
public class SecurityConfiguration {
    @Bean
    public DataSource dataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.H2)
            .addScript(JdbcDaoImpl.DEFAULT_USER_SCHEMA_DDL_LOCATION)
            .build();
    }

    @Bean
    public UserDetailsManager users(DataSource dataSource) {
        UserDetails user = User.withDefaultPasswordEncoder()
            .username("user")
            .password("password")
            .roles("USER")
            .build();
        JdbcUserDetailsManager users = new JdbcUserDetailsManager(dataSource);
        users.createUser(user);
        return users;
    }
}
```


- 변경된 방식

```java
@Configuration
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {
    @Bean
    public DataSource dataSource() {
        return new EmbeddedDatabaseBuilder()
            .setType(EmbeddedDatabaseType.H2)
            .build();
    }

    @Override
    protected void configure(AuthenticationManagerBuilder auth) throws Exception {
        UserDetails user = User.withDefaultPasswordEncoder()
            .username("user")
            .password("password")
            .roles("USER")
            .build();
        auth.jdbcAuthentication()
            .withDefaultSchema()
            .dataSource(dataSource())
            .withUser(user);
    }
}
```

이건 아직 실행을 안해봐서 실제로 작동하는지 모르겠다.

<br>

## +) 추가

- 기존 방식

```java

@Configuration
@EnableWebSecurity
public class SecurityConfiguration extends WebSecurityConfigurerAdapter {

    @Override
    protected void configure(HttpSecurity http) throws Exception {
        http.csrf().disable()
                .sessionManagement()
                .sessionCreationPolicy(SessionCreationPolicy.STATELESS)

                .and()
                .httpBasic().disable()
                .formLogin().disable()
                .addFilter(corsFilter);

        http.authorizeRequests()
                .antMatchers(FRONT_URL+"/main/**")
                .authenticated()
                .anyRequest().permitAll()

                .and()
                //(1)
                .exceptionHandling()
                .authenticationEntryPoint(new CustomAuthenticationEntryPoint());

		//(2)
        http.addFilterBefore(new JwtRequestFilter(), UsernamePasswordAuthenticationFilter.class);
    }
}

```

- 변경된 방식

```java
@Configuration
@EnableWebSecurity
@RequiredArgsConstructor
public class SecurityConfig {

    private final CorsFilter corsFilter;
    public static final String FRONT_URL = "http://localhost:3000";

    @Bean
    public BCryptPasswordEncoder encodePwd() {
        return new BCryptPasswordEncoder();
    }

    @Bean
    protected SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
                .csrf(AbstractHttpConfigurer::disable)
                .sessionManagement((sessionManagement) ->
                        sessionManagement.sessionCreationPolicy(SessionCreationPolicy.STATELESS)
                )
                .httpBasic(AbstractHttpConfigurer::disable)
                .formLogin(AbstractHttpConfigurer::disable)
                .addFilter(corsFilter);

        http
                .authorizeRequests((authz) -> authz
                                .requestMatchers(new AntPathRequestMatcher("/**")).permitAll()
                                .requestMatchers(
                                        new AntPathRequestMatcher("/bookmark/**"),
                                        new AntPathRequestMatcher("/user/mypage/**")
                                ).hasRole(Role.USER.name())
                                .requestMatchers(new AntPathRequestMatcher("/admin/**")).hasRole(Role.ADMIN.name())
                                .anyRequest().authenticated()
                )
                .exceptionHandling((exceptionConfig) ->
                        exceptionConfig.authenticationEntryPoint(new CustomAuthenticationEntryPoint())
                );

        http
                .addFilterBefore(new JwtRequestFilter(), UsernamePasswordAuthenticationFilter.class);

        return http.build();
    }

}
```

------

## 📌 requestMatchers 에러

> [에러 메시지] This method cannot decide whether these patterns are Spring MVC patterns or not.
{: .prompt-danger }

<br>

- 에러 메시지 1

```
Caused by: org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'filterChain' defined in class path resource [study/tutorial/jwt/config/SecurityConfig.class]: Failed to instantiate [org.springframework.security.web.SecurityFilterChain]: Factory method 'filterChain' threw exception with message: This method cannot decide whether these patterns are Spring MVC patterns or not. If this endpoint is a Spring MVC endpoint, please use requestMatchers(MvcRequestMatcher); otherwise, please use requestMatchers(AntPathRequestMatcher).
```

- 에러 메시지 2

```
Caused by: org.springframework.beans.BeanInstantiationException: Failed to instantiate [org.springframework.security.web.SecurityFilterChain]: Factory method 'filterChain' threw exception with message: This method cannot decide whether these patterns are Spring MVC patterns or not. If this endpoint is a Spring MVC endpoint, please use requestMatchers(MvcRequestMatcher); otherwise, please use requestMatchers(AntPathRequestMatcher).
```

- 에러 메시지 3

```
Caused by: java.lang.IllegalArgumentException: This method cannot decide whether these patterns are Spring MVC patterns or not. If this endpoint is a Spring MVC endpoint, please use requestMatchers(MvcRequestMatcher); otherwise, please use requestMatchers(AntPathRequestMatcher).
```

처음 고쳤을 땐 대충 이런 에러들이 떴었다. 이 에러는 requestMatchers 안에 적혀있는 엔드포인트가 MVC 엔드포인트인지 아닌지 구분을 할 수 없다는 뜻이었다.

- 당시 코드

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
                .authorizeHttpRequests((authz) -> authz
                        .requestMatchers("/api/hello").permitAll()
                        .anyRequest().authenticated())
                .httpBasic(withDefaults());

        return http.build();
    }
}

```


- 바뀐 코드

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        http
                .authorizeHttpRequests((authz) -> authz
                        .requestMatchers(new AntPathRequestMatcher("/api/hello")).permitAll()
                        .anyRequest().authenticated()
                )
                .httpBasic(withDefaults());

        return http.build();
    }
}
```

위의 코드처럼 그냥 `"/api/hello"`가 아닌 `new AntPathRequestMatcher("/api/hello")`를 사용해야 한다. 만약 MVC 엔드포인트라면 `.requestMatchers(new AntPathRequestMatcher("/api/hello"))`가 아닌 `.requestMatchers(new MvcRequestMatcher("/api/hello"))` 이런 식으로 고쳐야 한다.