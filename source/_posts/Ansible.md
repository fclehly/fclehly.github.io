---
title: Ansible
date: 2019-06-01 21:48:11
tags:
- devops
---

# ANSIBLE



------


[TOC]

------



## 场景

为了操作远程主机，我们通常是ssh登录到对应的机子上并输入命令执行，安装软件、修改配置、等等。如果只有单台主机，这么做是完全OK的，顶多是把一些常用操作变成脚本减少重复操作。但是当有了两台主机甚至更多主机之后，同样的命令在另一台主机还要敲一遍，这样的操作就有点枯燥繁琐了。有的人觉得仅仅是多一台主机还好，那如果多10台主机呢，比如需求是在10台机子上配置redis集群，那么ssh登录到每台机子上进行操作是一件很sb的事。

一个简单的解决方案是：使用pssh。pssh可以通过ssh的方式批量发送命令到多台主机并执行，pscp可以批量拷贝文件到多台主机。但是pssh的缺点也很明显，就是功能太少。如果想要通过pssh进行复杂的操作，那就比较困难了。

多主机的配置管理是常见的场景，前人已经造好了轮子，也就是一些自动化运维工具，我们直接使用就行了。比较著名的也是国内用的最多的就是Ansible。

其实本质上Ansible或者说大部分运维工具主要解决以下自动化运维场景：

- 文件传输
- 应用部署
- 配置管理
- 任务流编排



## 常用自动化运维工具

自动化运维工具主要可以分为两大类：

1. 需要装代理的；
2. 不需要装代理的；

第一种方式是指我们需要预先在目标主机上安装代理软件（在被控端上放个内鬼，就可以交易了），然后主控端通过通知代理软件来操纵被控端。

第二种方式是不需要在被控端安装代理软件，主要是通过ssh连接到被控主机进行控制操作，主控端需要提前得到被控主机的授权（比如秘钥对）才能进行相应的操作。



当前主流的自动化运维工具有：

- ansible：使用python编写，agentless，适用于中小型应用环境；
- saltstack： 使用python编写，需要在每台机子上安装agent，执行效率更高；
- puppet： 使用ruby编写，功能强大，配置复杂，重型，适合大型环境；
- fabric：使用python编写，agentless，功能比较简单；

由于puppet是使用ruby编写的，国内ruby玩家的数量远少于python，因此saltstack和ansible的使用者相对来说就比较多。fabric由于功能太少，完全能被ansible替代，所以企业用户很少使用fabric做复杂的任务流编排。

## Ansible

### 特性 

这里简单说一下ansible的主要特性：

- 模块化：调用特定的模块，完成特定的任务，比如说rabbitmq模块特定用于部署rabbitmq，redis模块特定用户部署redis等；
- 有paramiko，pyyaml，jinja2三个关键模块，分别的功能是ssh连接并操作，解析yaml文件，jinjia2模板引擎；
- 支持自定义模块；
- 基于python实现；
- 部署简单，基于python和ssh，agentless；
- 安全，基于openssh；
- 支持playbook编排任务；
- 幂等性，第一次执行某个ansible的操作和第n次执行同样的ansible操作对主机状态的改变结果是一样的；
- 无需代理不依赖pki；
- 可使用任何语言写模块；
- yaml格式，编排任务，支持丰富的数据结构；
- 较强大的多层解决方案；

### 安装

只要用pip安装即可。

```shell
> pip install ansible
```

或者

```
> pip3 install ansible
```

完成。



配置文件主要在：

1. `/etc/ansible/ansible.cfg`，ansible工具本身的配置。
2. `/etc/ansible/hosts`，静态主机清单（Inventory）配置。
3. `/etc/ansible/roles/`，ansible-roles可以放在这。



### 组件

#### Inventory

主机清单。对于批量主机操作，为了便捷的使用其中的部分主机，可以在inventory file中将其分组命名，默认的inventory file为`/etc/ansible/hosts`，inventory file可以有多个，且也可以通过Dynamic Inventory来动态生成。inventory文件的格式符合`INI`文件风格，中括号中的字符为组名。可以将同一个主机同时归并到多个不同的组中；如果目标主机使用非默认的SSH端口，还可以在主机名称之后使用冒号加端口号来表明。举个例子：

```
[webserver]
172.1.1.40:2222
10.2.2.2
www.bbb.com

# 主机机名遵循相似的命名模式，还可使用列表的方式标识个主机
[mysqlserver]
db[01:10].dddd.com

[redisserver]
rds-[a:f].cloud.com

# 在inventory中定义主机时为其添加主机变量以便于在playbook中使用
[appserver]
10.3.3.3 maxMemoryUsage=60
10.4.4.4 servicePort=8099

# 定义指定组内所有主机上的在playbook中可用变量
[appsever:vars]
config-center=172.3.4.5
enable-water=true
```



#### AD-Hoc

命令行模式使用ansible。

使用`ansible --help`可查看如何使用

```shell
fwz@fwz-mbp ~/Workspace> ansible --help
Usage: ansible <host-pattern> [options]

Define and run a single task 'playbook' against a set of hosts

Options:
  -a MODULE_ARGS, --args=MODULE_ARGS
                        module arguments
  --ask-vault-pass      ask for vault password
  -B SECONDS, --background=SECONDS
                        run asynchronously, failing after X seconds
                        (default=N/A)
  -C, --check           don't make any changes; instead, try to predict some
                        of the changes that may occur
  -D, --diff            when changing (small) files and templates, show the
                        differences in those files; works great with --check
......
......
```

简单的说，语法就是`ansible <host-pattern> [-f forks] [-m module_name] [-a args]`

本质上，就是调用相应的ansible模块，并传入相应的参数。

举几个例子:

ping所有主机（使用ping模块）：

```shell
> ansible all -a ping --ask-pass
ansible all -m ping --ask-pass
SSH password:
xx.xx.xx.xxx | SUCCESS => {
    "changed": false,
    "ping": "pong"
}
```

改变文件属性（使用file模块）:

```shell
> ansible mysql-server -m file -a 'owner=mysql group=mysql mode=644 path=/tmp/fstab.ansible'
```

拷贝文件（使用copy模块）:

```shell
> ansible storageIndex-server -m copy -a 'src=/home/devops/cloud-relay-1.2.4-SNAPSHOT.tar.gz dest=/root/cloud-storageIndex/cloud-relay-1.2.4.tar.gz'
```

配置Rabbitmq（使用rabbitmq_user模块）:

```shell
> ansible rabbitmq-server -m rabbitmq_user -a 'user=new_user password=root tags=administrator vhost=/ state=present'
```

执行linux的命令（使用shell模块）：

```shell
> ansible webserver -m shell -a 'netstat -nutlp > netstat_temp.txt'
```



#### Playbook

剧本。虽然可以在命令行中使用ansible，但是明显功能有限，无法支持复杂环境的配置管理工作。首先，命令行一次只能执行一条命令，其次，由于通常是手敲命令，因此不好重复。类似于linux中的shell脚本，ansible提供了playbook，翻译为剧本，用于组织基于多个ansible模块交互的工作流编排。

linux的脚本文件是`.sh`文件，ansible-playbook则是`.yaml`文件，即使用YAML语法。YAML的语法和其他高阶语言类似，并且可以简单表达清单、散列表、标量等数据结构。

直接举个例子：

```yaml
---
# This playbook will install mysql and create db user and give permissions.
- hosts: $servername
  remote_user: root
  - name: Install Mysql package
    yum: name={{ item }} state=installed
    with_items:
     - mysql-server
     - MySQL-python
     - libselinux-python
     - libsemanage-python

  - name: Configure SELinux to start mysql on any port
    seboolean: name=mysql_connect_any state=true persistent=yes
    when: sestatus.rc != 0

  - name: Create Mysql configuration file
    template: src=my.cnf.j2 dest=/etc/my.cnf
    notify:
    - restart mysql

  - name: Start Mysql Service
    service: name=mysqld state=started enabled=yes

  - name: insert iptables rule
    lineinfile: dest=/etc/sysconfig/iptables state=present regexp="{{ mysql_port }}"
                insertafter="^:OUTPUT " line="-A INPUT -p tcp  --dport {{ mysql_port }} -j  ACCEPT"
    notify: restart iptables

  - name: Create Application Database
    mysql_db: name={{ dbname }} state=present

  - name: Create Application DB User
    mysql_user: name={{ dbuser }} password={{ upassword }} priv=*.*:ALL host='%' state=present
```

例子很好理解，首先是定义了这个playbook在哪个主机组中执行，以及对应的用户，接着是一组task，分别调用了yum、seboolean、template、service、lineinfile、mysql_db、mysql_user模块。其中使用了notify作handler。

hosts：playbook中的每一个play的目的都是为了让某个或某些主机以某个指定的用户身份执行任务。hosts用于指定要执行指定任务的主机，其可以使一个或多个由冒号分隔主机组；

remote_user: remote_user也可用于各task中，也可以通过指定其通过sudo的方式在远程主机上执行任务，其可用于play全局或其任务；此外，甚至可以在sudo时使用sudo_user指定sudo时切换的用户。

task：play的主题部分是task list。task list中的各任务按次序逐个在hosts中指定的所有主机上执行，即在所有主机上完成第一个任务后再开始第二个。在运行自上而下某playbook时，如果中途发生错误，所有已执行任务都可能回滚，在更正playbook后重新执行一次即可。

taks的目的是使用指定的参数执行模块，而在模块参数中可以使用变量。模块执行是幂等的。这意味着多次执行是安全的，因为其结果均一致。

每个task都应该有其name，用于playbook的执行结果输出，建议其内容尽可能清晰地描述任务执行步骤，如果为提供name，则action的结果将用于输出。

定义task可以使用"action: module options"或”module：options“的格式推荐使用后者以实现向后兼容。如果action一行的内容过多，也中使用在行首使用几个空白字符进行换行。

```yaml
tasks:
 - name:make sure apache is running
   service: name=httpd state=started
```



在众多的模块中，只有command和shell模块仅需要给定一个列表而无需使用"key=value"格式

```yaml
tasks:
 - name: run this command and ignore the result
   shell: /usr/bin/somecommand || /bin/true
```



Handler: 用于当关注的资源发生变化时采取一定的操作。

"notify"这个action可用于在每个play的最后被触发，这样可以避免多次有改变发生时每次都执行执行的操作，取而代之，仅在所有的变化发生完成后一次性地执行指定操作，在notify中列出的操作称为handlers，也即notify中调用handlers中定义的操作。

```yaml
- name: template configuration file
 template: src=template.j2 dest=/etc/foo.conf
 notify:
   - restart memcached
   - restart apache
```



#### Roles

ansible自1.2版本引入的新特性，用于层次性、结构化地组织playbook。roles能够根据层次型结构自动转载变量文件、tasks以及handlers等。要使用roles只需要在playbook中使用include指令即可。简单来讲，roles就是通过分别将变量、文件、任务、模板以及处理器放置于单独的目录中，并可以便捷地include他们的一种机制。角色一般用于基于主机构建服务的场景中，但也可以使用于构建守护进程的场景中。

一个常用的role结构：

```shell
site.yml
webserver.yml
fooserver.yml
roles/
   common/
       files/
       templates/
       tasks/
       handlers/
       vars/
       meta/
   webserver/
       files/
       templates/
       tasks/
       handlers/
       vars/
       meta/
```

在ansible-playbook中可以这么使用role：

```yaml
- hosts: webserver
 roles:
   - common  
   - webserver
```

向role中传递参数：

```yaml
- hosts: webserver
 roles:
   - common
   - { role: foo_app_instance, dir:'/opt/a',port:5000}
   - { role: foo_app_instance, dir:'/opt/b',port:5001}
```

- task目录：至少应该包含一个为main.yml的文件，其定义了此角色的任务列表；此文件可以使用include包含其它的位于此目录中的task文件；
- file目录：存放由copy或script等模板块调用的文件；
- template目录：template模块会自动在此目录中寻找jinja2模板文件；
- handlers目录：此目录中应当包含一个main.yml文件，用于定义此角色用到的各handlers，在handler中使用inclnude包含的其它的handlers文件也应该位于此目录中；
- vars目录：应当包含一个main.yml文件，用于定义此角色用到的变量
- meta目录：应当包含一个main.yml文件，用于定义此角色的特殊设定及其依赖关系；ansible1.3及其以后的版本才支持；
- default目录：应当包含一个main.yml文件,用于为当前角色设定默认变量时使用此目录；

官方提供了很多ansible-roles的例子：[ansible/**ansible-examples**](https://github.com/ansible)

#### Galaxy

用于分享和下载ansible-role的平台，类似于同性交友网站github。