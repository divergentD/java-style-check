# import 空行

## 规范与检查边界

- 普通 import 组间用一个空行隔开，组内不插空行；省略空组。
- static import 单独放在普通 import 后，与普通 import 用一个空行隔开；静态组内部同样组间一个空行、组内无空行。
- 分组、字典序按 [import 顺序](../import-order/RULE.md)。不能只凭是否相邻判断两个 import 属于同组。

## 正例

```java
import org.springframework.stereotype.Service;

import java.util.List;
import java.util.Map;

import static org.assertj.core.api.Assertions.assertThat;
import static org.assertj.core.api.Assertions.assertThatThrownBy;
```

org 和 java 组之间有一个空行；组内无空行；static 与普通 import 分开。

## 反例

```java
import org.springframework.stereotype.Service;
import java.util.List;

import java.util.Map;
import static org.assertj.core.api.Assertions.assertThat;
```

org/java 组间缺空行、java 组内多空行、static 与普通 import 之间缺空行，均应修复。
