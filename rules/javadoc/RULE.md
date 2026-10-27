# Javadoc 与中文注释

## 规范与检查边界

- 所有类、接口及 public 方法都必须有 `/** ... */` Javadoc，说明类型职责或方法用途。
- 包含嵌套类型、enum/record、public 构造、public 重写方法，以及接口中隐式 public 的方法；生产和测试代码均适用。
- 普通行注释、普通块注释及 `@Override` 不能替代 Javadoc。直接继承框架方法即可，不为补注释新增无业务意义的重写；不要求给 Lombok/框架生成的方法补写源码。

### 类型作者与日期

- 每个类型的 Javadoc 必须包含非空 `@author` 和 `@date`，日期格式为 `yyyy-MM-dd`，表示类型创建日期；嵌套类型、接口、enum/record 同样适用。
- 作者填写实际作者姓名或可核实的身份标识；已有作者、创建日期保持原始信息，不因修改代码而覆盖。新建类型使用实际创建日期；为既有类型补充时依据可靠来源，不臆造作者或用本轮日期冒充创建日期。无法确认时报告待补充信息。
- `@date` 是自定义 Javadoc 标签；若项目生成 Javadoc，需在其工具配置中声明该标签，不能据此静默省略日期。

### 参数、返回值与异常

- 每个需 Javadoc 的方法和构造方法都必须为每个显式形参逐项填写 `@param 参数名 中文说明`，名称与签名一致，说明参数含义及必要约束。泛型形参使用 `@param <T> 中文说明`；类型泛型形参和 record 组件也须在类型 Javadoc 中逐项说明。
- 非 `void` 方法必须填写 `@return 中文说明`，说明返回值含义及适用的空值、空集合等语义。`void` 方法及构造方法不写 `@return`，无形参时不添加虚构的 `@param`。
- `throws` 声明中的每种异常，以及方法主动抛出或作为对外契约传播的非受检异常，必须逐项填写 `@throws 异常类型 中文说明`，说明触发条件。阅读实现及委托调用确认异常语义，不为凑标签罗列所有潜在底层异常；方法内部捕获且不再抛出的异常无需列出，无对外异常时不添加虚构的 `@throws`。
- 已编写 Javadoc 的非 public 方法或构造方法也遵循上述标签完整性要求；不因此扩大必须新增 Javadoc 的可见性范围。

### 中文注释

- 检查范围内所有 Java 注释的自然语言说明必须使用中文，包括 Javadoc 正文、标签说明、行注释和普通块注释。
- Javadoc 标签、代码标识符、实际作者身份标识、日期、URL、必要技术名称与协议原文保持准确，不要求翻译这些内容；不得借此保留整句英文说明。
- 注释应说明真实用途和语义，不能只重复参数名、返回类型或异常类名。

## 正例

```java
/**
 * 提供用户查询能力。
 *
 * @author 张三
 * @date 2026-10-10
 */
public interface UserService {
    /**
     * 根据用户标识查询用户。
     *
     * @param userId 用户标识，不能为空或空白
     * @return 查询到的用户，不存在时返回 null
     * @throws IllegalArgumentException 用户标识为空或空白时抛出
     */
    User find(String userId);
}

/**
 * 保存用户查询结果。
 *
 * @param id 用户标识
 * @author 张三
 * @date 2026-10-10
 */
public record User(String id) {}

/**
 * 提供用户数据刷新能力。
 *
 * @author 张三
 * @date 2026-10-10
 */
public interface UserRefresher {
    /**
     * 刷新用户数据。
     *
     * @throws IOException 读取用户数据失败时抛出
     */
    void refresh() throws IOException;
}
```

接口方法没有写 `public`，仍需 Javadoc；record 也属于类型。示例作者与日期仅用于展示格式，实际补充必须核实。
`refresh` 没有参数且返回 `void`，只需用途和异常说明，不添加 `@param` 或 `@return`。

## 反例

```java
// 查询用户的领域接口。
public interface UserService {
    /* 根据用户 ID 查询用户。 */
    User find(String userId);
}
```

类型使用 `//`，方法使用 `/* ... */`，两者都不是 Javadoc；类型还缺少作者与日期，方法缺少参数和返回值说明。

```java
/** 用户查询接口。 */
public interface UserService {
    /**
     * Find a user.
     *
     * @param userId userId
     * @return User
     * @throws IllegalArgumentException IllegalArgumentException
     */
    User find(String userId);
}
```

类型缺少 `@author` 和 `@date`；方法正文为英文，标签说明只重复标识符，未说明参数、返回值和异常触发条件。
修复时补齐可核实的类型元信息和中文语义说明，并依据真实异常契约核对 `@throws`。
