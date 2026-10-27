# SLF4J 日志

## 规范与检查边界

- Java 运行日志统一使用 SLF4J。项目已有 Lombok 时，使用 `lombok.extern.slf4j.Slf4j` 和生成的 `log`。
- 不手写 `LoggerFactory.getLogger(...)`，不单独创建日志 Factory；不用 `System.out/err`、JUL 或 Log4j/Logback 原生 API 直接输出业务日志。
- 保留日志级别、消息、参数和异常语义。不为风格检查擅自添加 Lombok；没有 Lombok 时沿用项目既有的 SLF4J 接入方式。
- 测试为验证日志适配器而构造 Logback Event、配置测试 LoggerContext，不等同于业务直接输出，须结合用途判断。

## 正例

项目已有 Lombok：

```java
import lombok.extern.slf4j.Slf4j;

/** 记录任务完成事件。 */
@Slf4j
public class RunService {
    /** 保存任务完成日志。 */
    public void completed(String runId) {
        log.info("Run completed: {}", runId);
    }
}
```

## 反例

```java
private static final Logger log = LoggerFactory.getLogger(RunService.class);

public void completed(String runId) {
    System.out.println("Run completed: " + runId);
}
```

手写 LoggerFactory 和标准输出分别违反日志获取与业务输出约定；修复时保留原有日志内容与级别。
