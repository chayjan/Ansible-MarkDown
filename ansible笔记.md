## Ansible简介

### 一. Ansible

#### 1. ansible-galaxy

##### Ⅰ. 概念: 

可简单理解为GitHub或PIP功能,通过 ansible-galaxy 命令,查找安装优秀的roles;

下载地址 : https://galaxy.ansible.com

##### Ⅱ. 命令格式:

```yaml
#使用格式:
ansible-galaxy [init|info|install|list|remove] [--help] [options]...

#三大部分解析:
1. [init|info|install|list|remove]
init: 初始化本地Roles配置,以备上传Roles到Galaxy
info: 列表指定Role的详细信息
install: 下载并安装Galaxy指定的Roles到本地;
list: 列出本地已经下载的Roles
remove: 删除本地已下载的Roles

2. help用法显示[--help]
针对第一部分 init/info等功能,后面跟上 --help 可单独显示该项用法
	如:ansible-galaxy init --help #返回init选项的用法说明

3. 参数项[options]
  该参数,结合第一部分的参数完成 ansible-galaxy 完整的功能用法,如:
  ansible-galaxy init -f role_name

```

#### 2. ansible-pull

##### Ⅰ. 概念:

该指令的使用涉及Ansible的一种工作模式: pull模式(Ansible默认使用push模式,工作机理与pull相反).

pull模式适用于以下场景:

​	①需要配置的机器数量巨大,即使使用高并发线程也得花很多时间

​	 ②在刚启动没有网络连接的主机上运行Ansible

##### Ⅱ. 命令格式

```yaml
#命令使用格式
ansible-pull [options] [playbook.yml]
#通过ansible-pull结合Git 和 crontab 一并实现,实现原理:
---通过crontab定期拉取指定git版本到本地,并以指定模式自动运行制定好的指令(playbook)
示例:
*/20 * * * * root /usr/local/bin/ansible-pull -o -C 2.1.0 -d 
/srv/www/king-gw/ -i /etc/ansible/hosts -U 
git://git.kingifa.com/king-gw-ansiblepull >> /var/log/ansible-pull.log 2>&1

#ansible-pull通常在配置大量机器的场景下使用,灵活性稍有欠缺,但效率可以无限提升,但要求高;
	
```

#### 3.ansible-doc

​	ansible-doc 是Ansible模块文档说明,针对每个模块的用法有详细的说明和案例介绍;类似Linux的man 命令

```yaml
命令格式:
ansible-doc [options] [moudle...]
示例:
#列出支持的模块;
ansible-doc -l
#模块功能说明:
ansible-doc ping 

```

#### 4. ansible-playbook

##### Ⅰ. 概念:

​	通过读取预先编写好的playbook文件实现批量管理,可以理解为按照一定条件祖晨的ansible任务集,ansible-playbook命令后跟YAML格式的playbook文件,执行编排好的任务集;

##### Ⅱ. 命令格式

```yaml
#使用格式:
ansible-playbook playbook_name.yaml
# *** playbook具有编写简单,可定制性高,灵活方便,以及可固话日常所有操作的特点,后续展开;

```

#### 5. ansible-vault

##### Ⅰ. 概念:

​	ansible-vault主要用于配置文件加密,如playbook文件中包含机密信息,可用ansible-vault 加密/解密文件;

##### Ⅱ. 命令格式:

```yaml
#Usage:
ansible-vault [create|decrypt|edit|encrypt|rekey|view] [--help] [options] filename
#exmaples:

#1. 加密a.yml文件:
ansible-vault encrypt a.yml #提示输入加密密码,加密后打开文件显示乱码
#2. 解密a.yml文件:
ansible-vault decrypt a.yml #提示输入预设密码解密,解密后可正常查看文件


```

#### 6. ansible-console

##### Ⅰ. 概念:

​	ansible-console 是ansible给用户提供的一款交互式工具,用户可以在ansible-console虚拟出来的终端上想shell一样使用ansible内置的各种命令;

##### Ⅱ. 使用示例:



#### 7. Ansible Inventory

##### Ⅰ. Concept:

​	inventory是ansible管理主机信息的配置文件,相当于系统hosts文件功能, 默认存放 /etc/ansibel/hosts ; 为方便批量管理主机, ansible通过inventory定义主机和组, 使用时通过

**-i** 或 **--inventory-file** 指定读取, 与ansible命令组合使用如下:

```
ansible -i /etc/ansible/hosts webs -m ping 	
#只有一个/当前路径存在inventory时不用指定路径
```

##### Ⅱ. 定义主机和组

​	Inventory配置文件遵循INI文件风格, **中括号内的字符为组名**, 支持同一个主机归并至不同分组中;

目标主机使用非默认SSH端口时,可在主机名后使用冒号加端口号来标明; 以行为单位分隔配置

```yaml
# 1. "#" 开头的行表示为注释行

# 2. Inventory 可以直接为IP地址 示例:
192.168.37.149

# 3. Inventory支持Hostname方式,冒号加端口号,示例:
ntp.magedu.com:49721
nfs.magedu.com		#默认端口号便可省略(22)

# 4. 中括号表示一个分组开始,紧随其后的主机均属于该组成员,
[webservers]
web1.magedu.com
web[10:20].magedu.com 
#[10:20]表示10到20之间所有:web10.magedu.com,web11.magedu.com...web2.magedu.com

web2.magedu.com	#空行之后的主机也属于该组

# 5. 
[dbservers]
db-a.magedu.com
db-[b:f].magedu.com #[b:f]之间所有字符的主机,同[10:20]用法

```

##### Ⅲ. 定义主机变量	

​	可以修改inventory配置实现非标准化配置需求,如web服务端口修改,在定义主机时添加为主机变量,以便在playbook中针对主机个性化的要求

```yaml
#示例:
[webservers]
web1.magedu.com http_port=808 maxRequestPerChild=801 
#自定义http_port端口号为808;配置maxRequestPerChild为801

```

##### Ⅳ. 定义组变量

​	ansible支持定义组变量, 针对大量机器的变量定义需求, 赋予指定组内所有主机在playbook中可用的变量, 相当于该组下所有主机的的统一变量, 定义组变量示例如下:

```yaml
#组变量示例:
[groupservers]
web1.magedu.com
web2.magedu.com

[groupservers:vars]
ntp_server=ntp.magedu.com	#定义groupservers组内所有主机ntp_server值为ntp.magedu.com
nfs_server=nfs.magedu.com	#定义groupservers组内所有主机nfs_server值为nfs.magedu.com
```

##### Ⅴ. 定义组嵌套及组变量

​	inventory中, 组可以包含其他组,也可以像组中的主机指定变量. 不过这些变量只能在playbook中使用,而ansible不支持. 组与组之间可以相互调用, 并可以向组中的主机指定变量;

```yaml
#组嵌套示例:
[apache]
httpd1.magedu.com
httpd2.magedu.com

[nginx]
ngx1.magedu.com
ngx2.magedu.com

[webservers:children]
apache
nginx

[webservers:vars]
ntp_server=ntp.magedu.com
#不常用了解即可
```

##### Ⅵ. 多重变量定义

​	变量除了在inventory中定义, 也可以独立于 Inventory 文件之外单独存储到yaml 格式的配置文件中, 变量检索通常在如下4个位置:

```yaml
# 变量检索位置:
1. Inventory 配置文件(默认/etc/ansible/hosts)
2. Playbook 中 vars 定义的区域
3. Roles 中 vars 目录下的文件
4. Roles 同级目录 group_vars 和 hosts_vars 目录下的文件

#假如football主机同属faleigh 和 webservers 组,那么其变量在如下文件中均有效:
/etc/ansible/group_vars/faleigh
/etc/ansbie/group_vars/webservers
/etc/ansible/host_vars/football
#变量设置尽量使用同一种方式,便于管理;

```

##### Ⅶ. 其他inventory参数列表

​	ansible基于ssh链接inventory中指定的远程主机时,还内置了许多其他参数,用于指定交互方式,部分重要参数如下: 

```yaml
ansible_ssh_hosts : 	# 指定链接主机
ansible_ssh_port : 		# 指定ssh链接端口
ansible_ssh_user : 		# ssh 链接用户
ansible_ssh_pass : 		# 指定ssh链接密码
ansible_sudo_pass : 	# 指定ssh连接的sudo密码
ansible_ssh_private_key_file : # 指定特有的私钥文件
......
# 这些参数均可以直接写在命令行或playbook文件中,以覆盖配置文件中的定义...
```

#### 8. ansible与正则

​	ansible/ansible-playbook 支持正则表达式用法, 使用方法简单  : 

```yaml
# 使用格式:
ansible <pattern_goes_here> -m <moudle_name> -a <arguments>

#示例:重启 webservers 组所有主机的httpd服务:
ansible webservers -m service -a "name=httpd state=restarted"

# 1. All(全量)匹配: 匹配所有主机, all 或 * 功能相同, 如检测所有主机存活状况:
ansible all -m ping
ansible "*" -m ping	# all 与 * 功能相同,但 * 要引号引起来

# 2. 逻辑或(or)匹配:
// 如果希望同时对多台主机或多个组同时执行,相互之间使用 : 分割即可,示例:
ansible "web1:web2" -m ping

# 3. 逻辑非(!)匹配:
// 针对多重条件的匹配规则,使用如下:
webservers:!phonenix	#所有在webservers组但不在phoenix组内的主机

# 4. 逻辑与(&)匹配:
webservers:&staging	 # webservers和staging组同时存在的主机

# 5. 多条件组合:
// webservers 和 dbservers 两个组内存在的所有主机 在staging中存在且在phoenix组中不存在的主机:
webservers:dbservers:&staging:!phoenix

# 6. 模糊匹配:
* 通配符,在ansible中表示0或多个任意字符,示例:
*.magedu.com 			# 所有以 .magedu.com结尾的主机的组合:
one*.com:dbservers 		# one开头.com结尾的所有主机和dbservers组中的所有主机

# 7. 域切割:
# ansible底层基于python,域切割功能同样适用,以inventory内容示例:
[webservers]
cobweb
webbing
weber
#通过截取数组下表可以获取对应变量值:
webservers[0]		# == cobweb
webservers[-1] 		# == webber
.....

# 8. 正则匹配:
# "~" 开始表示正则匹配,如:
~(web|db).*\.example\.com
#示例: 检测 beta.example.com,web.example.com,green.example.com,
# 		   beta.example.org,web.example.org,green.example.org的存活状态
ansible "~(beta|web|green)\.example\.(com|org)" -m ping

# 检测inventory中所有以192.168开头的服务器存活信息:
ansible ~192\.168\.[0-9]\{\2}.[0-9]{2,} -m ping

```

### 二. Ansible Ad-Hoc 命令集

​	Ad-Hoc, 即 "临时命令", 功能上相对于ansible-playbook而言. ansible提供两种任务完成方式,一是Ad-Hoc, 即 ansible ; 另一种就是 ansible-playbook ; 前者使用简单或者临时任务,相当于shell命令, 后者适用于复杂或者固化任务, 相当于shell scripts.

#### 1. 命令集用法:

​	Ad-Hoc 命令集由 /usr/bin/ansible实现,命令用法如下;

```yaml
# 使用格式:
ansible <hosts-pattern> [options]

# 可用选项:
-v, --verbose : # 输出详细执行信息, -vvv 可得到执行过程所有信息
-i PATH, --inventory=PATH : # 指定inventory信息
-f NUM, --forks=NUM : # 并发线程数量,默认5
--private-key=PRIVATE_KEY_FILE : # 指定秘钥文件
-m NAME, --module-name=NAME :	#指定执行使用的模块
-M DIRECTORY, --module-path=DIRECTORY : # 指定模块存放路径
-a 'ARGUMENTS', --args='ARGUMENTS'	: # 模块参数
-k --ask-pass SSH : # 认证密码
-K, --ask-sudo-pass sudo : # 用户密码(sudo时用)
-o,--one-line : # 标准输出至一行
-s, --sudo : # linux下的sudo命令
-t DIRECTORY, --tree=DIRECTORY : # 输出信息至DIRECTORY目录下,文件以主机名命名
-T SECONDS,--timeout=SECONDS : # 连接远程主机最大超时,单位s
-B NUM,--background=NUM : # 后台执行命令,超过NUM秒后中止执行现在的任务
-P NUM,--poll=NUM : # 定期返回后台任务进度
-u USERNAME,--user=USERNAME : # 指定远程主机以USERNAME用户运行命令
-U SUDO_USERNAME,--sudo-user=SUDO_USERNAME : # 使用sudo
-c CONNECTION,--connection=CONNECTION :
# 指定连接方式 paramiko(SSH) | ssh | local , local常用于crontab/kickstarts 
-l SUBSET,--limit=SUBSET : # 指定运行主机
-l~REGEX,--limit=~REGEX : # 正则指定运行主机
--list-hosts : # 列出符合条件的主机列表,不执行任何任务
```

​	场景示例 1 :

```yaml
# 1. 检查proxy组主机是否存活
ansible proxy -f 5 -m ping
# 2. 返回proxy组所有主机hostname:
ansible proxy -s -m command -a 'hostname' -vvv
# 3. web组所有主机列表:
ansible web --list
# 4. 对10.21.40.61 server以root执行 sleep 20 ,最大连接超时2s,设置为后台运行模式,且执行过程2s输出一次进度,如5s还未完成,终止该任务:
time ansible 10.21.40.61 -B 5 -P 2 -T 2 -m command -a 'sleep 20' -u root
```

#### 2. Ad-Hoc查看系统设置

​	通过df, free 等命令查看系统设置

```yaml
# 1. 批量查看apps组所有主机的磁盘容量(command模块)
ansible apps -a "df -lh"
# 2. 批量查看远程主机内存使用情况(shell模块)
ansible apps -m shell -a "free -m"

```

#### 3. Ad-Hoc研究Ansible并发特性 (待补充)

#### 4. Ad-Hoc研究Ansible模块的使用

#### 5. Ad-Hoc组管理和特定主机变更

#### 6. Ad-Hoc 用户与组管理



### 三. Ansible-Playbook

​	ansible 使用yaml语法描述配置文件, 其任务配置文件成为Playbook, 每一个Playbook中可以包含一系列任务(play)...

#### 1.Playbook语法

##### Ⅰ. 多行缩进

```yaml
#YAML(Yet Another Markup Language)
// yaml数据结构以类似大纲的缩排方式呈现,结构通过缩进表达,连续的项目通过"-"来表示,map结构里的key/value对用":"分隔,格式如下:

house:
family:
name: Doe
parents:
	- John
	- Jane
children:
	- Paul
	- Mark
	- Simone
address:
number: 34
street: Main Street
city: Nowheretown
zipcode: 12345
...

# 1. 字符串不一定要用双一号标识
# 2. 缩排的空格字符数目不重要,相同级别的元素左对齐即可(不能使用Tab字符,用空格)
# 3. 允许在文件中加入空行,增加可读性
# 4. 选择性的符号"..."可用来标识档案结尾
```

##### Ⅱ. 单行缩写

```yaml
# Yaml 也有用来描数好几行相同结构的数据的缩写语法,数组用'[]'括起来,hash用'{}'括起来.上面的示例可缩写为:
house: 
	family: { name: Doe, parents: [Jone,Jane], children: [Paul,Mark,Simone] }
	address: { number: 34, street: Main Street, city: Nowheretown, zipcode: 12345 }
	
```

##### Ⅲ. playbook文件示例

```yaml
---
# 这个是你选择的主机
- hosts: webservers
# 变量:
  vars:
    http_port: 80
  	max_clients: 200
# 远端执行权限
  remote_user: root
# 任务
  tasks:
    # 利用YUM模块来操作
    - name: ensure apache is at the latest version
      yum: pkg=hpptd state=latest
    - name: write the apache config file
      template: src=/srv/httpd.j2 dest=/etc/httpd.conf
  # 触发重启服务器
  notify: restart apache
  	- name: ensure apache is running
      service: name=httpd state=started
  #这里的restart Apache 和上面的触发配对,就是handlers的作用:
  handlers:
  	- name: restart apache
      service: name=httpd state=restarted

# Playbook语法总结:
1. 需要以"---"开头,需在顶行首写
2. 次行开始正常写playbook功能,建议先写明功能
3. 使用#号注释代码
4. 缩进必须统一,空格/tab不能混用;
5. 缩进级别必须一致,同样缩进表示同样的级别,程序判断配置是通过缩进结合换行来实现的
6. yaml文件大小写与linux判断方式一致,kv值均需大小写敏感
7. k/v可同行也可换行,同行使用": "分隔,换行使用"-"分隔
8. 完整的代码块功能需最少元素,须包括 name: task 
9. 一个name只能包括一个task
  
  
```

#### 2. Playbook案例

​	Ad-Hoc一次执行一个模块, playbook使ansible功能更为完善, 固化操作,减少人为失误,满足工程量较大的工具所局别的复杂度,可扩展等要求.

​	如如下一段安装Apache的shell脚本

```shell
#!/bin/bash
# 安装 Apache
yum install --queit -y httpd httpd-devel
# 复制配置文件
cp /path/to/config/httpd.conf  /etc/httpd/conf/httpd.conf
cp /path/to/httpd-vhosts.conf  /etc/httpd/conf/httpd-vhosts.conf
#启动Apache,并设置开机启动
service httpd start
chkconfig httpd on
```

​	将上述shell脚本转化为完整的playbook, 内容如下:

```yaml
---
- hosts: all
  tasks: 
  - name: "安装 Apache"
	command: yum install --quiet -y httpd httpd-devel
  - name: "复制文件"
	command: cp /tmp/httpd.conf /etc/httpd/conf/httpd.conf
	command: cp /tmp/httpd-vhosts.conf /etc/httpd/conf/httpd-vhosts.conf
  - name: "启动Apache,并设置开机启动"
    command: service httpd start
    command: chkconfig httpd on

```

#### 3. playbook与shell脚本差异对比

​	shell脚本转为playbook运行时,ansible会留下清晰的执行过程,同时ansible自带幂等性判断机制,重复执行一个playbook时,当ansible发现系统的现有状态与playbook将要实现的状态一致是,将会自动跳过该操作;而shell脚本肯定会把所有操作再执行一遍

#### 4. Ansible-playbook实战

##### Ⅰ. 限定执行范围

```yaml
# playbook指定的一批主机中有个别主机需要变更时,无需修改playbook文件本身,而是通过一些选项就可直接限定查看ansible命令的执行范围:
1. --limit
# 可以通过 修改  "-hosts: " 字段指定那些主机应用playbook操作
# 指定一台主机: www.magedu.com
# 指定多台主机: www.magedu1.com,www.magedu2.com
# 指定一组主机: dbserver
# 也可以通过ansible-playbook命令指定主机或组
ansible-playbook play.yml --limit webservers

2. --list-hosts
# 执行playbook时,哪些主机会受到影响,可用如下命令:
ansible-playbook play.yml --list-hosts

```

##### Ⅱ. 用户与权限设置

```yaml
--remote-user
# playbook中,hosts字段没有定义users关键字时,会使用inventory文件中定义的用户,如果inventory中没有定义,则默认使用当前系统用户身份链接远程主机,也可在playbook中直接指定用户:
ansible-playbook play.yml --remote-user=tom

--ask-sudo-pass
# 传递sudo密码到远程追,保证sudo命令正常运行

--sudo
# 强制所有play都是用sudo用户,同时指定--sudo-user指定执行那个用户权限,不指定默认root
ansible-playbook play.yml --sudo --sudo-user=tom --ask-sudo-passs

-i PATH, --inventory=PATH	# 指定inventory文件,默认为/etc/ansible/hosts
-v, --verbose				# 显示详细输出,也可使用-vvv精确到每分钟输出
-e VARS, --extra-vars=VARS	# 定义playbook中使用的变量,格式为"key=value"
-f NUM, --forks=NUM 		# 指定并发执行任务数量,默认为5,根据机器性能调整此值
-c TYPE, --connection=TYPE	# 链接远程的方式,默认SSH,设为local时,则只在本地执行playbook
--check 					# 检测模式,playbook所有任务将在每台主机上检测,但并不真正执行
```

#### 5. Ansible部署Node.js

##### Ⅰ.添加第三方源

​	部署服务器时,确保指定软件包可用或最新,首先添加额外的源(YUM或APT)

shell脚本示例:

```shell
#!/bin/bash
#导入Remi GPG秘钥
wget http://rpms.famillecollet.com/RPM-GPG-KEY-remi \-O /etc/pki/rpm-gpg/RPM-GPG-KEY-remi
rpm --import /etc/pki/rmp-gpg/RPM-GPG-KEY-remi
# 安装 Remi 源
rpm -Uvh --quiet \
http://rpms.famillecollet.com/enterprise/remi-release-6.rpm
# 安装 EPEL源
yum install epel-release
# 安装 Node.js (npm及依赖)
yum --enablerepo=epel install npm

```

ansible-playbook实现,可以让这一过程更据健壮性,且更容易理解,结构更加清晰

```yaml
--- 
- hosts: all
  tasks:
    - name: 导入Remi GPG 秘钥
      rpm_key: "key={{ item }} state=present"
      with_items:
        - "http://rpms.famillecollet.com/RPM-GPG-KEY-remi"
    - name: install Remi repo
      command: "rpm -Uvh --force {{ item.href }} creates={{ item.creates }}"
      with_items:
      	- href: "http://rpms.famillecollet.com/enterprise/remi-release-6.rpm"
      creates: "/etc/yum.repos.d/remi.repo"
    - name: 安装 Remi 源
      yum: name=epel-release state=present
    - name: 关闭防火墙
      service: name=iptables state=stopped
    - name: 安装NodeJS和npm
      yum: name=npm state=present enablerepo=epel
    - name: 使用taobao的npm源:
      command: > npm config set regisetry https://registry.npm.taobao.org
    - name: 关闭npm的https:
      command: > npm config ste strict-ssl false
    - name: 安装Forever(用于启动Node.js app)
      npm: name=forever global=yes state=latest
      
```

这个例子没走完,待续。。。

#### 6.Ansible部署Tomcat

##### Ⅰ. 定义并设置Handlers

```yaml
# 在playbook开头处引入变量文件vars.yaml,playbook命名为playbook.yml.开头内容定义如下:
---
- hosts:all
  var_files:
    -vars.yml
```

##### Ⅱ. vars.yml文件

```yaml
#在playbook相同目录下,创建变量文件vars.yml,变量定义如下:
---
# 软件包下载路径:
download_dirs: /tmp
# Tomcat 版本号:
tomcat_version: 8.0.35
# Tomcat 安装路径:
tomcat_dir: /opt/tomcat
# Solr 安装路径:
solr_dir: /opt/solr
# Solr 版本号(最新版):
solr_version: 6.1.0
```

##### Ⅲ. 定义handlers:

```yaml
handlers:
  - name: start tomcat		
    command: >
      initctlstart tomcat
    ...
```

##### Ⅳ. 安装java

```yaml
tasks:
  - name: 发送jdk软件包和java配置文件到远程主机
    copy: "src={{ item.src }} dest={{ item.dest }}"
    with_items:
      - src: "./jdk-8u11-linux-x64.tar.gz"
        dest: "/tmp/"
      - src: "./java.sh"
        dest: "/etc/profile.d/"
  - name: 创建java目录
    command: >
      mkdir -p /opt/java
  - name: 解压 JDK 软件包
    command: >
      tar -C /opt/java -xvf {{ download_dir }}/jdk-8u11-linux-x64.tar.gz
        --strip-components=1
  - name: 为java命令更新alternatives
    command: >
      update-alternatives --install /usr/bin/java java/opt/java/bin/java 300
  - name: 为javac更新alternatives
    command: >
      update-alternatives --install /usr/bin/javac javac /optjava/bin/
      javac 300
...

#在任务中用到java.sh文件定义java运行所需的环境变更,内容如下:
export JAVA_HOME="/opt/java"
export CLASSPATH=${JAVA_HOME}/lib:${JAVA_HOME}/jre/lib
export JRE_HOME=${JAVA_HOME}/jre
export PATH=$PATH:$JAVA_HOME/bin
    
    
```











































