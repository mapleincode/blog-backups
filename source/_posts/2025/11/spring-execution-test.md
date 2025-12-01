---
title: Spring execution 测试
date: 2025-11-25 18:01:56
tags: [java, spring, deepseek]
---





```java
// TestService.java

import java.util.ArrayList;
import java.util.List;


public class TestService {
    public void findUser(String hahah, java.lang.Long id) {}
    public void saveUser(String name, int age) {}
    public void deleteUser(Long id, String reason) {}
    public List<String> findAll() { return new ArrayList<>(); }
    public void process() {}

    public void findUserByForm(QueryForm form) {

    }

    public static class QueryForm {

    }
}

```



```java
// TestAspect.java

import org.aspectj.lang.JoinPoint;
import org.aspectj.lang.annotation.Aspect;
import org.aspectj.lang.annotation.Before;
import org.aspectj.lang.reflect.MethodSignature;

import java.util.ArrayList;
import java.util.List;


@Aspect
class TestAspect {
    public static List<String> invokedMethods = new ArrayList<>();

    @Before("execution(* find*(..))")
    public void logFindMethods(JoinPoint jp) {
        invokedMethods.add("method name find: " + jp.getSignature().getName());
    }

    @Before("execution(* *.findUser(.., *.*.Long))")
    public void logMethodsWithLongParam(JoinPoint jp) {
        MethodSignature signature = (MethodSignature) jp.getSignature();
        String[] parameterNames = signature.getParameterNames();
        System.out.println(String.join(", ", parameterNames));

        invokedMethods.add("Long param: " + jp.getSignature().getName());
    }

    @Before("execution(java.util.List *(..))")
    public void logListReturningMethods(JoinPoint jp) {
        invokedMethods.add("List return: " + jp.getSignature().getName());
    }

    @Before("execution(* cn.cloudcare..*.*(..))")
    public void logAllMethods(JoinPoint jp) {
        invokedMethods.add("all: " + jp.getSignature().getName());
    }

    @Before("execution(* *(*..*Form, ..))")
    public void logMethodsXX(JoinPoint jp) {
        invokedMethods.add("Object param: " + jp.getSignature().getName());
    }
}
```



```java
// ExecutionExpressionTest.java

import org.junit.Before;
import org.junit.Test;
import org.springframework.aop.aspectj.annotation.AspectJProxyFactory;

import static org.junit.Assert.assertFalse;
import static org.junit.Assert.assertTrue;

public class ExecutionExpressionTest {

    private TestService testService;
    private TestAspect testAspect;

    @Before
    public void setUp() {
        testAspect = new TestAspect();
        TestAspect.invokedMethods.clear();

        // 创建代理对象
        AspectJProxyFactory proxyFactory = new AspectJProxyFactory(new TestService());
        proxyFactory.addAspect(testAspect);
        testService = proxyFactory.getProxy();
    }

    @Test
    public void testFindMethodsExecution() {
        testService.findUser("", 1L);

        assertTrue(TestAspect.invokedMethods.contains("method name find: findUser"));
        assertTrue(TestAspect.invokedMethods.contains("Long param: findUser"));
        assertTrue(TestAspect.invokedMethods.contains("all: findUser"));

        System.out.println("匹配的方法: " + TestAspect.invokedMethods);
    }

    @Test
    public void testFindObjectMethodsExecution() {
        testService.findUserByForm(new TestService.QueryForm());

        assertTrue(TestAspect.invokedMethods.contains("Object param: findUserByForm"));

        System.out.println("匹配的方法: " + TestAspect.invokedMethods);
    }

    @Test
    public void testSaveMethodExecution() {
        testService.saveUser("John", 25);

        // saveUser 不应该匹配 find* 模式
        assertFalse(TestAspect.invokedMethods.stream()
                .anyMatch(s -> s.contains("find:")));

        // 但应该匹配 all: 模式
        assertTrue(TestAspect.invokedMethods.contains("all: saveUser"));

        System.out.println("匹配的方法: " + TestAspect.invokedMethods);
    }

    @Test
    public void testListReturningMethods() {
        testService.findAll();

        assertTrue(TestAspect.invokedMethods.contains("find: findAll"));
        assertTrue(TestAspect.invokedMethods.contains("List return: findAll"));
        assertTrue(TestAspect.invokedMethods.contains("all: findAll"));

        System.out.println("匹配的方法: " + TestAspect.invokedMethods);
    }

    @Test
    public void testMethodsWithLongParameter() {
        testService.deleteUser(1L, "test");

        assertTrue(TestAspect.invokedMethods.contains("Long param: deleteUser"));
        assertTrue(TestAspect.invokedMethods.contains("all: deleteUser"));

        System.out.println("匹配的方法: " + TestAspect.invokedMethods);
    }
}
```

