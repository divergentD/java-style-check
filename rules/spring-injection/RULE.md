# Spring 注入

## 规范与检查边界

- 固定配置值与环境变量的读取方式另见[配置与环境变量注入](../configuration-injection/RULE.md)，与本规则的 Bean 依赖注入区分检查。
- Spring 组件尽量减少仅用于依赖赋值的手写构造函数，使用 `@Autowired` 注入或项目已有 Lombok 的 `@RequiredArgsConstructor`。
- 注解名称是 `@RequiredArgsConstructor`。不为风格检查擅自添加 Lombok 依赖。
- 纯赋值构造提出简化建议；涉及显式初始化、校验、特定 Bean 选择、测试专用重载或特殊注入逻辑时，保留必要构造，不能仅凭存在构造函数判为违规。
- 修复须保留单/多构造选择、final 字段、限定符及初始化语义，执行项目适用的编译与相关测试。

## 正例

项目已有 Lombok，依赖无需额外初始化：

```java
/** 提供用户查询服务。 */
@Service
@RequiredArgsConstructor
public class UserService {
    private final UserDao userDao;
}
```

必要的初始化和配置校验仍可使用构造函数：

```java
/** 读取受控目录内的清单。 */
@Component
public class ManifestClient {
    private final Path root;

    /** 校验并固定清单根目录。 */
    public ManifestClient(Settings settings) {
        settings.validate();
        this.root = Path.of(settings.root()).toAbsolutePath().normalize();
    }
}
```

## 反例

项目已有 Lombok，下面的构造只重复依赖赋值，可简化：

```java
/** 提供用户查询服务。 */
@Service
public class UserService {
    private final UserDao userDao;

    /** 注入用户持久化接口。 */
    public UserService(UserDao userDao) {
        this.userDao = userDao;
    }
}
```

这是简化建议，不能以此为由删除包含必要初始化的构造，或改变应用实际选择的注入路径。
