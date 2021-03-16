---
title: 代码样式格式设置规则
description: 了解用于设置缩进、空格和换行格式的代码样式规则。
ms.date: 09/25/2020
ms.topic: reference
author: gewarren
ms.author: gewarren
dev_langs:
- CSharp
- VB
f1_keywords:
- IDE0055
- formatting rules
helpviewer_keywords:
- IDE0055
- formatting code style rules [EditorConfig]
- formatting rules
- EditorConfig formatting conventions
ms.openlocfilehash: 866949692341f65a5b78c7dd5b8eec918873d3b7
ms.sourcegitcommit: 1d3af230ec30d8d061be7a887f6ba38a530c4ece
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/09/2021
ms.locfileid: "102511796"
---
# <a name="formatting-rules"></a>格式设置规则

格式设置规则会影响 .NET 编程语言构造的缩进、空格和换行的排列方式。 规则分为以下几类：

- [.NET 格式设置规则](#net-formatting-rules)：适用于 C# 和 Visual Basic 的规则。 这些规则的 EditorConfig 选项名称以 `dotnet_` 前缀开头。
- [C# 格式设置规则](#c-formatting-rules)：仅适用于 C# 语言的规则。 这些规则的 EditorConfig 选项名称以 `csharp_` 前缀开头。

## <a name="rule-id-ide0055-fix-formatting"></a>规则 ID：“IDE0055”（修复格式设置）

所有格式设置选项都具有规则 ID `IDE0055` 和标题 `Fix formatting`。 使用以下配置行在 EditorConfig 文件中设置格式设置冲突的严重性。

```ini
dotnet_diagnostic.IDE0055.severity = <severity value>
```

严重性值必须是 `warning` 或 `error` 才能[在生成时强制执行](../overview.md#code-style-analysis)。 要了解所有可能的严重性值，请参阅[严重性级别](../configuration-options.md#severity-level)。

## <a name="option-format"></a>选项格式

可以在 EditorConfig 文件中指定格式设置规则的选项，格式如下：

`rule_name = value`

对于许多规则，可为 `value` 指定 `true`（以此样式为首选项）或 `false`（不以此样式为首选项）。 对于其他规则，可指定值（如 `flush_left` 或 `before_and_after`）来说明在什么时间以及在什么位置应用此规则。 不需要指定严重性。

## <a name="net-formatting-rules"></a>.NET 格式设置规则

本节中的格式设置规则适用于 C# 和 Visual Basic。

- [组织 using](#organize-using-directives)
  - dotnet_sort_system_directives_first
  - dotnet_separate_import_directive_groups

### <a name="organize-using-directives"></a>组织 using 指令

这些格式设置规则与 `using` 指令和 `Imports` 语句的排序和显示有关。

.editorconfig 文件示例：

```ini
# .NET formatting rules
[*.{cs,vb}]
dotnet_sort_system_directives_first = true
dotnet_separate_import_directive_groups = true
```

#### <a name="dotnet_sort_system_directives_first"></a>dotnet\_sort\_system\_directives_first

|Property|值|
|-|-|
| **选项名称** | dotnet_sort_system_directives_first |
| **适用的语言** | C# 和 Visual Basic |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 按字母顺序对 `System.*` `using` 指令排序，并将它们放在其他 using 指令前面。<br /><br />`false` - 不要将 `System.*` `using` 指令放在其他 `using` 指令前面。 |

代码示例：

```csharp
// dotnet_sort_system_directives_first = true
using System.Collections.Generic;
using System.Threading.Tasks;
using Octokit;

// dotnet_sort_system_directives_first = false
using System.Collections.Generic;
using Octokit;
using System.Threading.Tasks;
```

#### <a name="dotnet_separate_import_directive_groups"></a>dotnet\_separate\_import\_directive\_groups

|Property|值|
|-|-|
| **选项名称** | dotnet_separate_import_directive_groups |
| **适用的语言** | C# 和 Visual Basic |
| **引入的版本** | Visual Studio 2017 版本 15.5 |
| **选项值** | `true` - 在 `using` 指令组之间放置一个空白行。<br /><br />`false` - 不在 `using` 指令组之间放置空白行。 |

代码示例：

```csharp
// dotnet_separate_import_directive_groups = true
using System.Collections.Generic;
using System.Threading.Tasks;

using Octokit;

// dotnet_separate_import_directive_groups = false
using System.Collections.Generic;
using System.Threading.Tasks;
using Octokit;
```

## <a name="c-formatting-rules"></a>C# 格式设置规则

本节中的格式设置规则仅适用于 C# 代码。

- [换行符选项](#new-line-options)
  - csharp_new_line_before_open_brace
  - csharp_new_line_before_else
  - csharp_new_line_before_catch
  - csharp_new_line_before_finally
  - csharp_new_line_before_members_in_object_initializers
  - csharp_new_line_before_members_in_anonymous_types
  - csharp_new_line_between_query_expression_clauses
- [缩进选项](#indentation-options)
  - csharp_indent_case_contents
  - csharp_indent_switch_labels
  - csharp_indent_labels
  - csharp_indent_block_contents
  - csharp_indent_braces
  - csharp_indent_case_contents_when_block
- [间距选项](#spacing-options)
  - csharp_space_after_cast
  - csharp_space_after_keywords_in_control_flow_statements
  - csharp_space_between_parentheses
  - csharp_space_before_colon_in_inheritance_clause
  - csharp_space_after_colon_in_inheritance_clause
  - csharp_space_around_binary_operators
  - csharp_space_between_method_declaration_parameter_list_parentheses
  - csharp_space_between_method_declaration_empty_parameter_list_parentheses
  - csharp_space_between_method_declaration_name_and_open_parenthesis
  - csharp_space_between_method_call_parameter_list_parentheses
  - csharp_space_between_method_call_empty_parameter_list_parentheses
  - csharp_space_between_method_call_name_and_opening_parenthesis
  - csharp_space_after_comma
  - csharp_space_before_comma
  - csharp_space_after_dot
  - csharp_space_before_dot
  - csharp_space_after_semicolon_in_for_statement
  - csharp_space_before_semicolon_in_for_statement
  - csharp_space_around_declaration_statements
  - csharp_space_before_open_square_brackets
  - csharp_space_between_empty_square_brackets
  - csharp_space_between_square_brackets
- [换行选项](#wrap-options)
  - csharp_preserve_single_line_statements
  - csharp_preserve_single_line_blocks
- [sing 指令选项](#using-directive-options)
  - csharp_using_directive_placement

### <a name="new-line-options"></a>换行选项

这些格式设置规则与是否使用新行设置代码的格式有关。

.editorconfig 文件示例：

```ini
# CSharp formatting rules:
[*.cs]
csharp_new_line_before_open_brace = methods, properties, control_blocks, types
csharp_new_line_before_else = true
csharp_new_line_before_catch = true
csharp_new_line_before_finally = true
csharp_new_line_before_members_in_object_initializers = true
csharp_new_line_before_members_in_anonymous_types = true
csharp_new_line_between_query_expression_clauses = true
```

#### <a name="csharp_new_line_before_open_brace"></a>csharp\_new\_line\_before\_open_brace

此规则与左大括号 `{` 应放在前面代码的同一行还是新行上有关。 对于此规则，指定“全部”、“无”或一个或多个码位元素，如方法或属性，从而定义此规则的应用时间   。 若要指定多个代码元素，请使用逗号 (,) 分隔。

|Property|值|
|-|-|
| **选项名称** | csharp_new_line_before_open_brace |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `all` - 对于所有表达式，需要将大括号置于新行（“Allman”样式）。<br /><br />`none` - 对于所有表达式，需要将大括号置于同一行（“K&R”）。<br /><br />`accessors`、`anonymous_methods`、`anonymous_types`、`control_blocks`、`events`、`indexers`、`lambdas`、`local_functions`、`methods`、`object_collection_array_initializers`、`properties`、`types` - 对于指定的代码元素，需要将大括号置于新行（“Allman”样式）。 |

代码示例：

```csharp
// csharp_new_line_before_open_brace = all
void MyMethod()
{
    if (...)
    {
        ...
    }
}

// csharp_new_line_before_open_brace = none
void MyMethod() {
    if (...) {
        ...
    }
}
```

#### <a name="csharp_new_line_before_else"></a>csharp\_new\_line\_before_else

|Property|值|
|-|-|
| **选项名称** | csharp_new_line_before_else |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 将 `else` 语句置于新行。<br /><br />`false` - 将 `else` 语句置于同一行。 |

代码示例：

```csharp
// csharp_new_line_before_else = true
if (...) {
    ...
}
else {
    ...
}

// csharp_new_line_before_else = false
if (...) {
    ...
} else {
    ...
}
```

#### <a name="csharp_new_line_before_catch"></a>csharp\_new\_line\_before_catch

|Property|值|
|-|-|
| **选项名称** | csharp_new_line_before_catch |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 将 `catch` 语句置于新行。<br /><br />`false` - 将 `catch` 语句置于同一行。 |

代码示例：

```csharp
// csharp_new_line_before_catch = true
try {
    ...
}
catch (Exception e) {
    ...
}

// csharp_new_line_before_catch = false
try {
    ...
} catch (Exception e) {
    ...
}
```

#### <a name="csharp_new_line_before_finally"></a>csharp\_new\_line\_before_finally

|Property|值|
|-|-|
| **选项名称** | csharp_new_line_before_finally |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 需要将 `finally` 语句置于右大括号后的新行。<br /><br />`false` - 需要将 `finally` 语句置于右大括号所在的同一行。 |

代码示例：

```csharp
// csharp_new_line_before_finally = true
try {
    ...
}
catch (Exception e) {
    ...
}
finally {
    ...
}

// csharp_new_line_before_finally = false
try {
    ...
} catch (Exception e) {
    ...
} finally {
    ...
}
```

#### <a name="csharp_new_line_before_members_in_object_initializers"></a>csharp\_new\_line\_before\_members\_in\_object_initializers

|Property|值|
|-|-|
| **选项名称** | csharp_new_line_before_members_in_object_initializers |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 需要将对象初始值设定项的成员置于单独的行<br /><br />`false` - 需要将对象初始值设定项的成员置于同一行 |

代码示例：

```csharp
// csharp_new_line_before_members_in_object_initializers = true
var z = new B()
{
    A = 3,
    B = 4
}

// csharp_new_line_before_members_in_object_initializers = false
var z = new B()
{
    A = 3, B = 4
}
```

#### <a name="csharp_new_line_before_members_in_anonymous_types"></a>csharp\_new\_line\_before\_members\_in\_anonymous_types

|Property|值|
|-|-|
| **选项名称** | csharp_new_line_before_members_in_anonymous_types |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 需要将匿名类型的成员置于单独的行<br /><br />`false` - 需要将匿名类型的成员置于同一行 |

代码示例：

```csharp
// csharp_new_line_before_members_in_anonymous_types = true
var z = new
{
    A = 3,
    B = 4
}

// csharp_new_line_before_members_in_anonymous_types = false
var z = new
{
    A = 3, B = 4
}
```

#### <a name="csharp_new_line_between_query_expression_clauses"></a>csharp_new_line_between_query_expression_clauses

|Property|值|
|-|-|
| **选项名称** | csharp_new_line_between_query_expression_clauses |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 要求将查询表达式子句的元素置于单独的行<br /><br />`false` - 要求将查询表达式子句的元素置于同一行 |

代码示例：

```csharp
// csharp_new_line_between_query_expression_clauses = true
var q = from a in e
        from b in e
        select a * b;

// csharp_new_line_between_query_expression_clauses = false
var q = from a in e from b in e
        select a * b;
```

### <a name="indentation-options"></a>缩进选项

这些格式设置规则与是否使用缩进设置代码的格式有关。

.editorconfig 文件示例：

```ini
# CSharp formatting rules:
[*.cs]
csharp_indent_case_contents = true
csharp_indent_switch_labels = true
csharp_indent_labels = flush_left
csharp_indent_block_contents = true
csharp_indent_braces = false
csharp_indent_case_contents_when_block = true
```

#### <a name="csharp_indent_case_contents"></a>csharp\_indent\_case_contents

|Property|值|
|-|-|
| **选项名称** | csharp_indent_case_contents |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 缩进 `switch` case 内容<br /><br />`false` - 不缩进 `switch` case 内容 |

- 如果此规则设置为“true”，则为 i。
- 如果此规则设置为“false”，则为 d。

代码示例：

```csharp
// csharp_indent_case_contents = true
switch(c) {
    case Color.Red:
        Console.WriteLine("The color is red");
        break;
    case Color.Blue:
        Console.WriteLine("The color is blue");
        break;
    default:
        Console.WriteLine("The color is unknown.");
        break;
}

// csharp_indent_case_contents = false
switch(c) {
    case Color.Red:
    Console.WriteLine("The color is red");
    break;
    case Color.Blue:
    Console.WriteLine("The color is blue");
    break;
    default:
    Console.WriteLine("The color is unknown.");
    break;
}
```

#### <a name="csharp_indent_switch_labels"></a>csharp\_indent\_switch_labels

|Property|值|
|-|-|
| **选项名称** | csharp_indent_switch_labels |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 缩进 `switch` 标签<br /><br />`false` - 不缩进 `switch` 标签 |

代码示例：

```csharp
// csharp_indent_switch_labels = true
switch(c) {
    case Color.Red:
        Console.WriteLine("The color is red");
        break;
    case Color.Blue:
        Console.WriteLine("The color is blue");
        break;
    default:
        Console.WriteLine("The color is unknown.");
        break;
}

// csharp_indent_switch_labels = false
switch(c) {
case Color.Red:
    Console.WriteLine("The color is red");
    break;
case Color.Blue:
    Console.WriteLine("The color is blue");
    break;
default:
    Console.WriteLine("The color is unknown.");
    break;
}
```

#### <a name="csharp_indent_labels"></a>csharp\_indent_labels

|Property|值|
|-|-|
| **选项名称** | csharp_indent_labels |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `flush_left` - 标签置于最左侧的列<br /><br />`one_less_than_current` - 将标签置于比当前上下文少一个缩进的位置<br /><br />`no_change` - 将标签置于与当前上下文相同的缩进位置 |

代码示例：

```csharp
// csharp_indent_labels= flush_left
class C
{
    private string MyMethod(...)
    {
        if (...) {
            goto error;
        }
error:
        throw new Exception(...);
    }
}

// csharp_indent_labels = one_less_than_current
class C
{
    private string MyMethod(...)
    {
        if (...) {
            goto error;
        }
    error:
        throw new Exception(...);
    }
}

// csharp_indent_labels= no_change
class C
{
    private string MyMethod(...)
    {
        if (...) {
            goto error;
        }
        error:
        throw new Exception(...);
    }
}
```

#### <a name="csharp_indent_block_contents"></a>csharp_indent_block_contents

|Property|值|
|-|-|
| **选项名称** | csharp_indent_block_contents |
| **适用的语言** | C# |
| **选项值** | `true` - <br /><br />`false` -  |

代码示例：

```csharp
// csharp_indent_block_contents = true
static void Hello()
{
    Console.WriteLine("Hello");
}

// csharp_indent_block_contents = false
static void Hello()
{
Console.WriteLine("Hello");
}
```

#### <a name="csharp_indent_braces"></a>csharp_indent_braces

|Property|值|
|-|-|
| **选项名称** | csharp_indent_braces |
| **适用的语言** | C# |
| **选项值** | `true` - <br /><br />`false` -  |

代码示例：

```csharp
// csharp_indent_braces = true
static void Hello()
    {
    Console.WriteLine("Hello");
    }

// csharp_indent_braces = false
static void Hello()
{
    Console.WriteLine("Hello");
}
```

#### <a name="csharp_indent_case_contents_when_block"></a>csharp_indent_case_contents_when_block

|Property|值|
|-|-|
| **选项名称** | csharp_indent_case_contents_when_block |
| **适用的语言** | C# |
| **选项值** | `true` - <br /><br />`false` -  |

代码示例：

```csharp
// csharp_indent_case_contents_when_block = true
case 0:
    {
        Console.WriteLine("Hello");
        break;
    }

// csharp_indent_case_contents_when_block = false
case 0:
{
    Console.WriteLine("Hello");
    break;
}
```

### <a name="spacing-options"></a>间距选项

这些格式设置规则与是否使用空格字符设置代码的格式有关。

.editorconfig 文件示例：

```ini
# CSharp formatting rules:
[*.cs]
csharp_space_after_cast = true
csharp_space_after_keywords_in_control_flow_statements = true
csharp_space_between_parentheses = control_flow_statements, type_casts
csharp_space_before_colon_in_inheritance_clause = true
csharp_space_after_colon_in_inheritance_clause = true
csharp_space_around_binary_operators = before_and_after
csharp_space_between_method_declaration_parameter_list_parentheses = true
csharp_space_between_method_declaration_empty_parameter_list_parentheses = false
csharp_space_between_method_declaration_name_and_open_parenthesis = false
csharp_space_between_method_call_parameter_list_parentheses = true
csharp_space_between_method_call_empty_parameter_list_parentheses = false
csharp_space_between_method_call_name_and_opening_parenthesis = false
csharp_space_after_comma = true
csharp_space_before_comma = false
csharp_space_after_dot = false
csharp_space_before_dot = false
csharp_space_after_semicolon_in_for_statement = true
csharp_space_before_semicolon_in_for_statement = false
csharp_space_around_declaration_statements = false
csharp_space_before_open_square_brackets = false
csharp_space_between_empty_square_brackets = false
csharp_space_between_square_brackets = false
```

#### <a name="csharp_space_after_cast"></a>csharp\_space\_after_cast

|Property|值|
|-|-|
| **选项名称** | csharp_space_after_cast |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 在强制转换和值之间放置空格字符<br /><br />`false` - 删除转换和值之间的空格 |

代码示例：

```csharp
// csharp_space_after_cast = true
int y = (int) x;

// csharp_space_after_cast = false
int y = (int)x;
```

#### <a name="csharp_space_after_keywords_in_control_flow_statements"></a>csharp_space_after_keywords_in_control_flow_statements

|Property|值|
|-|-|
| **选项名称** | csharp_space_after_keywords_in_control_flow_statements |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 在控制流语句（如 `for` 循环）中的关键字后放置空格字符<br /><br />`false` - 删除控制流语句（如 `for` 循环）中的关键字后的空格 |

代码示例：

```csharp
// csharp_space_after_keywords_in_control_flow_statements = true
for (int i;i<x;i++) { ... }

// csharp_space_after_keywords_in_control_flow_statements = false
for(int i;i<x;i++) { ... }
```

#### <a name="csharp_space_between_parentheses"></a>csharp_space_between_parentheses

|Property|值|
|-|-|
| **选项名称** | csharp_space_between_parentheses |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `control_flow_statements` - 在控制流语句的括号之间放置空格<br /><br />`expressions` - 在表达式的括号之间放置空格<br /><br />`type_casts` - 在类型转换中的括号之间放置空格 |

如果省略此规则或使用 `control_flow_statements`、`expressions` 或 `type_casts` 以外的值，则不会应用该设置。

代码示例：

```csharp
// csharp_space_between_parentheses = control_flow_statements
for ( int i = 0; i < 10; i++ ) { }

// csharp_space_between_parentheses = expressions
var z = ( x * y ) - ( ( y - x ) * 3 );

// csharp_space_between_parentheses = type_casts
int y = ( int )x;
```

#### <a name="csharp_space_before_colon_in_inheritance_clause"></a>csharp\_space\_before\_colon\_in\_inheritance_clause

|Property|值|
|-|-|
| **选项名称** | csharp_space_before_colon_in_inheritance_clause |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 15.7 版 |
| **选项值** | `true` - 在类型声明中的基或接口冒号前放置空格字符<br /><br />`false` - 删除类型声明中基或接口冒号前的空格 |

代码示例：

```csharp
// csharp_space_before_colon_in_inheritance_clause = true
interface I
{

}

class C : I
{

}

// csharp_space_before_colon_in_inheritance_clause = false
interface I
{

}

class C: I
{

}
```

#### <a name="csharp_space_after_colon_in_inheritance_clause"></a>csharp\_space\_after\_colon\_in\_inheritance_clause

|Property|值|
|-|-|
| **选项名称** | csharp_space_after_colon_in_inheritance_clause |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 15.7 版 |
| **选项值** | `true` - 在类型声明中的基或接口冒号后放置空格字符<br /><br />`false` - 删除类型声明中基或接口冒号后的空格 |

代码示例：

```csharp
// csharp_space_after_colon_in_inheritance_clause = true
interface I
{

}

class C : I
{

}

// csharp_space_after_colon_in_inheritance_clause = false
interface I
{

}

class C :I
{

}
```

#### <a name="csharp_space_around_binary_operators"></a>csharp\_space\_around\_binary_operators

|Property|值|
|-|-|
| **选项名称** | csharp_space_around_binary_operators |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 15.7 版 |
| **选项值** | `before_and_after` - 在二元运算符前后插入空格<br /><br />`none` - 删除二元运算符前后的空格<br /><br />`ignore` - 忽略二元运算符前后的空格 |

如果省略此规则或使用 `before_and_after`、`none` 或 `ignore` 以外的值，则不会应用该设置。

代码示例：

```csharp
// csharp_space_around_binary_operators = before_and_after
return x * (x - y);

// csharp_space_around_binary_operators = none
return x*(x-y);

// csharp_space_around_binary_operators = ignore
return x  *  (x-y);
```

#### <a name="csharp_space_between_method_declaration_parameter_list_parentheses"></a>csharp_space_between_method_declaration_parameter_list_parentheses

|Property|值|
|-|-|
| **选项名称** | csharp_space_between_method_declaration_parameter_list_parentheses |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 在方法声明参数列表的左括号之后和右括号之前放置空格字符<br /><br />`false` - 删除方法声明参数列表的左括号之后和右括号之前的空格字符 |

代码示例：

```csharp
// csharp_space_between_method_declaration_parameter_list_parentheses = true
void Bark( int x ) { ... }

// csharp_space_between_method_declaration_parameter_list_parentheses = false
void Bark(int x) { ... }
```

#### <a name="csharp_space_between_method_declaration_empty_parameter_list_parentheses"></a>csharp_space_between_method_declaration_empty_parameter_list_parentheses

|Property|值|
|-|-|
| **选项名称** | csharp_space_between_method_declaration_empty_parameter_list_parentheses |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 15.7 版 |
| **选项值** | `true` - 在方法声明的空参数列表括号内插入空格<br /><br />`false` - 删除方法声明的空参数列表括号内的空格 |

代码示例：

```csharp
// csharp_space_between_method_declaration_empty_parameter_list_parentheses = true
void Goo( )
{
    Goo(1);
}

void Goo(int x)
{
    Goo();
}

// csharp_space_between_method_declaration_empty_parameter_list_parentheses = false
void Goo()
{
    Goo(1);
}

void Goo(int x)
{
    Goo();
}
```

#### <a name="csharp_space_between_method_declaration_name_and_open_parenthesis"></a>csharp_space_between_method_declaration_name_and_open_parenthesis

|Property|值|
|-|-|
| **选项名称** | csharp_space_between_method_declaration_name_and_open_parenthesis |
| **适用的语言** | C# |
| **选项值** | `true` - 在方法声明中方法名称和左括号之间放置空格字符<br /><br />`false` - 删除方法声明中方法名称和左括号之间的空格字符 |

代码示例：

```csharp
// csharp_space_between_method_declaration_name_and_open_parenthesis = true
void M () { }

// csharp_space_between_method_declaration_name_and_open_parenthesis = false
void M() { }
```

#### <a name="csharp_space_between_method_call_parameter_list_parentheses"></a>csharp_space_between_method_call_parameter_list_parentheses

|Property|值|
|-|-|
| **选项名称** | csharp_space_between_method_call_parameter_list_parentheses |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 在方法调用的左括号之后和右括号之前放置空格字符<br /><br />`false` - 删除方法调用的左括号之后和右括号之前的空格字符 |

代码示例：

```csharp
// csharp_space_between_method_call_parameter_list_parentheses = true
MyMethod( argument );

// csharp_space_between_method_call_parameter_list_parentheses = false
MyMethod(argument);
```

#### <a name="csharp_space_between_method_call_empty_parameter_list_parentheses"></a>csharp_space_between_method_call_empty_parameter_list_parentheses

|Property|值|
|-|-|
| **选项名称** | csharp_space_between_method_call_empty_parameter_list_parentheses |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 15.7 版 |
| **选项值** | `true` - 在空参数列表的括号中插入空格<br /><br />`false` - 删除空参数列表括号内的空格 |

代码示例：

```csharp
// csharp_space_between_method_call_empty_parameter_list_parentheses = true
void Goo()
{
    Goo(1);
}

void Goo(int x)
{
    Goo( );
}

// csharp_space_between_method_call_empty_parameter_list_parentheses = false
void Goo()
{
    Goo(1);
}

void Goo(int x)
{
    Goo();
}
```

#### <a name="csharp_space_between_method_call_name_and_opening_parenthesis"></a>csharp_space_between_method_call_name_and_opening_parenthesis

|Property|值|
|-|-|
| **选项名称** | csharp_space_between_method_call_name_and_opening_parenthesis |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 15.7 版 |
| **选项值** | `true` - 在方法调用名称和左括号之间插入空格<br /><br />`false` - 删除方法调用名称和左括号之间的空格 |

代码示例：

```csharp
// csharp_space_between_method_call_name_and_opening_parenthesis = true
void Goo()
{
    Goo (1);
}

void Goo(int x)
{
    Goo ();
}

// csharp_space_between_method_call_name_and_opening_parenthesis = false
void Goo()
{
    Goo(1);
}

void Goo(int x)
{
    Goo();
}
```

#### <a name="csharp_space_after_comma"></a>csharp_space_after_comma

|Property|值|
|-|-|
| **选项名称** | csharp_space_after_comma |
| **适用的语言** | C# |
| **选项值** | `true` - 在逗号后面插入空格<br /><br />`false` - 删除逗号后面的空格 |

代码示例：

```csharp
// csharp_space_after_comma = true
int[] x = new int[] { 1, 2, 3, 4, 5 };

// csharp_space_after_comma = false
int[] x = new int[] { 1,2,3,4,5 }
```

#### <a name="csharp_space_before_comma"></a>csharp_space_before_comma

|Property|值|
|-|-|
| **选项名称** | csharp_space_before_comma |
| **适用的语言** | C# |
| **选项值** | `true` - 在逗号前插入空格<br /><br />`false` - 删除逗号前的空格 |

代码示例：

```csharp
// csharp_space_before_comma = true
int[] x = new int[] { 1 , 2 , 3 , 4 , 5 };

// csharp_space_before_comma = false
int[] x = new int[] { 1, 2, 3, 4, 5 };
```

#### <a name="csharp_space_after_dot"></a>csharp_space_after_dot

|Property|值|
|-|-|
| **选项名称** | csharp_space_after_dot |
| **适用的语言** | C# |
| **选项值** | `true` - 在点号后面插入空格<br /><br />`false` - 删除点号后面的空格 |

代码示例：

```csharp
// csharp_space_after_dot = true
this. Goo();

// csharp_space_after_dot = false
this.Goo();
```

#### <a name="csharp_space_before_dot"></a>csharp_space_before_dot

|Property|值|
|-|-|
| **选项名称** | csharp_space_before_dot |
| **适用的语言** | C# |
| **选项值** | `true` - 在点前插入空格 <br /><br />`false` - 删除点前的空格 |

代码示例：

```csharp
// csharp_space_before_dot = true
this .Goo();

// csharp_space_before_dot = false
this.Goo();
```

#### <a name="csharp_space_after_semicolon_in_for_statement"></a>csharp_space_after_semicolon_in_for_statement

|Property|值|
|-|-|
| **选项名称** | csharp_space_after_semicolon_in_for_statement |
| **适用的语言** | C# |
| **选项值** | `true` - 在 `for` 语句中的每个分号后面插入空格<br /><br />`false` - 删除 `for` 语句中每个分号后的空格 |

代码示例：

```csharp
// csharp_space_after_semicolon_in_for_statement = true
for (int i = 0; i < x.Length; i++)

// csharp_space_after_semicolon_in_for_statement = false
for (int i = 0;i < x.Length;i++)
```

#### <a name="csharp_space_before_semicolon_in_for_statement"></a>csharp_space_before_semicolon_in_for_statement

|Property|值|
|-|-|
| **选项名称** | csharp_space_before_semicolon_in_for_statement |
| **适用的语言** | C# |
| **选项值** | `true` - 在 `for` 语句中的每个分号前插入空格 <br /><br />`false` - 删除 `for` 语句中每个分号前的空格 |

代码示例：

```csharp
// csharp_space_before_semicolon_in_for_statement = true
for (int i = 0 ; i < x.Length ; i++)

// csharp_space_before_semicolon_in_for_statement = false
for (int i = 0; i < x.Length; i++)
```

#### <a name="csharp_space_around_declaration_statements"></a>csharp_space_around_declaration_statements

|Property|值|
|-|-|
| **选项名称** | csharp_space_around_declaration_statements |
| **适用的语言** | C# |
| **选项值** | `ignore` - 不删除声明语句中多余的空格字符<br /><br />`false` - 删除声明语句中多余的空格字符 |

代码示例：

```csharp
// csharp_space_around_declaration_statements = ignore
int    x    =    0   ;

// csharp_space_around_declaration_statements = false
int x = 0;
```

#### <a name="csharp_space_before_open_square_brackets"></a>csharp_space_before_open_square_brackets

|Property|值|
|-|-|
| **选项名称** | csharp_space_before_open_square_brackets |
| **适用的语言** | C# |
| **选项值** | `true` - 在左方括号 `[` 前插入空格 <br /><br />`false` - 删除左方括号 `[` 前的空格 |

代码示例：

```csharp
// csharp_space_before_open_square_brackets = true
int [] numbers = new int [] { 1, 2, 3, 4, 5 };

// csharp_space_before_open_square_brackets = false
int[] numbers = new int[] { 1, 2, 3, 4, 5 };
```

#### <a name="csharp_space_between_empty_square_brackets"></a>csharp_space_between_empty_square_brackets

|Property|值|
|-|-|
| **选项名称** | csharp_space_between_empty_square_brackets |
| **适用的语言** | C# |
| **选项值** | `true` - 在空方括号 `[ ]` 之间插入空格 <br /><br />`false` - 删除空方括号 `[]` 之间的空格 |

代码示例：

```csharp
// csharp_space_between_empty_square_brackets = true
int[ ] numbers = new int[ ] { 1, 2, 3, 4, 5 };

// csharp_space_between_empty_square_brackets = false
int[] numbers = new int[] { 1, 2, 3, 4, 5 };
```

#### <a name="csharp_space_between_square_brackets"></a>csharp_space_between_square_brackets

|Property|值|
|-|-|
| **选项名称** | csharp_space_between_square_brackets |
| **适用的语言** | C# |
| **选项值** | `true` - 在非空方括号 `[ 0 ]` 中插入空格字符 <br /><br />`false` - 删除非空方括号 `[0]` 中的空格字符 |

代码示例：

```csharp
// csharp_space_between_square_brackets = true
int index = numbers[ 0 ];

// csharp_space_between_square_brackets = false
int index = numbers[0];
```

### <a name="wrap-options"></a>换行选项

这些格式设置规则与语句和代码块中单一行以及单独的行的使用有关。

.editorconfig 文件示例：

```ini
# CSharp formatting rules:
[*.cs]
csharp_preserve_single_line_statements = true
csharp_preserve_single_line_blocks = true
```

#### <a name="csharp_preserve_single_line_statements"></a>csharp_preserve_single_line_statements

|Property|值|
|-|-|
| **选项名称** | csharp_preserve_single_line_statements |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 将语句和成员声明保留在同一行上<br /><br />`false` - 将语句和成员声明保留在不同行上 |

代码示例：

```csharp
//csharp_preserve_single_line_statements = true
int i = 0; string name = "John";

//csharp_preserve_single_line_statements = false
int i = 0;
string name = "John";
```

#### <a name="csharp_preserve_single_line_blocks"></a>csharp_preserve_single_line_blocks

|Property|值|
|-|-|
| **选项名称** | csharp_preserve_single_line_blocks |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2017 版本 15.3 |
| **选项值** | `true` - 将代码块保留在单个行上<br /><br />`false` - 将代码块保留在单独的行上 |

代码示例：

```csharp
//csharp_preserve_single_line_blocks = true
public int Foo { get; set; }

//csharp_preserve_single_line_blocks = false
public int MyProperty
{
    get; set;
}
```

### <a name="using-directive-options"></a>using 指令选项

此格式设置规则涉及到使用放置在命名空间内和外的 using 指令。

.editorconfig 文件示例：

```ini
# 'using' directive preferences
[*.cs]
csharp_using_directive_placement = outside_namespace
csharp_using_directive_placement = inside_namespace
```

#### <a name="csharp_using_directive_placement"></a>csharp_using_directive_placement

|Property|值|
|-|-|
| **选项名称** | csharp_using_directive_placement |
| **适用的语言** | C# |
| **引入的版本** | Visual Studio 2019 版本 16.1 |
| **选项值** | `outside_namespace` - 将 using 指令保留在命名空间之外<br /><br />`inside_namespace` - 将 using 指令保留在命名空间之内 |

代码示例：

```csharp
// csharp_using_directive_placement = outside_namespace
using System;

namespace Conventions
{

}

// csharp_using_directive_placement = inside_namespace
namespace Conventions
{
    using System;
}
```

## <a name="see-also"></a>请参阅

- [语言规则](language-rules.md)
- [命名规则](naming-rules.md)
- [.NET 代码样式规则参考](index.md)
