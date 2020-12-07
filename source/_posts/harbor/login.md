---
title: harbor登录切换 
tags:
  - harbor
categories:
  - 技术文章
date: 2020-11-19 20:03:48+08:00
toc: true
---

harbor 在设置完 ldap 登录之后，ldap 会出现响应超时情况，所以想改回数据库模式，但是在页面上不能再设置为其他认证方式。

<!--more-->

通过阅读 `harbor` 的源码，发现其中的限制为以下代码：

```golang
// AuthModeCanBeModified determines whether auth mode can be
// modified or not. Auth mode can modified when there is only admin
// user in database.
func AuthModeCanBeModified() (bool, error) {
	c, err := GetOrmer().QueryTable(&models.User{}).Count()
	if err != nil {
		return false, err
	}
	// admin and anonymous
	return c == 2, nil
}
```

从中可以看出来，只有在数据库`registry`->`harbor_user` 中的数量为`2`时，才可以修改，所以需要进入`psql`中，将多余的用户删除掉，只保留`anonymous` 和 `admin`。
```sql
DELETE FROM harbor_user where id>2;
```

*执行`DELETE`语句时可能会遇到外键不允许删除，需要把`registry`->`project`中的`owner_id` 设置为`admin`的 `1`*

