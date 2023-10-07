---
title: bootstrap.yaml 参数获取
date: 2022-12-06 16:57:33
tags: [java]
---



```java
import lombok.Data;
import org.springframework.stereotype.Component;
import org.springframework.beans.factory.annotation.Value;

/**
 * @author maple
 * @since 2022-12-06
 */
@Data
@Component
public class GatewayProperties {
    @Value("${gateway.host}")
    private String host;
}

```

```java
class Test {
    @Autowired
    private GatewayProperties gatewayProperties;
  
  	void Test() {
      String url = gatewayProperties.getHost();
    }
}
```

