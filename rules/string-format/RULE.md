# 字符串格式化与编码

## 规范与检查边界

- 生产和测试代码中，字符串拼接统一使用 `String.format(Locale, format, args...)`，禁止使用 `+` 或 `+=` 拼接字符串，包括相邻字符串字面量及跨行拼接。不以 `concat`、`StringBuilder`/`StringBuffer.append` 或 `String.join` 替代此类拼接。
- 每次 `String.format` 必须显式传入非空 `Locale`。机器标识、路径、协议、错误信息等默认使用 `Locale.ROOT`；确需本地化展示时传入明确的目标 `Locale`。禁止依赖 `Locale.getDefault()`、无 Locale 的重载或 `String.formatted(...)`。
- `Locale` 控制格式化规则，UTF-8 控制字符与字节转换，两者不能互相替代。字符串不涉及字节转换时仅指定 Locale；涉及编码、解码或文本 I/O 时，同时显式使用 `StandardCharsets.UTF_8`，或使用 API 对应的显式 UTF-8 参数。禁止省略字符集或使用平台默认字符集。
- [SLF4J 日志](../slf4j-logging/RULE.md)继续使用 `{}` 参数占位符，无需预先调用 `String.format`；日志消息也禁止 `+` 拼接，保留异常参数与日志级别语义。
- 数值加法和自增不属于字符串拼接。必须阅读全文并判断操作数类型，不因出现 `+` 就认定违规；如 `"total=" + (left + right)`，仅外层字符串拼接需要改为格式化。
- 纯字符串字面量不要求套用格式化。超长常量优先保留完整字面量，按[行宽规则](../line-length/RULE.md)处理；注解值、case 标签等要求编译期常量的位置不得改成 `String.format`，应改为单个字面量或引用合法常量。
- 修复时保留原有内容、空白、换行、求值顺序及数值运算语义。动态内容作为参数传入，不作为格式模板；模板中的字面量 `%` 写为 `%%`。不顺带改变既有协议的编码；若其明确要求其他字符集，报告与本规则的冲突，由用户确定改动范围。
- 格式化调用继续遵守[行宽规则](../line-length/RULE.md)和[避免过度换行](../avoid-excessive-wrapping/RULE.md)。

## 正例

```java
import java.nio.charset.StandardCharsets;
import java.util.Locale;

String message = String.format(Locale.ROOT, "Run %s finished: %d%%", runId, progress);
String path = String.format(Locale.ROOT, "%s/%s", directory, fileName);
String total = String.format(Locale.ROOT, "total=%d", left + right);
String display = String.format(Locale.FRANCE, "%.2f", amount);
byte[] payload = message.getBytes(StandardCharsets.UTF_8);
String decoded = new String(payload, StandardCharsets.UTF_8);
log.info("Run {} finished: {}%", runId, progress);
```

格式化显式指定 Locale；涉及字节转换时另行指定 UTF-8。数值加法保留，日志沿用 SLF4J 占位符。

## 反例

```java
String message = "Run " + runId + " finished";
message += "!";
String path = directory.concat("/").concat(fileName);
String joined = String.join("/", directory, fileName);
String built = new StringBuilder().append(directory).append("/").append(fileName).toString();
String implicitLocale = String.format("%.2f", amount);
String defaultLocale = String.format(Locale.getDefault(), "%.2f", amount);
String nullLocale = String.format(null, "%.2f", amount);
String formatted = "Run %s finished".formatted(runId);
byte[] payload = message.getBytes();
String decoded = new String(payload);
log.info("Run " + runId + " finished");
```

这些写法分别绕过统一字符串格式化、依赖默认 Locale/缺少明确 Locale，或依赖默认字符集；日志应改用参数占位符。
