---
layout: post
title: springboot中拦截器与跨域配置冲突解决
date: '2019-08-05 10:40:48 +0800'
categories: [JAVA, 框架]
tags: [springboot]
author: tusiman
---
```java
@Configuration
public class GlobalCorsConfig extends WebMvcConfigurerAdapter {
 
    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/**").allowedOrigins("*").allowedMethods("GET", "POST", "OPTIONS", "PUT")
                .allowedHeaders("Content-Type", "X-Requested-With", "accept", "Origin", "Access-Control-Request-Method",
                        "Access-Control-Request-Headers")
                .exposedHeaders("Access-Control-Allow-Origin", "Access-Control-Allow-Credentials")
                .allowCredentials(true).maxAge(3600);
    }
 
}
```

修改为：

```java
@Configuration
public class GlobalCorsConfig {
 
    @Bean
    public CorsFilter corsFilter() {
        CorsConfiguration config = new CorsConfiguration();
        config.addAllowedOrigin("*");
        config.setAllowCredentials(true);
        config.addAllowedMethod("*");
        config.addAllowedHeader("*");
        UrlBasedCorsConfigurationSource configSource = new UrlBasedCorsConfigurationSource();
        configSource.registerCorsConfiguration("/**", config);
        return new CorsFilter(configSource);
    }
 
}
```
解决原理：一个http请求，先走filter，到达servlet后才进行拦截器的处理，所以我们可以把cors放在filter里，就可以优先于权限拦截器执行。
