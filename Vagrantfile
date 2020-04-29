# -*- mode: ruby -*-
# vi: set ft=ruby :

Vagrant.configure("2") do |config|

  if Vagrant.has_plugin?("vagrant-vbguest")
    # set auto_update to false, if you do NOT want to check the aorrect
    config.vbguest.auto_update = true
  end

  config.vm.box = "centos/7"

  config.vm.network "forwarded_port", guest: 21, host: 2001, id:"ftp"
  config.vm.network "forwarded_port", guest: 22, host: 2222, id:"ssh"
  config.vm.network "forwarded_port", guest: 80, host: 8080, id:"http"

  config.vm.network "private_network", ip: "192.168.33.10"

  config.vm.synced_folder "./www", "/var/www/html", owner: "apache", group: "apache", create: "true"
  
  config.vm.provision "shell", inline: <<-SHELL
    yum update
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
  SHELL
end
