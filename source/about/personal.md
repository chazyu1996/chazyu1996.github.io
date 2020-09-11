# 于子晨

> 年龄：24   邮箱：chazyu@qq.com  电话：17600641613

> 2014-2018  年 统招本科  郑州大学 通信工程专业
## 技术栈
* Python、go、shell、salt、django、kubernetes、operator、prometheus、harbor
## 工作及项目经历
### 2017.11 ~ 至今 好大夫在线  运维开发工程师
###### 自动化运维系统：将公司运维操作自动化

1. ci/cd自动化，一键发布自动编排 
2. 工单审批流程
3. cmdb基于openstack的自动发现
4. 基于openstack 一键开机、部署环境
5. 服务重启
6. 基于bind的版本化DNS自动化配置
7. 数据库自动化
8. svn、vpn、jumpserver等权限全自动化管理
###### jumpserver二次开发

###### saltstack 二次开发：满足高可用
1. salt-api集群化，高可用
2. salt-api 提供了更新接口，使得无需重启salt-api 即可加载配置文件）
3. 使用 **Tornado** 开发salt-api资源调度器

###### 人脸识别打卡接口对接
1. 使用*async协程* + *websocket*  实现人脸识别实时打卡，对接至人事系统
###### k8s容器平台的搭建
1. harbor作为存储仓库
2. 使用kubeasz 进行自动化部署，并针对其进行定制化修改
3. jenkins和gitlab-ci
4. prometheus-operator 作为监控
###### k8s容器化管理平台
1. 通过纵表的形式存储模板，再进行渲染，得到整个yaml，使得模板化更灵活
2. 针对容器节点自动化部署、升级
3. 集成 ci、cd 流程化打包
4. 集成 prometheus operator管理，管理 rule、alertmanager、target
5. 提出前端容器化测试解决方案（采用代理的形式）
6. 支持自我迭代
###### k8s operator 开发
1. 前端容器化测试环境crd ：自动调度自动分配代理、配置ingress、生成代理订阅地址。通过正反代理的模式，控制测试环境的流量进出
### 2017.7 ~ 2017.8 绿盟科技 python开发实习⽣
1. 负责ERP系统相关功能的开发:sprint 敏捷看板、PMO 开发、API 开发等等 
2. 修补存在的 sql 注入漏洞、xss 漏洞、跨站请求漏洞等等

### 2017.3 ~ 2017.4 郑州大学超算中心 学⽣助理
1. 负责集群的日常维护
2. 使用 shell 脚本、pbs 脚本、 Python 等在集群对相关软件进行测试并优化调整

### 获奖情况
* asc17世界⼤学生超算大赛⼀等奖