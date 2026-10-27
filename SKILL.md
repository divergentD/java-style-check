---
name: java-style-check
description: 手动检查 Java 代码风格，包括 Javadoc、Spring 注入、配置与环境变量注入、SLF4J 日志、MapStruct 实体转换、字符串格式化与显式 Locale/UTF-8、圈复杂度、import 与 120 字符换行。用于用户单独要求规范检查，不代替业务逻辑审查。
---

# Java 代码规范检查

仅在用户显式调用时执行。默认只检查并报告，用户要求修复时再修改代码。

## 检查范围与流程

- 优先使用用户指定的文件、目录或 diff。未指定范围时，检查当前仓库工作区变更中的 Java 文件（含暂存、未暂存和未跟踪文件）；没有 Java 变更时，请用户指定检查范围。
- 阅读完整文件确认 import 分组、构造函数用途、日志写法和长行上下文；不要仅凭正则命中认定违规。
- 对生产代码和测试代码按下表选择规则：用户要求整体风格检查或全量扫描时，读取全部规则；明确只检查专项时，读取对应规则及其相关规则链接。开始检查前先阅读所选规则的规范、边界与正反例。
- 输出检查范围及问题列表，每项包含文件路径、行号、违反规则与具体修改建议；没有问题时明确说明。区分本次变更新增问题与检查范围内既有问题，不宣称未检查范围符合规范。
- 若用户要求修复，保留注入语义与业务行为；修改后检查 diff 和格式，涉及注入方式或 Lombok 时执行项目适用的编译及相关测试。

## 规则索引

每条规则独立存放在 `rules/<规则名>/RULE.md`，包含规范与检查边界、正例、反例及原因。示例是聚焦当前规则的代码片段，可省略无关 import 和业务类型定义；实际代码仍须遵守全部适用规则。

| 规则 ID | 规范 | 规则文件 |
| --- | --- | --- |
| javadoc | 中文注释、类型作者与日期、公开方法的参数/返回值/异常 Javadoc | [Javadoc](rules/javadoc/RULE.md) |
| spring-injection | Spring 注入及必要构造边界 | [Spring 注入](rules/spring-injection/RULE.md) |
| configuration-injection | 固定配置使用 @Value 注入，保留类型化配置与必要环境访问 | [配置与环境变量注入](rules/configuration-injection/RULE.md) |
| slf4j-logging | SLF4J 与日志 Factory 约束 | [SLF4J 日志](rules/slf4j-logging/RULE.md) |
| string-format | 字符串拼接使用 String.format，显式 Locale 与 UTF-8 | [字符串格式化与编码](rules/string-format/RULE.md) |
| mapstruct-conversion | 实体映射使用 MapStruct | [MapStruct 实体转换](rules/mapstruct-conversion/RULE.md) |
| cyclomatic-complexity | 函数实现平均圈复杂度 ≤ 5、最大 ≤ 20 | [圈复杂度](rules/cyclomatic-complexity/RULE.md) |
| explicit-imports | 禁止普通及静态通配符 import | [显式 import](rules/explicit-imports/RULE.md) |
| line-length | 120 字符上限及字面量例外 | [120 字符行宽](rules/line-length/RULE.md) |
| continuation-format | 必要断点、续行缩进及对齐 | [续行格式](rules/continuation-format/RULE.md) |
| avoid-excessive-wrapping | 完整语句在行宽内保持一行 | [避免过度换行](rules/avoid-excessive-wrapping/RULE.md) |
| import-order | 普通、静态 import 分组及排序 | [import 顺序](rules/import-order/RULE.md) |
| import-spacing | import 组间与组内空行 | [import 空行](rules/import-spacing/RULE.md) |

报告每项问题时注明规则 ID，方便追溯对应规范和正反例。修改规范时更新对应规则文件；新增规则时同步此索引，不在入口重复展开规则正文。
