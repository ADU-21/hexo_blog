---
title: ansible学习小记
date: 2016-06-15 20:30:46
categories: Devops
tags: [opration, ansible]

---
# Ansible是什么
借助官网上的一句话，ansible is a simple IT automation, 即ansible 是用于IT自动化管理的一个工具
<!-- more -->
## 诞生背景
在传统小规模开发中，我们在开发机上开发，在Linux服务器上部署，整个过程只需要一个人操作，运维既是在开发机上开发，测试，然后选个凌晨两三点的时间把打包好的字节码文件复制到服务器上，这种开发生产环境用不着自动化配置管理工具。
但当我们的开发升级到数十个人的团队，服务器多达数台，这种操作方式的弊端就会显露出来，一是多人协作带来的开发环境和生产环境不一致导致开发环境可用的代码到了生产环境（服务器）上变得不可用，二是多台服务器的重复配置带来的工作内容的冗余，一定程度上降低了我们的生产效率。这种时候运维的角色开始逐渐显现出来。
这种级别的运维，通常只需要一些python或者bash脚本就可以实现自动化部署，配置服务器等功能。再加上规范的文档，基本可以解决团队之间的沟通问题。
但是随着产品迭代周期的加长，团队的扩大，问题也随之而来，实践中脚本的不易维护，程序员们不愿意更新文档等问题逐渐暴露出来。于是市面上诞生了一批以”代码即文档”为核心思想的自动化配置管理工具，Ansible就是其中之一。

## 操作方法
ansible主要由几个部分租成，其核心是inventory文件和yaml编写的playbook，按照[最佳实践](http://docs.ansible.com/ansible/playbooks_best_practices.html)的标准，一个完整的ansible文件应该具有以下结构：

```
production                # inventory file for production servers
staging                   # inventory file for staging environment

group_vars/
   group1                 # here we assign variables to particular groups
   group2                 # ""
host_vars/
   hostname1              # if systems need specific variables, put them here
   hostname2              # ""

library/                  # if any custom modules, put them here (optional)
filter_plugins/           # if any custom filter plugins, put them here (optional)

site.yml                  # master playbook
webservers.yml            # playbook for webserver tier
dbservers.yml             # playbook for dbserver tier

roles/
    common/               # this hierarchy represents a "role"
        tasks/            #
            main.yml      #  <-- tasks file can include smaller files if warranted
        handlers/         #
            main.yml      #  <-- handlers file
        templates/        #  <-- files for use with the template resource
            ntp.conf.j2   #  <------- templates end in .j2
        files/            #
            bar.txt       #  <-- files for use with the copy resource
            foo.sh        #  <-- script files for use with the script resource
        vars/             #
            main.yml      #  <-- variables associated with this role
        defaults/         #
            main.yml      #  <-- default lower priority variables for this role
        meta/             #
            main.yml      #  <-- role dependencies

    webtier/              # same kind of structure as "common" was above, done for the webtier role
    monitoring/           # ""
    fooapp/               # ""
```
这里我挑几个重点讲：
site.yml是ensile playbook的入口文件，执行该文件会依次找到inventory文件，找到hosts组，然后ssh到目标机器组的host上，开始执行task。
task是通过调用ansible模块的方式，在远程设备上执行命令来实现配置的，所以理论上可以通过命令行操作的操作，ansible都可以执行。
以下为一个task:

```
-name: install npm
 sudo: yes
 yum: 
 	name: npm
 	state: present
```
该task使用了ansible的yum模块，用于检测远端设备上是否部署了nam，如果没有部署，ansible会进行yum install nam 操作进行安装，安装成功，显示ok，失败则抛出异常并中断ansible执行。


## 几款自动化配置工具
我没有使用过其他自动化配置管理工具，在此仅对ansible做进一步介绍
ensile的底层实现使用python，在Linux上支持较好，windows支持较弱，因为生产环境和测试环境通常都是Linux操作系统，所以这点无伤大雅。
你可以理解成Ansible就是对一批python脚本的封装,你只需要更改一些yams文件就可以达到控制服务器配置版本信息的目的，关于yams语法：[http://www.ansible.com.cn/docs/YAMLSyntax.html](http://www.ansible.com.cn/docs/YAMLSyntax.html)



# 如何使用
# Ansible的不足之处

未完待续。。