---
draft: false 
date: 2023-12-29
authors:
  - smaxad47
categories:
  - Game
---

# WOW架设教程记录
私服架设指南,相关推荐网站:  
[UIWOW](https://uiwow.com/)
[狮王游戏论坛](http://bbs.yxlyy.club/)
<!-- more -->

## 获取服务器端
本文举例使用AzerothCore作为演示.获取服务端的路径多样,如UIWOW,狮王游戏论坛,GITHUB,有收费有免费的,常见核心分别有:TrinityCore,AzerothCore,AshamaneCore,Skyfire Project,OregonCore,CMangos.  

## 所需要的东西
包含所有开服所需的东西,单机并不一定全需要:  

1. Mysql5.5+ (或MariaDB)  
2. 7.0 < PHP < 8.0  
3. Nginx (或者Caddy或者自己喜欢的)  
4. HeidiSql (或者NaviCat或者自己喜欢的)  
5. [WoWSimpleRegistration](https://github.com/masterking32/WoWSimpleRegistration)  
6. [azerothcore](https://github.com/azerothcore/azerothcore-wotlk)  

## 编译流程
**建议跳过!**  
下载编译好的服务端.  
这部分内容有心情再更新.
想架设干净的仿官服,建议使用仅带eluna,npcbot,ahbot的端  
如:[AZ3.35纯净端+模块端](https://uiwow.com/thread-21528-1-2.html)  
[leewheel](https://space.bilibili.com/1437832569)的探索服,需从[B站鱼人](https://space.bilibili.com/676535608)处下载.  
等等


## 流程
1. 仅考虑335的情况,服务端程序文件有```authserver.exe```和```worldserver.exe```,在```authserver.conf```配置文件中配置服务器相关环境,如监听地址,数据库地址等等.在```worldserver.conf```配置文件中配置游戏内相关内容,经验,战场,以及MOD机器人相关的配置全在该文件中.

2. 往往下载别人编译好的服务端是自带数据库的,如果有直接使用,如果没有就需要手动导入数据库文件.  
从AZ核心文件夹中的```data/sql/create/```找到```create_mysql.sql```,使用heidiSql执行该文件后,本地数据库中会新增3个库,```acore_world```,```acore_characters```,```acore_auth```.  
确保服务端文件夹下有Data数据的,如果没有,等编译攻略.  
启动```authserver.exe```和```worldserver.exe```,数据库将自动创建表和内容.

    

3. 修改```acore_auth```库中的```realmlist```表,修改```address```字段为服务器的外网地址,```name```字段可选修改.

4. 编辑Caddyfile:  
```
:8999 {
  root * C:/www
  php_fastcgi 127.0.0.1:9000
  file_server
}
```
克隆[WoWSimpleRegistration](https://github.com/masterking3WoWSimpleRegistration)库至C盘www目录下,并且编辑```application/config/confiphp.sample```,去掉```.sample```,根据实际情况配置该文件内容  
运行Caddy,终端使用命令```caddy.exe run --config path/Caddyfile```  

5. 编辑PHP.ini,启用插件:  
```
extension=gd2
extension=gmp
extension=mbstring
extension=openssl
extension=pdo_mysql
extension=pdo_odbc
extension=pdo_pgsql
extension=pdo_sqlite
extension=soap
```
运行PHP,终端使用命令```php-cgi.exe -b 127.0.0.1:9000 -c php.ini```

6. 客户端修改```Config.wtf```文件,添加内容```SET realmList "服务器IP地址"```,请根据实际情况修改,默认端口不需要添加端口,如果在```authserver.exe```修改过监听端口,客户端需要对应更改,如果```Config.wtf```文件不存在,可以手动创建,该文件一般位置在```WTF```下.

7. 访问```http://服务器IP地址:8999```注册帐号,进入游戏,试玩游戏,没有问题就可以将客户端打包传给小伙伴玩了!