# 配置与环境变量注入

## 规范与检查边界

- 在 Spring 管理的组件、`@Configuration` 和 `@Bean` 工厂中，已知固定键的少量标量配置使用 `org.springframework.beans.factory.annotation.Value` 的 `@Value` 注入；避免仅为读取这些值而传入 `Environment`，再逐项调用 `getProperty()`。
- 按项目既有方式选择字段、构造参数或 `@Bean` 方法参数注入。配置仍通过 Spring 属性源解析，保留 YAML、配置中心、环境变量等来源；不要改为硬编码大写环境变量名并直接调用 `System.getenv()`。
- 已有成组、类型化的 `@ConfigurationProperties` 配置继续复用，不机械拆成多个 `@Value`。本规则针对散落的配置读取，不要求所有配置改用同一种绑定方式。
- 保留必要的 `Environment` 使用：动态配置键、属性源或活动 Profile 检查，以及业务明确要求的动态读取；结合调用用途说明必要性。非 Spring 启动入口、子进程环境传递等不属于本规则的固定配置注入场景。
- 改造时保留原配置键、默认值、缺失处理、类型转换、校验和 Bean 启用条件。布尔开关直接注入 `boolean` 并沿用原默认值；原先缺失应失败的必填项不能通过新增默认值放宽。原先由空值或空串触发业务校验的配置，也不能无意改成更早的占位符解析失败。
- 涉及注入方式的修改须编译并运行适用的配置或启动测试，确认正常配置可装配，禁用、缺失和非法配置继续保持原行为。

## 正例

少量固定配置通过参数注入，原有业务校验继续留在既有边界：

```java
/**
 * 根据注入配置装配执行策略。
 * @param identityMode 身份模式，缺省空串由原策略校验
 * @param enabled 执行开关，默认关闭
 * @return 执行策略
 */
@Bean
public ExecutionPolicy executionPolicy(@Value("${app.identity-mode:}") String identityMode,
    @Value("${app.execution.enabled:false}") boolean enabled) {
    return new ExecutionPolicy(identityMode, enabled);
}
```

已有配置类承载一组配置时，继续注入该配置类；不要为了套用上例拆散属性绑定。

## 反例

仅为读取两个固定配置而把通用环境对象传入工厂：

```java
@Bean
public ExecutionPolicy executionPolicy(Environment environment) {
    return new ExecutionPolicy(environment.getProperty("app.identity-mode", ""),
        environment.getProperty("app.execution.enabled", Boolean.class, false));
}
```

这种场景应使用上面的 `@Value` 参数注入，并保持空串与 `false` 的默认语义。不能借此把必要的动态环境访问一并删除。
