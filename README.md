## 概要

Ansible用playbookです
CentOS7(or8)環境に、Zabbix4.0-server(rpm)とZabbix-agent(rpm)の機能を自動設定する

## 対象となる環境

* CentOS7 ( or CentOS8 )
* インターネットにつながり、ansibleサーバからrootユーザで直接ssh(ssh公開鍵認証でログイン)できること
* 最小構成でインストールしたCentOS7(or8)で稼働確認実施

## 自動設定内容

* os(共通(common))
	+ selinux無効化
	+ ホスト名設定
	+ timezoneの設定(zone情報指定可能)
	+ zabbix-repoの登録

* zabbix-server
  + Firewallの穴開け(http,snmptrap,zabbix-server)
  + zabbix4.0(zabbix official repo)
  + httpd, php, db(mariadb or mysql)の設定
  + CentOS8の場合、mariadbではなく、mysqlを利用(https://support.zabbix.com/browse/ZBX-16465 の回避)
	
  
* zabbix-agent
	+ zabbix-agent4.0
  + zabbix-agentの設定(Server,ServerActive,HostnameItem)

# 指定可能なインストール先

* zabbix40/inventory/inventory.ini

```
[zabbix_servers] ... zabbix server
[zabbix_agents] ... zabbix-agent client
```

# 指定可能な設定内容

* zabbix40/inventory/inventory.ini 

```
[servers]
testsv01 ansible_ssh_host=192.168.11.1 ansible_ssh_user=root
[agents]
testcl01 ansible_ssh_host=192.168.11.2 ansible_ssh_user=root
[all:vars]
timezone="Asia/Tokyo"
zabbix_server_ip="192.168.11.1"
[servers:vars]
zabbix_mysql_password="password"
```

### playbook実行

ansibleサーバで実行
```
git clone https://github.com/mishikawan/zabbix40-ansible.git
ansible-playbook -i inventory/inventory.ini site.yml
```

無事完了すると、zabbixサーバ上でzabbixサーバが稼働しています。以下、URLでアクセス可能。
```
http://{zabbix-server-ip}/zabbix
  ID = Admin
  PASS = zabbix
```
