## Curator简介和容器化参考

[TOC]

##### 1. Curator简介

Elasticsearch Curator是一个帮助管理Elasticsearch的索引和快照的插件工具

##### 2. Curator安装

Elasticsearch Curator是一款客户端工具，所以只需要在操作机器上安装即可使用。

```bash
rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch

cat >/etc/yum.repos.d/es-curator.repo <<-EOF

[curator-5]

name=CentOS/RHEL 7 repository for Elasticsearch Curator 5.x packages

baseurl=https://packages.elastic.co/curator/5/centos/7

gpgcheck=1

gpgkey=https://packages.elastic.co/GPG-KEY-elasticsearch

enabled=1

EOF

yum install elasticsearch-curator -y  
```

或者通过pip更简单

```bash
pip install -U elasticsearch-curator
```

##### 3. 容器化参考

因为Elasticsearch Curator的主要面对对象是Elasticsearch的索引和快照，且非容器化方案下一般会配合Crontab去实现具体期望，那么在Kubernetes环境下，用CronJob是操作最为合适

```bash
git clone https://github.com/KubeLouis/elasticsearch-curator.git

cd elasticsearch-curator

helm install -f values.yaml curator ./

kubectl get cj
# 可以看到预设好了的cronjob

kubectl get cm
# 对应要执行操作的配置文件和具体要操作的动作都可以在configmap里看到
```

通过编辑elasticsearch-curator/values.yaml中的cronjob和configMaps来具体定义自己的期望

##### 4. values.yaml中常用字段

| 字段                       | 简介                                                         | 示例                                                         |
| -------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| cronjob.schedule           | cronjob执行的时间周期                                        | schedule: "0 1 * * *"                                        |
| image.repository           | cronjob拉起的镜像仓库                                        | repository: untergeek/curator                                |
| image.tag                  | cronjob拉起的镜像tag                                         | tag: 5.8.1                                                   |
| configMaps.action_file_yml | Curator的Action file，用来具体书写自己操作Curator期望        | 参考：https://www.elastic.co/guide/en/elasticsearch/client/curator/current/actionfile.html |
| configMaps.config_yml      | Curator的Configuration file，用来做一些具体操作Action file之前的基础配置信息 | 参考：https://www.elastic.co/guide/en/elasticsearch/client/curator/current/configfile.html |

##### 5. 参考文档

​	Elastic Curator官方文档：https://www.elastic.co/guide/en/elasticsearch/client/curator/current/index.html

​	Kubernetes Cronjob官方文档：https://kubernetes.io/zh/docs/tasks/job/automated-tasks-with-cron-jobs/

​	Helm Hub：https://hub.helm.sh/charts/stable/elasticsearch-curator
