---
title 切入点指示符 （Pointcut Designator）
date: 2025-11-25 16:48:22
tags: [java, spring, deepseek]
---

好的，`execution` 是 Spring AOP 中最重要、最常用的**切入点指示符（Pointcut Designator）**，用于定义哪些方法应该被拦截。

## 基本语法结构

```java
execution(修饰符? 返回类型 声明类型? 方法名(参数类型列表) 异常类型?)
```

其中 `?` 表示可选部分。

**完整格式：**
```java
execution([modifiers] return-type [declaring-type].method-name([parameters]) [throws])
```

---

## 各部分详解

### 1. 返回类型（Return Type）
- **必须指定**
- 可以使用 `*` 通配符表示任意返回类型

```java
// 匹配任意返回类型
execution(* ...)

// 匹配具体返回类型
execution(String ...)
execution(void ...)
execution(java.util.List ...)
```



### 2. 声明类型（Declaring Type）

- **可选**，指定类或接口
- 需要包含包名

```java
// 匹配指定类中的所有方法
execution(* com.example.service.UserService.*(..))

// 匹配包下所有类的所有方法
execution(* com.example.service.*.*(..))

// 匹配包及其子包下所有类的所有方法
execution(* com.example.service..*.*(..))
```

### 3. 方法名（Method Name）
- 支持 `*` 通配符

```java
// 匹配所有方法
execution(* *(..))

// 匹配以 get 开头的方法
execution(* get*(..))

// 匹配具体方法名
execution(* getUser(..))
execution(* findById(..))
```



### 4. 参数类型列表（Parameters）

- `()` - 匹配无参数方法
- `(..)` - 匹配任意数量、任意类型的参数（包括无参数）
- `(type)` - 匹配指定类型的一个参数
- `(type, type)` - 匹配指定类型的两个参数
- `(type, ..)` - 匹配第一个参数为指定类型，后面跟任意数量任意类型参数
- `(.., type)` - 匹配最后一个参数为指定类型，前面可以是任意类型参数



```java
// 匹配无参数方法
execution(* *.getAll())

// 匹配任意参数的方法（最常用）
execution(* *.save(..))

// 匹配只有一个String参数的方法
execution(* *.findByName(String))

// 匹配第一个参数为Long，第二个参数为String的方法
execution(* *.update(Long, String))

// 匹配第一个参数为String，后面跟任意参数的方法
execution(* *.search(String, ..))
```

---

## 完整示例组合

### 基础示例
```java
// 匹配 UserService 中所有方法
execution(* com.example.service.UserService.*(..))

// 匹配 service 包中所有类的所有方法
execution(* com.example.service.*.*(..))

// 匹配 service 包及其子包中所有类的所有方法
execution(* com.example.service..*.*(..))

// 匹配所有以 find 开头的方法
execution(* find*(..))
```

### 实际应用示例
```java
@Aspect
@Component
public class ServiceAspect {
    
    // 匹配 UserService 中所有 public 方法
    @Before("execution(public * com.example.service.UserService.*(..))")
    public void logUserService() {
        System.out.println("执行 UserService 方法");
    }
    
    // 匹配 service 包中所有返回 List 的方法
    @Before("execution(java.util.List com.example.service.*.*(..))")
    public void logListReturningMethods() {
        System.out.println("执行返回 List 的方法");
    }
    
    // 匹配查询相关方法（以 find/get/query 开头）
    @Before("execution(* com.example.service.*.find*(..)) || " +
            "execution(* com.example.service.*.get*(..)) || " +
            "execution(* com.example.service.*.query*(..))")
    public void logQueryMethods() {
        System.out.println("执行查询方法");
    }
    
    // 匹配更新相关方法（以 update/save/delete 开头）
    @Before("execution(* com.example.service.*.update*(..)) || " +
            "execution(* com.example.service.*.save*(..)) || " +
            "execution(* com.example.service.*.delete*(..))")
    public void logUpdateMethods() {
        System.out.println("执行更新方法");
    }
}
```

---

## 高级用法和技巧

### 1. 使用包路径通配
```java
// 匹配 com.example 包下任何子包中的 Service 结尾的类
execution(* com.example..*Service.*(..))

// 匹配 impl 包中的所有类
execution(* com.example.service.impl.*.*(..))
```

### 2. 组合多个条件
```java
// 使用 && (与), || (或), ! (非) 组合条件
@Before("execution(* com.example.service.*.*(..)) && " +
        "!execution(* com.example.service.*.get*(..))")
public void logNonGetMethods() {
    System.out.println("执行非查询方法");
}
```

### 3. 参数类型匹配技巧

```java
// 匹配包含特定类型参数的方法
execution(* *(*..Long, ..))                    // 第一个参数为 Long 类型
execution(* *(*..String, *..String, ..))        // 前两个参数都是 String 类型
execution(* *(.., *..Long))                    // 最后一个参数为 Long 类型
```



> 参数类型里，* 只能匹配一个包，DeepSeek 返回的原文是 `*.Long` ，实测并不能匹配。
>
> 实际模糊匹配应该是 `*..Long` 或者 `*.*.Long` 以匹配 java.lang.Long
>
> 实际上对于 Long 这种基础类型不需要完整路径
>
> 还有一点是，对于方法，使用类似 `*.getUser(*)`是可以匹配所有方法的。



### 4. 在注解中使用

```java
// 定义切入点
@Pointcut("execution(* com.example.service.*.*(..))")
public void serviceLayer() {}

// 重用切入点
@Before("serviceLayer()")
public void logServiceAccess() {
    System.out.println("访问 Service 层");
}

// 组合多个切入点
@Pointcut("execution(* com.example.service.*.find*(..))")
public void findMethods() {}

@Pointcut("execution(* com.example.service.*.get*(..))")
public void getMethods() {}

@Before("findMethods() || getMethods()")
public void logQueryOperations() {
    System.out.println("执行查询操作");
}
```

---

## 实际项目中的最佳实践

### 1. 按层拦截
```java
// Service 层切入点
@Pointcut("execution(* com.yourcompany..service..*.*(..))")
public void serviceLayer() {}

// DAO/Repository 层切入点  
@Pointcut("execution(* com.yourcompany..repository..*.*(..))")
public void repositoryLayer() {}

// Controller 层切入点
@Pointcut("execution(* com.yourcompany..controller..*.*(..))")
public void controllerLayer() {}
```

### 2. 按操作类型拦截
```java
// 写操作
@Pointcut("execution(* com..*.save*(..)) || " +
          "execution(* com..*.update*(..)) || " +
          "execution(* com..*.delete*(..)) || " +
          "execution(* com..*.insert*(..))")
public void writeOperations() {}

// 读操作
@Pointcut("execution(* com..*.find*(..)) || " +
          "execution(* com..*.get*(..)) || " +
          "execution(* com..*.query*(..)) || " +
          "execution(* com..*.select*(..))")
public void readOperations() {}
```

### 3. 完整实战示例
```java
@Aspect
@Component
public class BusinessAspect {
    
    // 定义各种切入点
    @Pointcut("execution(* com.example..service..*.*(..))")
    public void serviceMethods() {}
    
    @Pointcut("execution(* com.example..service..*.save*(..))")
    public void saveMethods() {}
    
    @Pointcut("execution(* com.example..service..*.find*(..))")
    public void findMethods() {}
    
    @Pointcut("execution(* com.example..service..*.delete*(..))")
    public void deleteMethods() {}
    
    @Pointcut("within(@org.springframework.stereotype.Service *)")
    public void serviceAnnotation() {}
    
    // 组合使用
    @Before("serviceMethods() && saveMethods()")
    public void beforeSave(JoinPoint joinPoint) {
        System.out.println("准备保存数据: " + joinPoint.getSignature().getName());
        Object[] args = joinPoint.getArgs();
        if (args.length > 0) {
            System.out.println("参数: " + Arrays.toString(args));
        }
    }
    
    @Around("serviceMethods() && findMethods()")
    public Object aroundFind(ProceedingJoinPoint pjp) throws Throwable {
        long start = System.currentTimeMillis();
        try {
            return pjp.proceed();
        } finally {
            long duration = System.currentTimeMillis() - start;
            System.out.println("查询方法 " + pjp.getSignature().getName() + " 耗时: " + duration + "ms");
        }
    }
    
    @AfterThrowing(pointcut = "serviceMethods()", throwing = "ex")
    public void afterThrowing(JoinPoint joinPoint, Exception ex) {
        System.err.println("方法 " + joinPoint.getSignature().getName() + " 执行异常: " + ex.getMessage());
    }
}
```

## 重要注意事项

1. **性能考虑**：过于宽泛的 `execution` 表达式会影响性能，尽量精确匹配
2. **包路径**：使用 `..` 匹配子包时要注意范围，避免拦截到不期望的类
3. **访问修饰符**：通常不需要指定，默认匹配所有访问级别的方法
4. **异常声明**：在实际项目中很少使用异常类型匹配

掌握 `execution` 表达式是使用 Spring AOP 的关键，建议根据项目结构设计合理的切入点表达式。