# INSTALLATION INSTRUCTIONS
## for Arch Linux amd64

### 0/ WIP! You are warned, this does only partially work!
------------

This is a very bare bone install instruction.
Only the steps from the CLI are included à la gist.

{!generic/globalVariables.md!}

{!generic/arch.md!}

```bash
# Update keyring, just in case
sudo pacman -Sy archlinux-keyring
sudo pacman -Syyu
git clone https://aur.archlinux.org/yay.git
cd yay
makepkg -si
sudo pacman -Syy neovim ntp
sudo pacman -Syy apache mysql expect php php-fpm redis php-gd php-redis 
yay -S php-pear php-gnupg
sudo mv `which vi` `which vi`-$(date +%Y%m%d)
sudo ln -s `which nvim` /usr/bin/vi
sudo vi /etc/httpd/conf/httpd.conf
#LoadModule unique_id_module modules/mod_unique_id.so

ProxyPassMatch ^/(.*\.php(/.*)?)$ unix:/run/php-fpm/php-fpm.sock|fcgi://localhost/srv/http/$1
LoadModule proxy_module modules/mod_proxy.so
LoadModule proxy_fcgi_module modules/mod_proxy_fcgi.so

/etc/httpd/conf/extra/php-fpm.conf
DirectoryIndex index.php index.html
<FilesMatch \.php$>
    SetHandler "proxy:unix:/run/php-fpm/php-fpm.sock|fcgi://localhost/"
</FilesMatch>

Include conf/extra/php-fpm.conf

sudo systemctl enable httpd.service

https://wiki.archlinux.org/index.php/PHP

ssl
LoadModule ssl_module modules/mod_ssl.so
LoadModule socache_shmcb_module modules/mod_socache_shmcb.so
Include conf/extra/httpd-ssl.conf
https://weakdh.org/sysadmin.html
# cd /etc/httpd/conf
# openssl req -new -x509 -nodes -newkey rsa:4096 -keyout server.key -out server.crt -days 1095
# chmod 400 server.key
# cd /etc/httpd/conf
# openssl genpkey -algorithm RSA -pkeyopt rsa_keygen_bits:4096 -out server.key
# chmod 400 server.key
# openssl req -new -sha256 -key server.key -out server.csr
# openssl x509 -req -days 1095 -in server.csr -signkey server.key -out server.crt

sudo gsed -i "s/#LoadModule rewrite_module libexec\/apache24\/mod_rewrite.so/LoadModule rewrite_module libexec\/apache24\/mod_rewrite.so/" /usr/local/etc/apache24/httpd.conf
sudo gsed -i "s/#LoadModule ssl_module libexec\/apache24\/mod_ssl.so/LoadModule ssl_module libexec\/apache24\/mod_ssl.so/" /usr/local/etc/apache24/httpd.conf
sudo gsed -i "s/Listen 80/Listen 80\nListen 443/" /usr/local/etc/apache24/httpd.conf
sudo systemctl restart httpd
sudo systemctl start mysqld
sudo mysql_secure_installation


sudo systemctl start ntpd
sudo systemctl enable ntpd
sudo systemctl enable NetworkManager
sudo systemctl start sshd
sudo systemctl enable sshd

sudo sed -i 's/# %wheel ALL=(ALL) NOPASSWD: ALL/%wheel ALL=(ALL:ALL) NOPASSWD: ALL/g' /etc/sudoers
sudo useradd -m -g users -G wheel -s /bin/bash misp
```

Maintain:

$ yay -Syu
$ yay -Sc
$ yay -Rsn $(pacman -Qdtq)
# pacman-key --populate archlinux

{!generic/hardening.md!}
