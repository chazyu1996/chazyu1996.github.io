---
title: "Salt各模块用法说明"
date: 2019-09-18T11:42:22+08:00
tags:
  - salt
summary: salt 具体方法详解
categories:
  - 技术文章
---
<!--more-->

# ACME 加密

## `acme.cert`

参数

> `name` Common Name of the certificate (DNS name of certificate)

> `aliases=None` subjectAltNames (Additional DNS names on certificate)

> `email=None` e-mail address for interaction with ACME provider

> `webroot=None` True or full path to webroot used for authentication

> `test_cert=False` Request a certificate from the Happy Hacker Fake CA (mutually exclusive with 'server')

> `renew=None` True/'force' to force a renewal, or a window of renewal before expiry in days

> `keysize=None` RSA key bits

> `server=None`  API endpoint to talk to

> `owner='root'` owner of private key

> `group='root'` group of private key

例：

```
salt 'gitlab.example.com' acme.cert dev.example.com "[gitlab.example.com]" test_cert=True renew=14 webroot=/opt/gitlab/embedded/service/gitlab-rails/public
```

## `acme.certs`
参数：

> 无

例：

```
salt 'vhost.example.com' acme.certs
```

## `acme.info`

## `acme.expires`

## `acme.has`

## `acme.renew_by`

## `acme.needs_renewal`

# group 管理主机上的组信息

## `group.add`

## `group.delete`

## `group.info`

## `group.getent`

## `group.chgid`

## `group.adduser`

## `group.deluser`

## `group.members`

# aliases 管理别名文件中的信息

## `aliases.list_aliases`

## `aliases.get_target`

## `aliases.set_target`

## `aliases.has_target`

## `aliases.rm_alias`

# alternatives 替代系统

## `alternatives.display`

## `alternatives.show_link`

## `alternatives.show_current`

## `alternatives.check_exists`

## `alternatives.check_installed`

## `alternatives.install`

## `alternatives.remove`

## `alternatives.auto`

## `alternatives.set`

# apache 

## `apache.version`

## `apache.fullversion`

## `apache.modules`

## `apache.servermods`

## `apache.directives`

## `apache.vhosts`

## `apache.signal`

## `apache.useradd`

## `apache.userdel`

## `apache.server_status`

## `apache.config`

# apcupsd

## `apcupsd.status`

## `apcupsd.status_load`

## `apcupsd.status_charge`

## `apcupsd.status_battery`

# apf

## `apf.running`

## `apf.disable`

## `apf.enable`

## `apf.reload`

## `apf.refresh`

## `apf.allow`

## `apf.deny`

## `apf.remove`

# apt

## `pkg.latest_version`

## `pkg.version`

## `pkg.refresh_db`

## `pkg.install`

## `pkg.autoremove`

## `pkg.remove`

## `pkg.purge`

## `pkg.upgrade`

## `pkg.hold`

## `pkg.unhold`

## `pkg.list_pkgs`

## `pkg.list_upgrades`

## `pkg.upgrade_available`

## `pkg.version_cmp`

## `pkg.list_repos`

## `pkg.get_repo`

## `pkg.del_repo`

## `pkg.del_repo_key`

## `pkg.mod_repo`

## `pkg.file_list`

## `pkg.file_dict`

## `pkg.set_selections`

## `pkg.owner`

## `pkg.info_installed`
