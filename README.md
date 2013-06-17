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
* CKAN2.0日本語環境インストールマニュアル (Ubuntu12.04)
TODO: URL を開示してよいか確認。

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
* osx$ mkdir /your/work/directory
* osx$ cd /your/work/directory
* osx$ vagrant init precise64 http://files.vagrantup.com/precise64.box # download Vangrantfile to /your/work/directory
* osx$ vagrant up # install Ubuntu 12.04 64 to your VirtualBox, and run it.
* osx$ vagrant ssh # log in to the new Ubuntu 12.04
* vagrant@precise64:~$ # now you get a shell access from your new Ubuntu 12.04

### Ubuntu 12.04 上で CKAN 2.0 をインストールする
* vagrant@precise64:~$ sudo apt-get update
* vagrant@precise64:~$ sudo vi /etc/default/locale -> modify LC_ALL="C.UTF-8" (which is originally "en_US")
* vagrant@precise64:~$ sudo apt-get install language-pack-ja postgresql libpq-dev python-dev python-pip python-virtualenv git-core openjdk-7-jdk libapache2-mod-wsgi tomcat7 postfix
* vagrant@precise64:~$ sudo -u postgres psql -l
make sure you have an output like:
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
!! the Encoding must be UTF8 not LATIN1. If not, you need to make sure your LC_ALL indicates the use of UTF-8.
