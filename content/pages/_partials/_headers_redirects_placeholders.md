---
_build:
  publishResources: false
  render: never
  list: never
inputParameters: type
---

可以使用 `:placeholder_name`定义占位符。冒号 (`:`)后面跟一个字母表示占位符的开始，后面的占位符名称必须由字母数字字符和下划线 (`:[A-Za-z]/w*`)组成。每个已命名的占位符只能被引用一次。占位符匹配除分隔符以外的所有字符，分隔符在作为主机的一部分时是句号 (`.`) 或正斜线 (`/`)，在作为路径的一部分时只能是正斜线 (`/`)。

同样，匹配的值可以通过 `:placeholder_name`在 $1 值中使用。