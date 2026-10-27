# 120 字符行宽

## 规范与检查边界

- Java 代码每行最多 120 个字符，生产和测试代码均适用。
- package/import 声明及不宜拆分的 URL、字符串字面量可例外；不能因为行内存在字符串，就免除整段可拆分调用或方法声明的行宽检查。
- 超长时仅在必要语法边界断开；具体断点按 [续行格式](../continuation-format/RULE.md)。不为凑行宽把独立语句拼在一起，也不改变求值顺序或字符串内容。
- 同时检查 [过度换行](../avoid-excessive-wrapping/RULE.md)，不能只查超长行。

## 正例

```java
return client.createTask(ownerId, projectId, baseVersion, targetVersion, inputRef, inputDigest,
    requestDigest, workerVersion, remainingTimeoutSeconds);
```

完整调用超过 120 字符时，在必要的实参边界换行，保留参数顺序。

## 反例

```java
return client.createTask(ownerId, projectId, baseVersion, targetVersion, inputRef, inputDigest, requestDigest, workerVersion, remainingTimeoutSeconds);
```

这是可在参数边界拆分的超长调用，不属于不可拆字符串例外。
