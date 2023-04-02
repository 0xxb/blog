---
title: Spring MVC 集成 Swagger
tags: 技术
date: 2019-01-22 11:58
comments: true
---

Spring整合Swagger其实很简单，几步搞定，废话不多说。

在pom.xml中添加依赖：
```xml
<!-- swagger -->
<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger2</artifactId>
	<version>2.9.2</version>
</dependency>

<dependency>
	<groupId>io.springfox</groupId>
	<artifactId>springfox-swagger-ui</artifactId>
	<version>2.9.2</version>
</dependency>
```
添加完以后记得Reimport一下

然后添加控制器，控制器名称可以为Swagger2，也可以是其他，代码如下：  

```java
import io.swagger.annotations.ApiOperation;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.service.Contact;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;

@Configuration
@EnableSwagger2
public class Swagger2 {

    static final String VERSION = "1.0.0";

    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.withMethodAnnotation(ApiOperation.class))
                .paths(PathSelectors.any())
                .build();
    }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("API文档")
                .description("API文档")
                .contact(new Contact("Your name", "", "Your email address"))
                .version(VERSION)
                .build();
    }

}

```

这样就成功集成好Swagger了，启动项目后访问 http://域名:端口/swagger-ui.html 就可以看到所有接口。

下面是一个接口文档编写的案例：  

```java
import io.swagger.annotations.ApiImplicitParam;
import io.swagger.annotations.ApiImplicitParams;
import io.swagger.annotations.ApiOperation;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class Test {
    @ApiOperation(value = "测试接口", notes = "接口说明")
    @ApiImplicitParams({
            @ApiImplicitParam(name = "test", value = "测试参数", paramType = "query", dataType = "String")
    })
    @GetMapping("/hello")
    public String index(String test)
    {
        System.out.println(test);
        return "Word";
    }
}

```

Swagger官网：https://swagger.io