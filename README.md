vagrant-ckan-installlog
=======================

Install log for CKAN 2.0 with Japanese language settings to Ubuntu 12.04 in VirtualBox in Vagrant in OS X

OS X 上の vagrant で、VirtualBox 上の Ubuntu 12.04 に CKAN 2.0 を日本語設定つきでインストールした記録

aim
---
to establish a CKAN 2.0 environment which is easy to handle from OS X.

OS X から扱いやすい CKAN 2.0 環境を確立すること。

status
------
ongoing 作業中

references
----------
* [CKAN2.0日本語環境インストールマニュアル (Ubuntu12.04)](https://docs.google.com/document/d/1gsrFF5IUwlabzQcoVzkfeM7VnwZ0XJUzvQOGhQVFfjw/edit?pli=1)

environment
-----------
OS X 10.8.4

the process
-----------
### Ubuntu 12.04 環境を準備する
* Install [VirtualBox](https://www.virtualbox.org/wiki/Downloads)
* In VirtualBox, change the default VM folder (デフォルトの仮想マシンフォルダー) of VirtualBox -> 環境設定 if you want to save disk usage of your SSD drive (e.g. if you are running on MBA).
* Install [Vagrant](http://downloads.vagrantup.com)
* "export VAGRANT_HOME=/where/you/have/enough/space" on your .bash_profile if you want and source .bash_profile.

```sh
osx$ mkdir /your/work/directory
osx$ cd /your/work/directory
osx$ vagrant init precise64 http://files.vagrantup.com/precise64.box # download Vangrantfile to /your/work/directory
osx$ vagrant up # install Ubuntu 12.04 64 to your VirtualBox, and run it.
osx$ vagrant ssh # log in to the new Ubuntu 12.04
vagrant@precise64:~$ # now you get a shell access from your new Ubuntu 12.04
```

### Ubuntu 12.04 上で CKAN 2.0 をインストールする

```sh
* vagrant@precise64:~$ sudo apt-get update
* vagrant@precise64:~$ sudo vi /etc/default/locale -> modify LC_ALL="C.UTF-8" (which is originally "en_US")
* vagrant@precise64:~$ sudo apt-get install language-pack-ja postgresql libpq-dev python-dev python-pip python-virtualenv git-core openjdk-7-jdk libapache2-mod-wsgi tomcat7 postfix
* vagrant@precise64:~$ sudo -u postgres psql -l
```

make sure you have an output like:

```
vagrant@precise64:~$ sudo -u postgres psql -l
                              List of databases
   Name    |  Owner   | Encoding | Collate |  Ctype  |   Access privileges   
-----------+----------+----------+---------+---------+-----------------------
 postgres  | postgres | UTF8     | C.UTF-8 | C.UTF-8 | 
 template0 | postgres | UTF8     | C.UTF-8 | C.UTF-8 | =c/postgres          +
           |          |          |         |         | postgres=CTc/postgres
 template1 | postgres | UTF8     | C.UTF-8 | C.UTF-8 | =c/postgres          +
           |          |          |         |         | postgres=CTc/postgres
(3 rows)
```

!! the Encoding must be UTF8 not LATIN1. If not, you need to make sure your LC_ALL indicates the use of UTF-8.

```sh
vagrant@precise64:~$ sudo -u postgres createuser -S -D -R -P ckanuser
Enter password for new role: (postgres)
Enter it again: (postgres)
vagrant@precise64:~$ sudo -u postgres createdb -O ckanuser ckantest
vagrant@precise64:~$ sudo vi /etc/tomcat7/server.xml 
```

以下の部分を編集してUTF-8の設定をする。

```xml
<Connector port="8080" protocol="HTTP/1.1" connectionTimeout="20000"
 URIEncoding="UTF-8" setCharacterEncoding="UTF-8" redirectPort="8443" />
```

テスト用にポート開ける．

```sh
vagrant@precise64:~$ sudo ufw allow 8080
```

Solr をインストールする。

```sh
$ sudo mkdir /usr/local/jakarta
$ cd /usr/local/jakarta 
$ sudo wget http://ftp.jaist.ac.jp/pub/apache/lucene/solr/4.2.1/solr-4.2.1.tgz
$ sudo mv solr-4.2.1.tgz solr-4.2.1.tar.gz
$ sudo tar xvzf solr­4.21..tar.g
$ sudo mkdir ../solr
$ sudo cp -a solr-4.2.1/example/solr/* ../solr/
$ sudo cp -a solr-4.2.1/contrib ../solr
$ sudo cp -a solr-4.2.1/dist ../solr
$ sudo chown -R tomcat7:tomcat7 ../solr/
$ sudo vi /usr/local/solr/collection1/conf/solrconfig.xml 
```

72-82行目の../../..を/usr/local/solr/collection1に置換

```sh
$ sudo vi /etc/tomcat7/Catalina/localhost/solr.xml
```

以下の内容で新規作成する．

```xml
<?xml version="1.0" encoding="utf-8"?>
<Context docBase="/usr/local/solr/dist/solr-4.2.1.war" debug="0" crossContext="true">
<Environment name="solr/home" type="java.lang.String" value="/usr/local/solr" override="true"/>
</Context>
```

```sh
$ sudo service tomcat7 restart
$ w3m http://localhost:8080 # it works が出ていることを確認。
$ sudo ufw deny 8080 # ポートを閉じる
```

