# import 顺序

## 规范与检查边界

- 普通 import 分组顺序：`com.example.` → 其他 `com.` → `org.` → 其他第三方包 → `java.`。
- 其他第三方包括 `io.`、`net.`、`lombok.`、`jakarta.`、`javax.` 等；按包前缀归组，`com.example.` 不重复归入其他 `com.`。
- 组内按完整导入名称字典序排列。省略空组，不要求为了展示顺序添加无用 import。
- static import 独立放在所有普通 import 之后；其内部也按相同分组顺序及字典序排列。
- 空行按 [import 空行](../import-spacing/RULE.md)，通配符按 [显式 import](../explicit-imports/RULE.md)检查。

## 正例

```java
import com.example.platform.UserClient;

import com.fasterxml.jackson.databind.ObjectMapper;

import org.springframework.stereotype.Service;

import jakarta.validation.Valid;
import lombok.RequiredArgsConstructor;

import java.util.List;
import java.util.Map;

import static org.assertj.core.api.Assertions.assertThat;

import static java.util.Collections.emptyList;
```

`jakarta` 和 `lombok` 属于同一第三方组，组内按完整名称排序；静态 import 在全部普通 import 之后。

## 反例

```java
import java.util.Map;
import java.util.List;

import org.springframework.stereotype.Service;

import com.example.platform.UserClient;
```

普通组的顺序颠倒，且 java 组内 Map 在 List 之前，不符合字典序。
