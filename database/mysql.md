---
theme: "black"
transition: "zoom"
highlightTheme: "darkula"
---

## MySQL显示宽度

> 对于tinyint（M） 或者 int（M），M为其显示宽度。

"高性能MySQL" 书中在"4.1 选择优化的数据类型"中提到:

> MySQL 可以为整数类型指定宽度, 例如 INT(11), 对大多数应用这是没有意义的: 它不会限制值的合法范围, 只是规定了 MySQL 的一些交互工具(例如 MySQL 命令行客户端)用来显示字符的个数. 对于存储和计算来说, INT(1) 和 INT(20) 是相同的