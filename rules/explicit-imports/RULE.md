# 显式 import

## 规范与检查边界

- 禁止通配符 import，普通及静态 import 都适用。
- 每个实际使用的类型或静态成员单独一行显式导入，不因同包类型多而改用 `*`。
- 分组和空行另按 [import 顺序](../import-order/RULE.md)与 [import 空行](../import-spacing/RULE.md)检查。

## 正例

```java
import java.util.List;
import java.util.Map;

import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;
```

## 反例

```java
import java.util.*;

import static org.assertj.core.api.Assertions.*;
```

两处 `*` 都违规；修复时只显式导入文件实际使用的类型或成员。
