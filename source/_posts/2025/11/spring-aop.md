---
title: Spring AOP 中各种 Advice 的实现示例
tags: [java, deepseek]
date: 2025-11-25 16:25:57
---



当然可以！下面我为您详细展示 Spring AOP 中各种 Advice 的实现示例，包括 `@Before`、`@AfterReturning`、`@AfterThrowing`、`@After` 和 `@Around`。

## 1. @Before - 前置通知

在目标方法执行**之前**执行，适用于参数校验、权限检查、日志记录等场景。

### 基础实现
```java
@Aspect
@Component
public class BeforeAdviceExample {

    // 最简单的 Before Advice
    @Before("execution(* com.example.service.UserService.*(..))")
    public void logBeforeMethod() {
        System.out.println("【Before】准备执行方法...");
    }

    // 获取方法信息
    @Before("execution(* com.example.service.*.*(..))")
    public void logBeforeWithJoinPoint(JoinPoint joinPoint) {
        String methodName = joinPoint.getSignature().getName();
        String className = joinPoint.getTarget().getClass().getSimpleName();
        Object[] args = joinPoint.getArgs();
        
        System.out.println("【Before】执行 " + className + "." + methodName);
        System.out.println("【Before】参数: " + Arrays.toString(args));
    }

    // 参数校验示例
    @Before("execution(* com.example.service.UserService.createUser(..)) && args(user,..)")
    public void validateUser(JoinPoint joinPoint, User user) {
        if (user.getName() == null || user.getName().trim().isEmpty()) {
            throw new IllegalArgumentException("用户名不能为空");
        }
        if (user.getAge() < 0 || user.getAge() > 150) {
            throw new IllegalArgumentException("年龄不合法");
        }
        System.out.println("【Before】用户参数校验通过");
    }

    // 权限检查示例
    @Before("execution(* com.example.service.AdminService.*(..))")
    public void checkPermission(JoinPoint joinPoint) {
        // 模拟从安全上下文中获取用户信息
        SecurityContext context = SecurityUtils.getContext();
        if (!context.hasRole("ADMIN")) {
            throw new SecurityException("无权限执行该操作");
        }
        System.out.println("【Before】权限检查通过");
    }
}
```

## 2. @AfterReturning - 返回后通知

在目标方法**成功执行并返回结果后**执行，适用于处理返回结果、记录成功日志等。

```java
@Aspect
@Component
public class AfterReturningAdviceExample {

    // 基础用法 - 只是记录
    @AfterReturning("execution(* com.example.service.UserService.findUser(..))")
    public void logAfterReturning() {
        System.out.println("【AfterReturning】方法执行成功");
    }

    // 获取返回值
    @AfterReturning(
        pointcut = "execution(* com.example.service.UserService.findUser(..))",
        returning = "result"
    )
    public void logReturnValue(JoinPoint joinPoint, Object result) {
        String methodName = joinPoint.getSignature().getName();
        
        if (result != null) {
            System.out.println("【AfterReturning】方法 " + methodName + " 返回: " + result);
        } else {
            System.out.println("【AfterReturning】方法 " + methodName + " 返回 null");
        }
    }

    // 修改返回值（注意：这里不能修改原返回值，但可以记录或触发其他操作）
    @AfterReturning(
        pointcut = "execution(* com.example.service.UserService.getUserStats(..))",
        returning = "stats"
    )
    public void processStats(UserStats stats) {
        if (stats != null) {
            // 可以基于返回值执行一些后处理，如缓存、统计等
            System.out.println("【AfterReturning】处理用户统计: " + stats.getTotalUsers());
            
            // 注意：这里不能修改 stats 对象本身，但可以修改其内部状态（如果是可变对象）
            stats.setProcessedTime(new Date());
        }
    }

    // 成功操作后的清理工作
    @AfterReturning("execution(* com.example.service.FileService.uploadFile(..))")
    public void cleanupTempFiles(JoinPoint joinPoint) {
        Object[] args = joinPoint.getArgs();
        if (args.length > 0 && args[0] instanceof String fileName) {
            System.out.println("【AfterReturning】清理临时文件: " + fileName);
            // 实际项目中可能会删除上传过程中产生的临时文件
        }
    }
}
```

## 3. @AfterThrowing - 异常通知

在目标方法**抛出异常后**执行，适用于异常处理、错误日志记录、事务回滚等。

```java
@Aspect
@Component
public class AfterThrowingAdviceExample {

    // 基础异常通知
    @AfterThrowing("execution(* com.example.service.*.*(..))")
    public void logAfterThrowing() {
        System.out.println("【AfterThrowing】方法执行出现异常");
    }

    // 获取异常信息
    @AfterThrowing(
        pointcut = "execution(* com.example.service.*.*(..))",
        throwing = "ex"
    )
    public void logExceptionDetails(JoinPoint joinPoint, Exception ex) {
        String methodName = joinPoint.getSignature().getName();
        String className = joinPoint.getTarget().getClass().getSimpleName();
        
        System.err.println("【AfterThrowing】" + className + "." + methodName + " 发生异常: " + ex.getMessage());
        System.err.println("【AfterThrowing】异常类型: " + ex.getClass().getSimpleName());
        
        // 在实际项目中，这里可能会将异常信息发送到监控系统
    }

    // 特定异常处理
    @AfterThrowing(
        pointcut = "execution(* com.example.service.UserService.*(..))",
        throwing = "ex"
    )
    public void handleUserServiceException(JoinPoint joinPoint, UserNotFoundException ex) {
        System.err.println("【AfterThrowing】用户未找到异常: " + ex.getUserId());
        // 可以发送通知邮件或记录特定的业务日志
    }

    // 数据库异常处理
    @AfterThrowing(
        pointcut = "execution(* com.example.service.*.*(..))",
        throwing = "ex"
    )
    public void handleDataAccessException(JoinPoint joinPoint, DataAccessException ex) {
        String methodName = joinPoint.getSignature().getName();
        System.err.println("【AfterThrowing】数据库访问异常 in " + methodName + ": " + ex.getMessage());
        // 这里可以记录详细的SQL信息或执行回滚补偿操作
    }
}
```

## 4. @After - 后置通知（Finally通知）

在目标方法**执行之后**执行，无论成功还是异常都会执行，适用于资源清理、释放连接等。

```java
@Aspect
@Component
public class AfterAdviceExample {

    // 基础后置通知
    @After("execution(* com.example.service.FileService.*(..))")
    public void cleanupResources() {
        System.out.println("【After】执行资源清理");
    }

    // 带方法信息的后置通知
    @After("execution(* com.example.service.*.*(..))")
    public void logMethodCompletion(JoinPoint joinPoint) {
        String methodName = joinPoint.getSignature().getName();
        System.out.println("【After】方法 " + methodName + " 执行完成（无论成功或失败）");
    }

    // 资源释放示例
    @After("execution(* com.example.service.DatabaseService.executeQuery(..))")
    public void releaseDatabaseResources(JoinPoint joinPoint) {
        try {
            // 模拟释放数据库连接、关闭Statement等
            System.out.println("【After】释放数据库资源");
            // ConnectionPool.releaseConnection();
        } catch (Exception e) {
            System.err.println("【After】资源释放失败: " + e.getMessage());
        }
    }

    // 文件操作后清理
    @After("execution(* com.example.service.FileService.*(..))")
    public void cleanupFileHandles(JoinPoint joinPoint) {
        System.out.println("【After】清理文件句柄和流资源");
        // 在实际项目中，这里会确保文件流被正确关闭
    }
}
```

## 5. @Around - 环绕通知（功能最强大）

**包围**着目标方法的执行，可以控制是否执行目标方法、修改参数、处理返回值等。

```java
@Aspect
@Component
public class AroundAdviceExample {

    // 性能监控
    @Around("execution(* com.example.service.*.*(..))")
    public Object measureExecutionTime(ProceedingJoinPoint pjp) throws Throwable {
        long startTime = System.currentTimeMillis();
        
        try {
            // 执行原方法
            Object result = pjp.proceed();
            return result;
        } finally {
            long endTime = System.currentTimeMillis();
            long duration = endTime - startTime;
            
            String methodName = pjp.getSignature().getName();
            System.out.println("【Around】方法 " + methodName + " 执行耗时: " + duration + "ms");
            
            // 如果执行时间过长，记录警告
            if (duration > 1000) {
                System.out.println("【Around】警告: " + methodName + " 执行时间过长!");
            }
        }
    }

    // 缓存示例
    @Around("execution(* com.example.service.UserService.findUserById(Long)) && args(userId)")
    public Object cacheUser(ProceedingJoinPoint pjp, Long userId) throws Throwable {
        String cacheKey = "user:" + userId;
        
        // 先尝试从缓存获取
        Object cachedValue = CacheManager.get(cacheKey);
        if (cachedValue != null) {
            System.out.println("【Around】从缓存获取用户: " + userId);
            return cachedValue;
        }
        
        // 缓存中没有，执行原方法
        System.out.println("【Around】缓存未命中，查询数据库: " + userId);
        Object result = pjp.proceed();
        
        // 将结果放入缓存
        if (result != null) {
            CacheManager.put(cacheKey, result, 300); // 缓存5分钟
            System.out.println("【Around】用户数据已缓存: " + userId);
        }
        
        return result;
    }

    // 重试机制
    @Around("execution(* com.example.service.ExternalService.callApi(..))")
    public Object retryOnFailure(ProceedingJoinPoint pjp) throws Throwable {
        int maxRetries = 3;
        int retryCount = 0;
        Exception lastException = null;
        
        while (retryCount < maxRetries) {
            try {
                System.out.println("【Around】第 " + (retryCount + 1) + " 次尝试");
                return pjp.proceed();
            } catch (Exception e) {
                lastException = e;
                retryCount++;
                System.out.println("【Around】调用失败，准备重试: " + e.getMessage());
                
                if (retryCount < maxRetries) {
                    // 等待一段时间后重试
                    Thread.sleep(1000 * retryCount);
                }
            }
        }
        
        throw new RuntimeException("方法执行失败，重试 " + maxRetries + " 次后仍失败", lastException);
    }

    // 参数校验和修改
    @Around("execution(* com.example.service.UserService.updateUser(..)) && args(user)")
    public Object validateAndProcessUser(ProceedingJoinPoint pjp, User user) throws Throwable {
        // 参数校验
        if (user.getId() == null) {
            throw new IllegalArgumentException("用户ID不能为空");
        }
        
        // 参数处理（添加审计信息）
        user.setUpdatedAt(new Date());
        user.setUpdatedBy(getCurrentUserId());
        
        System.out.println("【Around】参数校验和处理完成，准备执行方法");
        
        // 执行原方法，可以修改参数数组
        Object[] args = pjp.getArgs();
        // 如果需要修改参数，可以创建新的args数组
        
        Object result = pjp.proceed(args);
        
        // 可以修改返回值
        if (result instanceof Boolean && (Boolean) result) {
            System.out.println("【Around】用户更新成功");
            // 可以返回包装后的结果
            return new OperationResult(true, "更新成功");
        }
        
        return result;
    }

    // 权限控制和结果包装
    @Around("execution(* com.example.service.AdminService.manageSystem(..))")
    public Object checkPermissionAndWrapResult(ProceedingJoinPoint pjp) throws Throwable {
        // 权限检查
        if (!hasAdminPermission()) {
            return new OperationResult(false, "权限不足");
        }
        
        try {
            Object result = pjp.proceed();
            // 统一包装成功结果
            return new OperationResult(true, "操作成功", result);
        } catch (BusinessException e) {
            // 捕获业务异常，返回规范的错误结果
            return new OperationResult(false, e.getMessage());
        }
    }
    
    private boolean hasAdminPermission() {
        // 模拟权限检查
        return SecurityUtils.getCurrentUser().hasRole("ADMIN");
    }
    
    private String getCurrentUserId() {
        // 模拟获取当前用户ID
        return "user123";
    }
}
```

## 辅助类定义

```java
// 用于示例的辅助类
@Data
class User {
    private Long id;
    private String name;
    private Integer age;
    private Date updatedAt;
    private String updatedBy;
}

class UserStats {
    private int totalUsers;
    private Date processedTime;
    // getter/setter
}

class OperationResult {
    private boolean success;
    private String message;
    private Object data;
    
    public OperationResult(boolean success, String message) {
        this.success = success;
        this.message = message;
    }
    
    public OperationResult(boolean success, String message, Object data) {
        this.success = success;
        this.message = message;
        this.data = data;
    }
    // getter/setter
}

// 自定义异常
class UserNotFoundException extends RuntimeException {
    private Long userId;
    public UserNotFoundException(Long userId) {
        super("用户未找到: " + userId);
        this.userId = userId;
    }
    public Long getUserId() { return userId; }
}

// 模拟工具类
class SecurityUtils {
    public static SecurityContext getContext() { return new SecurityContext(); }
    public static User getCurrentUser() { return new User(); }
}

class CacheManager {
    public static Object get(String key) { return null; }
    public static void put(String key, Object value, int seconds) {}
}

class SecurityContext {
    public boolean hasRole(String role) { return true; }
}
```

这些示例涵盖了 Spring AOP 中各种 Advice 的常见使用场景，您可以根据实际需求进行修改和扩展。
