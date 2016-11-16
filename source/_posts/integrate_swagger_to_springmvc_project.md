title: 快速集成Springfox Swagger到SpringMVC4
date: 2015-12-31 15:18:23
comments: true
categories: java
tags:
- Spring
- Maven
---

为了偷懒不用每次更新Api就要去维护文档，于是想起了Swagger这个功能强大，UI界面漂亮，可以在线测试的工具。直接部署Swagger稍微复杂，本文使用的是Springfox，他的前身是swagger-springmvc。下文将介绍如何通过Maven快速集成Springfox到SpringMVC4项目中。

# 一、Maven
petstore是官方的一个demo，加入此依赖是为了演示接口文档，如开发时需要参考接口描述的编写可保留
 ``` xml
 <!--官方petstore demo-->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-petstore</artifactId>
    <version>2.2.2</version>
</dependency>
<!--springfox核心jar-->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger2</artifactId>
    <version>2.2.2</version>
</dependency>
<!--静态页面依赖的webjar-->
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-swagger-ui</artifactId>
    <version>2.2.2</version>
</dependency>
 ```
 <!--more-->

# 二、Springfox配置文件
 1. SpringfoxDocConfig.java
使用注解定义扫描路径及api路径的正则以及说明的ApiInfo。
更详细的配置类写法请参照官方的 [Reference](http://springfox.github.io/springfox/docs/current/#springfox-configuration-and-demo-applications)
 ``` java
package com.example.swagger;

import com.google.common.base.Predicate;
import com.google.common.collect.Sets;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.EnableWebMvc;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.AuthorizationScopeBuilder;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.AuthorizationScope;
import springfox.documentation.service.BasicAuth;
import springfox.documentation.service.SecurityReference;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spi.service.contexts.SecurityContext;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;
import java.util.ArrayList;
import static com.google.common.base.Predicates.or;
import static com.google.common.collect.Lists.newArrayList;
import static springfox.documentation.builders.PathSelectors.regex;

@Configuration
@EnableWebMvc //NOTE: Only needed in a non-springboot application
@EnableSwagger2 //Enable swagger 2.0 spec
@ComponentScan(basePackages = {
        "com.example.modules.control"
        })
public class SpringfoxDocConfig {
    @Bean
    public Docket appApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("app")
                .apiInfo(apiInfo())
                .forCodeGeneration(true)
                .select()
                .paths(appPaths())
                .build();
    }
    private Predicate<String> appPaths() {
        return or(
                regex("/api/user.*"),
                regex("/api/acct.*"),
                regex("/api/pet.*")
        );
    }

    @Bean
    public Docket adminApi(){
        return new Docket(DocumentationType.SWAGGER_2)
                .groupName("admins")
                .apiInfo(apiInfo())
                .forCodeGeneration(true)
                .select()
                .paths(regex("/admins.*"))
                .build();
    }


    @Bean
    public Docket userApi() {
        AuthorizationScope[] authScopes = new AuthorizationScope[1];
        authScopes[0] = new AuthorizationScopeBuilder()
                .scope("read")
                .description("read access")
                .build();
        SecurityReference securityReference = SecurityReference.builder()
                .reference("test")
                .scopes(authScopes)
                .build();

        ArrayList<SecurityContext> securityContexts = newArrayList(SecurityContext.builder().securityReferences
                (newArrayList(securityReference)).build());
        return new Docket(DocumentationType.SWAGGER_2)
                .securitySchemes(newArrayList(new BasicAuth("test")))
                .securityContexts(securityContexts)
                .groupName("user")
                .apiInfo(apiInfo())
                .select()
                .paths(userOnlyEndpoints())
                .build();
    }
    private Predicate<String> userOnlyEndpoints() {
        return new Predicate<String>() {
            @Override
            public boolean apply(String input) {
                return input.contains("user");
            }
        };
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("文档显示标题 (REST API)")
                .description("文档显示说明")
                .termsOfServiceUrl("http://www.example.com")
                .contact("admin@example.com")
                .license("Apache License Version 2.0")
                .licenseUrl("https://github.com/springfox/springfox/blob/master/LICENSE")
                .version("版本")
                .build();
    }

    @Bean
    public Docket configSpringfoxDocket_all() {
        return new Docket(DocumentationType.SWAGGER_2)
                .produces(Sets.newHashSet("application/json"))
                .consumes(Sets.newHashSet("application/json"))
                .protocols(Sets.newHashSet("http", "https"))
                .forCodeGeneration(true)
                .apiInfo(apiInfo())
                .select().paths(regex(".*"))
                .build();
    }
}

 ```

 2. 配置spring-mvc.xml，添加静态资源访问
 ``` xml
 <!-- springfox-swagger-ui 静态资源访问 -->
 <mvc:resources mapping="swagger-ui.html" location="classpath:/META-INF/resources/"/>
 <mvc:resources mapping="/webjars/**" location="classpath:/META-INF/resources/webjars/"/>
 ```

 3. 访问路径
 http://baseurl/swagger-resources   ApiDoc Resources.json
 如：
  ``` json
 [ {
   "name" : "admins",
   "location" : "/v2/api-docs?group=admins",
   "swaggerVersion" : "2.0"
 }, {
   "name" : "app",
   "location" : "/v2/api-docs?group=app",
   "swaggerVersion" : "2.0"
 }, {
   "name" : "default",
   "location" : "/v2/api-docs",
   "swaggerVersion" : "2.0"
 }, {
   "name" : "user",
   "location" : "/v2/api-docs?group=user",
   "swaggerVersion" : "2.0"
 } ]
 ```
 http://baseurl/swagger-ui.html     Swagger UI ApiDoc
 如：
 ![Swagger UI ApiDoc](http://imogos.oss-cn-hangzhou.aliyuncs.com/integrate_swagger_to_springmvc_project.png)
