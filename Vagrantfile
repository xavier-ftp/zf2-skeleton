# -*- mode: ruby -*-
# vi: set ft=ruby :

VAGRANTFILE_API_VERSION = '2'

@script = <<SCRIPT
set -ex

echo "Installing Zend"
wget http://repos.zend.com/zend.key -O- | apt-key add -
echo "deb http://repos.zend.com/zend-server/9.0.0/deb_apache2.4 server non-free" > /etc/apt/sources.list.d/zend.list

apt-get update
apt-get upgrade -y
apt-get install -y zend-server-php-7.0 curl

echo "PATH=$PATH:/usr/local/zend/bin" >> /etc/profile.d/zend
echo "LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/zend/lib" >> /etc/profile.d/zend
source /etc/profile


echo "Installing composer and running composer install"
curl -Ss https://getcomposer.org/installer | php installer --install-dir=/usr/local/bin --filename=composer

cd /var/www/zf
if [ -f composer.json ] ; 
then
  composer install
fi


echo "Configuring vhost"

DOCUMENT_ROOT_ZEND="/var/www/zf/public"
echo "
<VirtualHost *:80>
    ServerName skeleton-zf.local
    DocumentRoot $DOCUMENT_ROOT_ZEND
    <Directory $DOCUMENT_ROOT_ZEND>
        DirectoryIndex index.php
        AllowOverride All
        Order allow,deny
        Allow from all
    </Directory>
</VirtualHost>
" > /etc/apache2/sites-available/skeleton-zf.conf
a2enmod rewrite
a2dissite 000-default
a2ensite skeleton-zf
service apache2 restart

echo "** [ZEND] Visit http://localhost:8085 in your browser for to view the application **"

SCRIPT

Vagrant.configure(VAGRANTFILE_API_VERSION) do |config|
  config.vm.box = 'ubuntu/trusty64'
  config.vm.network "forwarded_port", guest: 80, host: 8085
  config.vm.hostname = "skeleton-zf.local"
  config.vm.synced_folder '.', '/var/www/zf'
  config.vm.provision 'shell', inline: @script

  config.vm.provider "virtualbox" do |vb|
    vb.customize ["modifyvm", :id, "--memory", "1024"]
  end

end
