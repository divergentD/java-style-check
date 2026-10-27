# MapStruct 实体转换

## 规范与检查边界

- 实体之间的转换统一使用 MapStruct，覆盖 DO、Domain、DTO、VO、record 的单对象、集合、嵌套、多来源字段映射及已有对象更新。
- MapStruct 映射接口统一放在独立的 `mapstruct` 包，可按访问边界划分子包；使用公共顶层接口供多个调用方复用，不嵌入 Service、Client 或实体类。映射包不依赖业务 Service 实现，校验和规范化在调用方完成。
- 禁止手写逐字段构造、setter/builder 搬运、`BeanUtils.copyProperties` 或另建通用反射转换器。
- 字段重命名、忽略项和类型适配在 MapStruct 映射中显式声明。自定义方法只保留必要的 JSON、时间、枚举适配与业务规则；不能把完整手工对象映射藏入 `default` 方法或 `expression`。
- 业务新建、状态迁移、JSON 编解码及测试数据初始化不等同于对象转换；测试中的实际对象转换同样适用。DTO→DO 的字段搬运即使位于创建流程中，仍属于映射。
- 沿用项目既有分层和转换边界；对于区分持久化与业务层的项目，DO 相关映射可集中在 `mapstruct.persistence`，仅 DAO 调用；`dao/converter` 保留持久化 JSON、时间和枚举适配，业务 Service 不直接依赖这些持久化边界。
- 保留空值、默认值、集合顺序、权限、审计和异常语义。`@MappingTarget` 只处理对象字段更新，不能替代锁、租约、CAS 或事务。
- 核对 MapStruct API、注解处理器、Lombok 兼容和实际映射调用；MyBatis 的 Mapper 不能算 MapStruct。仅声明一个未调用的 MapStruct 接口不能证明已迁移。

## 正例

下面的 DO 映射接口作为公共顶层接口放在独立包，并限制仅 DAO 使用，使用 `org.mapstruct.Mapper` 和 `org.mapstruct.Mapping`：

```java
package com.example.mapstruct.persistence;

/** 将用户持久化记录转换为领域对象。 */
@Mapper(componentModel = "spring")
public interface UserConverter {
    /** 显式映射数据库用户名与领域名称。 */
    @Mapping(target = "name", source = "userName")
    User toDomain(UserDO source);

    /** 保留查询结果的顺序，转换用户列表。 */
    List<User> toDomainList(List<UserDO> sources);
}
```

DAO 实际调用已注入的映射器，而不在 Service 中暴露 DO：

```java
return converter.toDomain(row);
```

## 反例

```java
public User toDomain(UserDO row) {
    return new User(row.getId(), row.getUserName(), row.getCreatedAt());
}
```

即使将此方法放入 `@Mapper` 接口的 `default` 方法，字段仍由手工搬运，不符合规范。

```java
UserDTO target = new UserDTO();
target.setId(source.getId());
target.setName(source.getName());
BeanUtils.copyProperties(source, target);
```

逐字段 setter 与反射复制都应替换为实际使用的 MapStruct 映射；校验和事务留在其原有业务边界。

```java
/** 订单业务服务。 */
public class OrderService {
    /** 嵌在业务实现内的映射声明。 */
    @Mapper
    interface OrderMapper {
        /** 将订单映射为视图。 */
        OrderDTO toDto(Order source);
    }
}
```

即使使用 MapStruct，嵌套映射接口也不符合独立包和跨调用方复用规范；应提取为 `mapstruct` 包下的公共顶层接口，Service 调用该接口。
