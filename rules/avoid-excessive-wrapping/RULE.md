# 避免过度换行

## 规范与检查边界

- 语句、方法形参、调用实参和链式调用的完整写法不超过 120 字符时保持一行，不因参数个数或调用层级提前换行。
- 超长表达式也应合并能清晰容纳的相邻续行，按 [续行格式](../continuation-format/RULE.md)在少量必要边界拆分。
- 相邻行可合并只是候选；阅读全文，确认它们属于同一语句且没有注释、求值顺序或异常语义障碍后才认定。不能把独立语句硬拼到一行。

## 正例

```java
String digest = HexFormat.of().formatHex(MessageDigest.getInstance("SHA-256").digest(bytes));
```

完整语句在 120 字符内，保持一行。

## 反例

```java
String digest =
    HexFormat.of()
        .formatHex(
            MessageDigest.getInstance("SHA-256")
                .digest(bytes));
```

每层调用都拆行且缩进不断加深，没有行宽需要；应恢复为正例中的一行。
