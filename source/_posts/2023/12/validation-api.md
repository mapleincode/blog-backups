---
title: validation-api 
date: 2023-12-20 18:18:42
tags: [java]


---



#### validation-api 内置的 constraints

| **Validation-API** | **概述**                                                     |
| ------------------ | ------------------------------------------------------------ |
| @AssertFalse       | 被注释的元素必须为 false                                     |
| @AssertTrue        | 被注释的元素必须为 true                                      |
| @DecimalMax        | 被注释的元素必须是一个数字，其值必须小于等于指定的最大值     |
| @DecimalMin        | 被注释的元素必须是一个数字，其值必须大于等于指定的最小值     |
| @Digits            | 被注释的元素必须是一个在可接受范围内的数字                   |
| @Email             | 被注释的元素必须是正确格式的电子邮件地址                     |
| @Future            | 被注释的元素必须是将来的日期                                 |
| @FutureOrPresent   | 被注释的元素必须是现在或将来的日期                           |
| @Max               | 被注释的元素必须是一个数字，其值必须小于等于指定的最大值     |
| @Min               | 被注释的元素必须是一个数字，其值必须大于等于指定的最小值     |
| @Negative          | 被注释的元素必须是一个严格的负数（0为无效值）                |
| @NegativeOrZero    | 被注释的元素必须是一个严格的负数（包含0）                    |
| @NotBlank          | 被注释的元素同StringUtils.isNotBlank，只作用在String上，在String属性上加上@NotBlank约束后，该属性不能为null且trim()之后size>0 |
| @NotEmpty          | 被注释的元素同StringUtils.isNotEmpty，作用在集合类上面，在Collection、Map、数组上加上@NotEmpty约束后，该集合对象是不能为null的，并且不能为空集，即size>0 |
| @NotNull           | 被注释的元素不能是Null，作用在Integer上（包括其它基础类），在Integer属性上加上@NotNull约束后，该属性不能为null，没有size的约束；@NotNull作用在Collection、Map或者集合对象上，该集合对象不能为null，但可以是空集，即size=0（一般在集合对象上用@NotEmpty约束） |
| @Null              | 被注释的元素元素是Null                                       |
| @Past              | 被注释的元素必须是一个过去的日期                             |
| @PastOrPresent     | 被注释的元素必须是过去或现在的日期                           |
| @Pattern           | 被注释的元素必须符合指定的正则表达式                         |
| @Positive          | 被注释的元素必须严格的正数（0为无效值）                      |
| @PositiveOrZero    | 被注释的元素必须严格的正数（包含0）                          |
| @Szie              | 被注释的元素大小必须介于指定边界（包括）之间                 |

 

#### hibernate-validator内置的constraints



| hibernate-validator    | 概述                                                         |
| :--------------------- | :----------------------------------------------------------- |
| @CodePointLength       | 验证字符序列的编码点长度在min和max之间，可以通过设置规范化策略来验证规范化值 |
| @ConstraintComposition | 布尔运算符，应用于合成约束注释的所有约束，组合约束注释可以定义组成它的约束的布尔组合，参考ConstraintComposition实现 |
| @CreditCardNumber      | 被注释元素必须是一个有效的信用卡号码，这是Luhn算法的实现，目的是检查用户的错误，而不是信用卡的有效性 |
| @Currency              | 参考moneyaryamount和CurrencyUnit实现                         |
| @EAN                   | 检查被注释的字符序列是否有效，EAN长度为13，支持的类型是String，当字符串为null被认为有效的 |
| @Email                 | 被注释的字符串必须是正确格式的电子邮件地址【已禁用】         |
| @ISBN                  | 检查被注释字符序列是否有效，支持的类型是String，null将被认为有效的，在验证过程中，忽略所有非ISBN字符，所有数字和“X”都被认为是有效的ISBN字符。主要用于证以破折号分隔的ISBN时，这很有用，例如：239-992-190-873-492 |
| @Length                | 被注释的字符串的长度必须在指定的范围内                       |
| @LuhnCheck             | Luhn算法检查约束，用于验证一系列数字通过Luhn Modulo 10校验算法。Luhn Modulo 10的计算方法是把每一个数字加起来，每个数字都是奇数，数字(从右到左)的值乘以2，如果值大于9的，则结果数字的总和在总和之前，支持的类型是String，null被认为有效的 |
| @Mod10Check            | 允许验证一系列数字通过Mod10校验和算法。经典的Mod10是通过把每一个奇数加起来计算出来的数字(从右到左)的值乘以乘数，例如：ISBN-13是Modulo 10校验和乘数= 3，在已知的情况下，代码使用乘数的偶数和奇数数字；为了支持这种实现，Mod10约束使用权重选项，它具有与乘数相同的效果，但为偶数数字，支持的类型是String。null被认为有效的 |
| @Mod11Check            | 允许验证一系列数字通过Mod11校验和算法，对于最常见的Mod11变体的总和计算是通过乘以一个权重最右边的数字(不包括校验数字)到最左边。权重从2开始，每个数字加1。然后结果为11 - (sum % 11)计算校验数字。例如：24187的校验位是3 Sum = 7x2 + 8x3 + 1x4 + 4x5 + 2x6 = 74  11 - (74% 11) = 11 - 8 = 3，所以“24187-3”是一个有效的字符序列 |
| @ModCheck              | 被注解的元素表示验证一系列数字通过mod 10或mod 11校验和算法，支持的类型是String，null被认为有效的【已禁用】 |
| @Normalized            | 验证字符序列是否为规范化形式，可以通过设置规范化策略来验证规范化值 |
| @NotBlank              | 同StringUtils.isNotBlank，只作用在String上，在String属性上加上@NotBlank约束后，该属性不能为null且trim()之后size>0（同validation-api）【已禁用】 |
| @NotEmpty              | 同StringUtils.isNotEmpty，作用在集合类上面，在Collection、Map、数组上加上@NotEmpty约束后，该集合对象是不能为null的，并且不能为空集，即size>0【已禁用】 |
| @Range                 | 被注释的元素必须在合适的范围内                               |
| @SafeHtml              | 验证用户提供的富文本，以确保它不包含恶意代码，如嵌入式元素。注意，这个约束假设您想要验证代表HTML文档正文片段的输入。如果你想要验证代表一个完整HTML文档的输入，在校验的白名单中添加HTML、head和body标记【已禁用】 |
| @ScriptAssert          | 类级约束，它对脚本表达式求值注释的元素。此约束可用于实现验证日常活动，依赖于注释元素的多个属性。脚本表达式可以写在任何脚本或表达式语言中，其中的JSR 223兼容的引擎可以在类路径中找到 |
| @UniqueElements        | 验证集合中的每个对象都是唯一的，即不能找到两个相等的元素集合，这对于JAX-RS很有用，它总是将集合反序列化为一个列表。因此，当重复的将其转换为一个集合时，会被隐式或默认的删除掉 |
| @URL                   | 验证带注释的字符串是一个URL，参数protocol、host和port对应URL的相应部分。可以加上一个额外的正则表达式regexp和flags可以进一步定制URL的验证标准。默认情况下，约束验证使用java.net.URL构造函数来验证字符串，这意味着匹配的协议处理程序需要可用，需要保证程序中以下协议的处理程序在默认JVM-HTTP、HTTPS、FTP文件和JAR中存在的 |

 

 