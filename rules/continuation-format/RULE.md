# 续行格式

## 规范与检查边界

- 确认完整写法超过 [120 字符](../line-length/RULE.md)后，仅在必要的少量语法边界换行，避免每个参数或每层调用各占一行。
- 第二行相对第一行增加 4 个空格，后续续行与第二行对齐，不随嵌套层级继续加深。
- 运算符、链式调用的点号与其后内容一起移到下一行；多个参数在逗号后换行，不在左括号前换行。
- 复杂嵌套优先提取有意义的局部变量；不能为排版改变求值顺序、空值或异常语义。
- 断点参考原有[阿里巴巴 Formatting Style 第 6 条](https://github.com/alibaba/Alibaba-Java-Coding-Guidelines#formatting-style)；尽量使用当前行宽按本项目偏好执行。

## 正例

```java
    return client.createTask(ownerId, projectId, baseVersion, targetVersion, inputRef, inputDigest,
        requestDigest, workerVersion, remainingTimeoutSeconds);
```

第一行 4 空格，续行 8 空格；参数在逗号后断开。

```java
    return repository.findByOwnerAndRepositoryAndStatus(ownerId, repositoryId, status, includeArchived).stream()
        .map(converter::toDomain).filter(this::isVisible).toList();
```

点号跟随下一行的调用，续行不会逐层加深。

## 反例

```java
    return client.createTask(ownerId, projectId, baseVersion, targetVersion, inputRef, inputDigest,
                requestDigest,
                        workerVersion, remainingTimeoutSeconds);
```

续行缩进不对齐，且可以容纳的实参被提前拆开。

```java
    return repository.findByOwnerAndRepositoryAndStatus(ownerId, repositoryId, status, includeArchived).stream().
        map(converter::toDomain).filter(this::isVisible).toList();
```

上一行末尾的点号应移到下一行，与 `map` 一起写。
