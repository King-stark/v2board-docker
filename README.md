# v2board-docker
🐳fast running v2board
* 项目
https://github.com/v2board/v2board-docker.git

### 准备好`Docker`和`Docker-Compose`
```
#这里采用一键脚本快速部署docker环境，如需跟随linux存储库管理docker，请参考官方文档
curl -sSL https://get.docker.com/ | sh 
systemctl start docker 
systemctl enable docker
#安装docker-compose插件
curl -L https://github.com/docker/compose/releases/download/v2.10.2/docker-compose-`uname -s`-`uname -m` -o /usr/local/bin/docker-compose && sudo chmod +x /usr/local/bin/docker-compose
```
### 拉取稳定代码
```
git clone https://github.com/v2board/v2board-docker.git
cd v2board-docker/
git submodule update --init
echo '  branch = master' >> .gitmodules
git submodule update --remote
```
### 修改docker-compose.yml等文件
```
#官方库内文件长期没有更新，修改一下内容
#将supervisord.conf文件内
command=php /www/artisan queue:work --queue=send_email
修改为
command=php /www/artisan horizon
#caddy.conf中localhost可修改为域名访问80/443，或者保持不变，让宿主机监听80/443的web服务反代到容器内部
```

### 启动环境

```
# 如需更改数据库名和密码，在docker-compose.yml文件内修改保存后再运行以下命令
docker-compose up -d
```

### 安装V2Board
```
docker-compose exec www bash
wget https://getcomposer.org/installer -O composer.phar
#如果需要换源，请将composer.json 中repositories url改成https://mirrors.aliyun.com/composer/
php composer.phar
php composer.phar install
php artisan v2board:install

# 数据库名和密码与docker-compose.yml内保持一致
数据库地址： mysql
数据库名：v2board
数据库用户名：root
数据库密码：v2boardisbest

#重启服务
chmod -R 755 ${PWD}
exit
docker-compose restart

```
### 升级V2Board
```
# 在v2board-docker文件夹目录下执行

git submodule update --remote
docker-compose exec www bash
rm -rf composer.lock composer.phar
wget https://getcomposer.org/installer -O composer.phar
php composer.phar
php composer.phar update
php artisan v2board:update
php artisan config:cache
```
