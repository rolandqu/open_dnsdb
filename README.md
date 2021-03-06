# OpenDnsdb

## 项目主页

OpenDnsdb 项目相关文档:  [文档链接](../../wikis/home)


## 简介

OpenDnsdb 是去哪儿网OPS团队开源的DNS管理系统，用于添加、修改、删除zones/records.
使用简单并可靠的方法管理View、ACL、网段等.
详尽的日志，便于审计.

OpenDnsdb并不是一个DNS服务器，而是一个对现有DNS服务器的管理系统，提供Web管理UI以及命令行工具等.

对OpenDnsdb的操作，会生成DNS配置文件并同步给DNS服务器。也就是说OpenDnsdb的故障或不可用并不会对DNS服务本身造成任何影响.


## 主要功能

* 支持 Bind 9.
* IP管理, 管理公司网段及ip，可以实现域名和ip的自动绑定
* 域名管理, 域名的增、删、改、查.
* View域名管理, view域名的增删改查、状态修改，view域名的迁移.
* 配置管理, 管理zone文件，线上配置与数据库配置同步，修改配置自动完成部署.
* 日志, 关键操作都有日志记录，并可通过页面进行查询，便于审查
* 支持RESTful API, 支持Webhook.
* 基于Python 2/3 开发, 支持Postgresql和SQLite.


## 应用结构

* docs/
	各种说明文档、手册, copyright/license等.

* dnsdb_fe/
	web ui

* script/
	同步脚本, 各种工具.

* etc/
	开发、测试环境的配置文件, 配置模板等.

* dnsdb_command.py
	数据库初始脚本

* dnsdb/constant
 常量设置，用到的正则匹配规则


## 安装手册
* 安装virtualenv: pip install virtualenv
* 项目克隆
* 切换到项目目录: cd open_dnsdb 
* 初始化项目python环境: python tools/install_venv.py
* 启用虚拟环境 source .venv/bin/activate 
* 初始化数据库
	*  export FLASK_APP=dnsdb_command.py
	*  export FLASK_ENV=beta
	*  flask deploy (生成测试账号: test)
* 生成程序控制脚本: tools/with_venv.sh python setup.py install
* 安装supervisor用于管理python进程:
	* 安装: sudo pip install supervisor
	* 生成默认配置: echo_supervisord_conf > /etc/supervisord.conf
	* 修改配置文件 vim /etc/supervisord.conf
		```
		[include]
		files = /etc/supervisor/conf.d/*.conf
		```
	* mkdir -p /etc/supervisor/conf.d
	* 添加openDnsdb项目配配置: 
		* dnsdb: cp etc/beta/supervisor-dnsdb.conf /etc/supervisor/conf.d/open-dnsdb.conf
		* updater(仅bind服务器需要): cp etc/beta/supervisor-updater.conf /etc/supervisor/conf.d/open-dnsdb-updater.conf
	* 启动: supervisord -c /etc/supervisord.conf
	* 查看是否启动成功: ps aux | grep supervisord
	* supervisorctl -c /etc/supervisord.conf