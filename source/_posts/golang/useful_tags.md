---
title: "tags"
tags:
  - golang
categories:
  - 技术文章
date: 2019-07-05T10:03:48+08:00
toc: true
---

# tags 标签在golang 中有什么作用？

字段标记允许您将元信息附加到可以使用反射获取的字段上。通常，它用于提供有关如何将结构域编码为另一种格式（或从另一种格式存储（或从数据库中检索））的转换信息，但是您可以使用它存储想要存储的任何元信息，这些元信息既可以用于另一种包装或供您自己使用。
<!--more-->

> `key` 通常表示包，`json`这个`key`就表示`encoding/json`这个包

```go
type User struct {
    Name string `json:"name" xml:"name"`
}
```

> 如果在 `tag`中传递多个信息的`value`，通常使用`,`进行分隔，
```go
type User struct {
    Name string `json:"name" xml:"name"`
}
```

> 通常用破折号 `-` 表示将字段从`value`中排除
```go
type User struct {
    Name string `json:"-,"`
}
```

# 使用反射访问自定义标签

可以使用反射`reflect`来访问结构字段的标记值。基本上，我们需要获取`Type`的结构，然后可以使用`Type.Field(i int)`或`Type.FieldByName(name string)`查询字段。这些方法返回的值`StructField`描述/表示一个`struct`字段；并且`StructField.Tag`是`StructTag`描述/表示标记值的类型值。

以前我们谈论过“惯例”。这种约定意味着，如果你遵循它，你可以使用`StructTag.Get(key string)`它解析变量的值，并返回该方法`"value"`的`key`指定。该公约实施/内置到这个`Get()`方法。如果您不遵循约定，`Get()`将无法解析`key:"value"`对并找到您要查找的内容。这也不是问题，但是您需要实现自己的解析逻辑。

还有`StructTag.Lookup()`（在Go 1.7中添加了），它类似于，`Get()`但是将不包含给定键的标签与将空字符串与给定键相关联的标签区分开”。

```go
type User struct {
    Name  string `mytag:"MyName"`
    Email string `mytag:"MyEmail"`
}

u := User{"Bob", "bob@mycompany.com"}
t := reflect.TypeOf(u)

for _, fieldName := range []string{"Name", "Email"} {
    field, found := t.FieldByName(fieldName)
    if !found {
        continue
    }
    fmt.Printf("\nField: User.%s\n", fieldName)
    fmt.Printf("\tWhole tag value : %q\n", field.Tag)
    fmt.Printf("\tValue of 'mytag': %q\n", field.Tag.Get("mytag"))
}
```

> 输出：

```go
Field: User.Name
    Whole tag value : "mytag:\"MyName\""
    Value of 'mytag': "MyName"

Field: User.Email
    Whole tag value : "mytag:\"MyEmail\""
    Value of 'mytag': "MyEmail"
```