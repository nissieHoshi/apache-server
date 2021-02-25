# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  if Vagrant.has_plugin?("vagrant-vbguest")
    # set auto_update to false, if you do NOT want to check the aorrect
    config.vbguest.auto_update = true
  end

  config.vm.box = "centos/7"

  config.vm.provider "virtualbox" do |vm|
    # メモリを2048MBに設定
    vm.customize ["modifyvm", :id, "--memory", "2048"]
  end
  
  config.vm.network "forwarded_port", guest: 22, host: 2222, id:"ssh"
  config.vm.network "forwarded_port", guest: 80, host: 8080, id:"http"

  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.synced_folder "./www", "/var/www/html", create: "true"
  
  config.vm.provision "shell", inline: <<-SHELL
    yum update -y
    timedatectl set-timezone Asia/Tokyo
    yum install -y httpd httpd-tools httpd-devel httpd-manual
    systemctl enable httpd
    yum install -y epel-release
    rpm -Uvh http://rpms.famillecollet.com/enterprise/remi-release-7.rpm
    yum install -y --enablerepo=remi,remi-php73 php php-mbstring php-xml php-xmlrpc php-gd php-pdo php-pecl-mcrypt php-mysqlnd php-pecl-mysql
    yum -y remove mariadb-libs
    curl -sS https://downloads.mariadb.com/MariaDB/mariadb_repo_setup | sudo bash
    yum -y install MariaDB-server
    systemctl enable mariadb.service
    systemctl start mariadb.service
    systemctl enable firewalld
    systemctl start firewalld
    firewall-cmd --add-service=http --permanent
    systemctl reload firewalld
    systemctl start httpd
    printf "expose_php off\npost_max_size = 20M\nupload_max_filesize = 20M\ndate.timezone = "Asia/Tokyo"\nmbstring.language = Japanese\nmbstring.internal_encoding = UTF-8\nmbstring.http_input = UTF-8\nmbstring.http_output = pass\nmbstring.encoding_translation = On\nmbstring.detect_order = auto\nmbstring.substitute_character = none\nerror_reporting = E_ALL\ndisplay_error = On\n" >> /etc/php.ini
    systemctl reload httpd
    sed -i.bak s/\\[mariadb\\]/"[mariadb]\ncharacter-set-server=utf8mb4\ncollation-server=utf8mb4_unicode_ci"/ /etc/my.cnf.d/server.cnf
    systemctl restart mariadb
    yes | mysql_secure_installation
    sed -i.bak s/SELINUX=enforcing/SELINUX=permissive/ /etc/selinux/config
    setenforce 0
    touch /var/www/html/index.php && echo '<?php phpinfo() ?>' > /var/www/html/index.php
  SHELL
end
