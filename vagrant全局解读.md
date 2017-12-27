Title: vagrant全局解读
Date: 2017-12-26 13:45
Category: 其他
>利用vagrant 进行本地实验是一个非常好的选择

## vagrant box

vagrant box 就是系统镜像，可以通过命令行进行安装

```shell
vagrant box add USER/BOX
```
这是从网上的box 云 下载安装，有时候会很慢，可以事先下载到本地再进行安装

这是一个下载链接例子：
https://atlas.hashicorp.com/laravel/boxes/homestead/versions/0.2.6/providers/virtualbox.box

找到你想下载的box页面地址点击版本后的链接上加上/providers/virtualbox.box就是下载链接了。

https://atlas.hashicorp.com/bento/boxes/centos-6.7/versions/2.2.7/providers/virtualbox.box

## 多虚拟机配置

```ruby
Vagrant.configure("2") do |config|
  config.vm.provision "shell", inline: "echo Hello"
  config.vm.provision "shell", path: "scripts/install.sh"

  config.vm.define "web" do |web|
    web.vm.box = "centos"
  end

  config.vm.define "db" do |db|
    db.vm.box = "centos"
    db.vm.network "public_network"
    db.vm.provider "virtualbox" do |v|
      v.name = "db_master"
      v.memory = "1024"
      v.cpus = 2
    end
  end
end
```

## 安装脚本

```shell
#安装必要依赖
yum install -y gcc
yum install -y vim
yum install -y wget
#安装几个源
rpm -Uvh http://mirror.webtatic.com/yum/el6/latest.rpm
wget http://www.atomicorp.com/installers/atomic
chmod +x atomic
./atomic
su -c 'rpm -Uvh http://dl.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm'   #64位

##设置中文
echo 'export LANG="zh_CN.UTF-8"' >> ~/.bashrc
```