# JARKOM Soal Shift Modul 2

## Daftar isi

- [Anggota Kelompok](#anggota-kelompok)
- [Nomer 1](#nomer-1)
- [Nomer 2](#nomer-2)
- [Nomer 3](#nomer-3)
- [Nomer 4](#nomer-4)
- [Nomer 5](#nomer-5)
- [Nomer 6](#nomer-6)
- [Nomer 7](#nomer-7)
- [Nomer 8](#nomer-8)
- [Nomer 9](#nomer-9)
- [Nomer 10](#nomer-10)
- [Nomer 11](#nomer-11)
- [Nomer 12](#nomer-12)
- [Nomer 13](#nomer-13)
- [Nomer 14](#nomer-14)
- [Nomer 15](#nomer-15)
- [Nomer 16](#nomer-16)
- [Nomer 17](#nomer-17)
- [Kendala](#kendala)

## Anggota Kelompok F07

| NRP        | NAMA                       |
| ---------- | -------------------------- |
| 5025201145 | Mochamad Revanza Kurniawan |
| 5025201208 | Bagus Febrian Dali Hidayat |
| 5025201241 | Jabalnur                   |

## Nomer 1
- DNS master : WISE
- DNS Slave : Berlint
- Web server : Eden
- Client : SSS & Garden

Semua node terhubung pada router Ostania, sehingga dapat mengakses internet
<img width="606" alt="image" src="https://user-images.githubusercontent.com/73029778/198825654-3554d22a-1a65-4475-a619-3610fe3ad222.png">

> Konfigurasi Ostania
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 10.32.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 10.32.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 10.32.3.1
	netmask 255.255.255.0
```

> Konfigurasi WISE
```
auto eth0
iface eth0 inet static
	address 10.32.2.2
	netmask 255.255.255.0
	gateway 10.32.2.1
```

> Konfigurasi SSS
```
auto eth0
iface eth0 inet static
	address 10.32.1.2
	netmask 255.255.255.0
	gateway 10.32.1.1
```

> Konfigurasi Garden
```
auto eth0
iface eth0 inet static
	address 10.32.1.3
	netmask 255.255.255.0
	gateway 10.32.1.1
```

> Konfigurasi Berlint
```
auto eth0
iface eth0 inet static
	address 10.32.3.2
	netmask 255.255.255.0
	gateway 10.32.3.1
```

> Konfigurasi Eden
```
auto eth0
iface eth0 inet static
	address 10.32.3.3
	netmask 255.255.255.0
	gateway 10.32.3.1
```

> Dalam terminal Ostania
Masukkan command berikut. Prefix untuk kelompok kita adalah 10.32
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.32.0.0/16
apt-get update
```
command tersebut bisa dimasukkan ke dalam .bashrc

> Dalam terminal WISE, SSS, Garden, Berlint, dan Eden
Masukkan command berikut
```
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt-get update
```

Selanjutnya bisa dicoba `ping google.com` di setiap nodenya untuk mengetes apakah sudah terkoneksi ke internet

## Nomer 2
Untuk mempermudah mendapatkan informasi mengenai misi dari Handler, bantulah Loid membuat website utama dengan akses wise.yyy.com dengan alias www.wise.yyy.com pada folder wise

> Dalam terminal WISE
Masukkan command berikut
```
#install bind
apt-get install bind9 -y
#buat zone
echo '
zone "wise.F07.com" {
    type master;
    file "/etc/bind/wise/wise.F07.com";
}; 
' > /etc/bind/named.conf.local
#buat folder wise
mkdir /etc/bind/wise
#copy db.local
cp /etc/bind/db.local /etc/bind/wise/wise.F07.com
#config wise.F07.com
echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.F07.com. root.wise.F07.com. (
		2022102401         ; Serial
		    604800         ; Refresh
		    86400         ; Retry
		    2419200         ; Expire
		    604800 )       ; Negative Cache TTL
;
@       IN      NS      wise.F07.com.
@       IN      A       10.32.2.2       ; IP Wise
www     IN      CNAME   wise.F07.com.   ; alias
@       IN      AAAA    ::1
' > /etc/bind/wise/wise.F07.com
#restart bind
service bind9 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
#ubah nameserver ke IP WISE
echo '
nameserver 10.32.2.2 #IP WISE
' > /etc/resolv.conf
#ping ke wise.F07.com
ping wise.F07.com -c 3
```

## Nomer 3
Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden

> Dalam terminal WISE
```
#tambah subdomain pada config wise.F07.com
echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@               IN      SOA     wise.F07.com. root.wise.F07.com. (
		2022102401         ; Serial
		    604800         ; Refresh
		    86400         ; Retry
		    2419200         ; Expire
		    604800 )       ; Negative Cache TTL
;
@               IN      NS      wise.F07.com.
@               IN      A       10.32.2.2       ; IP Wise
www             IN      CNAME   wise.F07.com.   ; alias wise
eden            IN      A       10.32.3.3       ; subdomain IP Eden
www.eden        IN      CNAME   eden            ; alias eden
@               IN      AAAA    ::1
' > /etc/bind/wise/wise.F07.com
#restart bind
service bind9 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
#ping ke wise
ping wise.F07.com -c 3
#ping ke alias wise
ping www.wise.F07.com -c 3
#ping subdomain eden
ping eden.wise.F07.com -c 3
#ping ke alias subdomain eden
ping www.eden.wise.F07.com -c 3
```

## Nomer 4
Buat juga reverse domain untuk domain utama

> Dalam terminal WISE
```
#membuat zone reverse domain
echo '
zone "wise.F07.com" {
    type master;
    file "/etc/bind/wise/wise.F07.com";
};

zone "2.32.10.in-addr.arpa" {
    type master;
    file "/etc/bind/wise/2.32.10.in-addr.arpa";
};
' > /etc/bind/named.conf.local
#copy db.local ke 2.32.10.in-addr.arpa
cp /etc/bind/db.local /etc/bind/wise/2.32.10.in-addr.arpa
#config .arpa
echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     wise.F07.com. root.wise.F07.com. (
		2022102402         ; Serial
		    604800         ; Refresh
		    86400         ; Retry
		    2419200         ; Expire
		    604800 )       ; Negative Cache TTL
;
2.32.10.in-addr.arpa.   IN      NS      wise.F07.com.
2                       IN      PTR     wise.F07.com. ; Byte ke-4 WISE
' > /etc/bind/wise/2.32.10.in-addr.arpa
#restart bind
service bind9 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
#kembalikan nameserver ke 192.168.122.1
echo '
nameserver 192.168.122.1
' > /etc/resolv.conf
#update lalu install dnsutils
apt-get update
apt-get install dnsutils -y
#kembalikan nameserver ke WISE
echo '
nameserver 10.32.2.2 #IP WISE
' > /etc/resolv.conf
#cek host
host -t PTR 10.32.2.2
```

## Nomer 5
Agar dapat tetap dihubungi jika server WISE bermasalah, buatlah juga Berlint sebagai DNS Slave untuk domain utama

> Dalam terminal WISE
```
#mengubah zone untuk slave
echo '
zone "wise.F07.com" {
    type master;
    notify yes;
    also-notify { 10.32.3.2; };
    allow-transfer { 10.32.3.2; };
    file "/etc/bind/wise/wise.F07.com";
};

zone "2.32.10.in-addr.arpa" {
    type master;
    file "/etc/bind/wise/2.32.10.in-addr.arpa";
};
' > /etc/bind/named.conf.local
#restart bind
service bind9 restart
```

> Dalam terminal Berlint
```
#update lalu install bind
apt-get update
apt-get install bind9 -y
#buat zone
echo '
zone "wise.F07.com" {
    type slave;
    masters { 10.32.2.2; }; #IP WISE
    file "/var/lib/bind/wise.F07.com";
};
' > /etc/bind/named.conf.local
#restart bind
service bind9 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
#tambah nameserver ke IP Berlint
echo '
nameserver 10.32.2.2 #IP WISE
nameserver 10.32.3.2 #IP Berlint
' > /etc/resolv.conf
#ping ke wise.F07.com
ping wise.F07.com -c 3
```

## Nomer 6
Karena banyak informasi dari Handler, buatlah subdomain yang khusus untuk operation yaitu operation.wise.yyy.com dengan alias www.operation.wise.yyy.com yang didelegasikan dari WISE ke Berlint dengan IP menuju ke Eden dalam folder operation

> Dalam terminal WISE
```
IN      SOA     wise.F07.com. root.wise.F07.com. (
		2022102401         ; Serial
		    604800         ; Refresh
		    86400         ; Retry
		    2419200         ; Expire
		    604800 )       ; Negative Cache TTL
;
@                       IN      NS      wise.F07.com.
@                       IN      A       10.32.3.3       ; IP Eden
www                     IN      CNAME   wise.F07.com.   ; alias wise
eden                    IN      A       10.32.3.3       ; subdomain IP Eden
www.eden                IN      CNAME   eden            ; alias eden
ns1                     IN      A       10.32.3.2       ; IP Berlint
operation               IN      NS      ns1
www.operation           IN      CNAME   operation
@                       IN      AAAA    ::1
' > /etc/bind/wise/wise.F07.com
#edit /etc/bind/named.conf.options
echo '
options {
    directory "/var/cache/bind";

    // forwarders {
    //      0.0.0.0;
    // };

    //dnssec-validation auto;

    allow-query{any;};

    auth-nxdomain no;    # conform to RFC1035
    listen-on-v6 { any; };
};
' > /etc/bind/named.conf.options
#restart bind
service bind9 restart
```

> Dalam terminal Berlint
```
#edit /etc/bind/named.conf.options
echo '
options {
    directory "/var/cache/bind";

    // forwarders {
    //      0.0.0.0;
    // };

    //dnssec-validation auto;

    allow-query{any;};

    auth-nxdomain no;    # conform to RFC1035
    listen-on-v6 { any; };
};
' > /etc/bind/named.conf.options
#tambah zone delegasi
echo '
zone "operation.wise.F07.com" {
    type master;
    file "/etc/bind/operation/operation.wise.F07.com";
};

zone "wise.F07.com" {
    type slave;
    masters { 10.32.2.2; }; #IP WISE
    file "/var/lib/bind/wise.F07.com";
};
' > /etc/bind/named.conf.local
#buat direktori delegasi
mkdir /etc/bind/operation
#copy db.local ke operation.wise.F07.com
cp /etc/bind/db.local /etc/bind/operation/operation.wise.F07.com
#config operation.wise.F07.com
echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@               IN      SOA     operation.wise.F07.com. root.operation.wise.F07.com. (
		2022102403         ; Serial
		    604800         ; Refresh
		    86400         ; Retry
		    2419200         ; Expire
		    604800 )       ; Negative Cache TTL
;
@               IN      NS      operation.wise.F07.com.
@               IN      A       10.32.3.3       ; IP Eden
www             IN      CNAME   operation.wise.F07.com.
' > /etc/bind/operation/operation.wise.F07.com
#restart bind
service bind9 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
    #ping ke wise
    ping wise.F07.com -c 3
    #ping ke operation
    ping operation.wise.F07.com -c 3
    #ping ke alias operation
    ping www.operation.wise.F07.com -c 3
```

## Nomer 7
Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.yyy.com dengan alias www.strix.operation.wise.yyy.com yang mengarah ke Eden 

> Dalam terminal Berlint
```
#tambah delegasi subdomain pada config wise.F07.com
echo '
;
; BIND data file for local loopback interface
;
$TTL    604800
@               IN      SOA     wise.F07.com. root.wise.F07.com. (
		2022102401         ; Serial
		    604800         ; Refresh
		    86400         ; Retry
		    2419200         ; Expire
		    604800 )       ; Negative Cache TTL
;
@                       IN      NS      wise.F07.com.
@                       IN      A       10.32.3.3       ; IP Eden
www                     IN      CNAME   wise.F07.com.   ; alias wise
eden                    IN      A       10.32.3.3       ; subdomain IP Eden
www.eden                IN      CNAME   eden            ; alias eden
ns1                     IN      A       10.32.3.2       ; IP Berlint
operation               IN      NS      ns1
www.operation           IN      CNAME   operation
www.strix.operation     IN      CNAME   operation
@                       IN      AAAA    ::1
' > /etc/bind/wise/wise.F07.com
#restart bind
service bind9 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
#ping ke subdomain strix pada operation
ping strix.operation.wise.F07.com -c 3
#ping ke alias subdomain strix pada operation
ping www.strix.operation.wise.F07.com -c 3
```

## Nomer 8
Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.wise.yyy.com. Pertama, Loid membutuhkan webserver dengan DocumentRoot pada /var/www/wise.yyy.com

> Dalam terminal Eden
```
#kembalikan nameserver ke 192.168.122.1
echo '
nameserver 192.168.122.1
' > /etc/resolv.conf
#update lalu install dnsutils
apt-get update
apt-get install apache2 -y
apt-get install php -y
apt-get install libapache2-mod-php7.0 -y
apt-get install unzip -y
apt-get install wget -y
#kembalikan nameserver ke WISE
echo '
nameserver 10.32.2.2 #IP WISE
' > /etc/resolv.conf
#cek versi php
php -v
#copy default ke wise.F07.com
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/wise.F07.com.conf
#ubah document root
echo '
<VirtualHost *:80>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/wise.F07.com
    ServerName wise.F07.com
    ServerAlias www.wise.F07.com    

    #LogLevel info ssl:warn

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
' > /etc/apache2/sites-available/wise.F07.com.conf
#aktifkan konfigurasi wise.F07.com
a2ensite wise.F07.com.conf
#membuat directory document root
mkdir /var/www/wise.F07.com
#download resource
wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1S0XhL9ViYN7TyCj2W66BNEXQD2AAAw2e' -O wise.zip
unzip wise.zip
mv wise/* /var/www/wise.F07.com

#restart apache
service apache2 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
#kembalikan nameserver ke 192.168.122.1
echo '
nameserver 192.168.122.1
' > /etc/resolv.conf
#update lalu install dnsutils
apt-get update
apt-get install lynx -y
#kembalikan nameserver ke WISE
echo '
nameserver 10.32.2.2 #IP WISE
nameserver 10.32.3.2 #IP Berlint
' > /etc/resolv.conf
#buka eden di lynx
lynx http://www.wise.F07.com
```

## Nomer 9
Setelah itu, Loid juga membutuhkan agar url www.wise.yyy.com/index.php/home dapat menjadi menjadi www.wise.yyy.com/home

> Dalam terminal Eden
```
a2enmod rewrite
service apache2 restart
echo "
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule (.*) /index.php/\$1 [L]
" >/var/www/wise.F07.com/.htaccess
echo '
<VirtualHost *:80>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/wise.F07.com
    ServerName wise.F07.com
    ServerAlias www.wise.F07.com    

    Alias "/home" "/var/www/wise.F07.com/index.php/home"
    #LogLevel info ssl:warn

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
' > /etc/apache2/sites-available/wise.F07.com.conf

service apache2 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
lynx www.wise.F07.com/home
```

## Nomer 10
Setelah itu, pada subdomain www.eden.wise.yyy.com, Loid membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/eden.wise.yyy.com

> Dalam terminal Eden
```
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/eden.wise.F07.com.conf
#ubah document root
echo '
<VirtualHost *:80>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/eden.wise.F07.com
    ServerName eden.wise.F07.com
    ServerAlias www.eden.wise.F07.com       

    #LogLevel info ssl:warn

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
' > /etc/apache2/sites-available/eden.wise.F07.com.conf
#aktifkan konfigurasi eden.wise.F07.com
a2ensite eden.wise.F07.com.conf
#membuat directory document root
mkdir /var/www/eden.wise.F07.com
#download resource
wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1q9g6nM85bW5T9f5yoyXtDqonUKKCHOTV' -O eden.wise.zip
unzip eden.wise.zip
mv eden.wise/* /var/www/eden.wise.F07.com

#restart apache
service apache2 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
lynx eden.wise.F07.com
```

## Nomer 11
Akan tetapi, pada folder /public, Loid ingin hanya dapat melakukan directory listing saja

> Dalam terminal Eden
```
#ubah document root
echo '
<VirtualHost *:80>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/eden.wise.F07.com
    ServerName eden.wise.F07.com
    ServerAlias www.eden.wise.F07.com       

    <Directory /var/www/eden.wise.F07.com>
	    Options +Indexes
    </Directory>
    <Directory /var/www/eden.wise.F07.com/public>
	    Options +Indexes
    </Directory>

    #LogLevel info ssl:warn

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
' > /etc/apache2/sites-available/eden.wise.F07.com.conf

service apache2 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
lynx eden.wise.F07.com/public
```

## Nomer 12
Tidak hanya itu, Loid juga ingin menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache

> Dalam terminal Eden
```
#ubah document root
echo '
<VirtualHost *:80>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/eden.wise.F07.com
    ServerName eden.wise.F07.com
    ServerAlias www.eden.wise.F07.com       

    <Directory /var/www/eden.wise.F07.com>
	    Options +Indexes
    </Directory>
    <Directory /var/www/eden.wise.F07.com/public>
	    Options +Indexes
    </Directory>

    ErrorDocument 404 /error/404.html

    #LogLevel info ssl:warn

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
' > /etc/apache2/sites-available/eden.wise.F07.com.conf

service apache2 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
lynx eden.wise.F07.com/public/mengcapek
```

## Nomer 13
Loid juga meminta Franky untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.eden.wise.yyy.com/public/js menjadi www.eden.wise.yyy.com/js 

> Dalam terminal Eden
```
#ubah document root
echo '
<VirtualHost *:80>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/eden.wise.F07.com
    ServerName eden.wise.F07.com
    ServerAlias www.eden.wise.F07.com       

    <Directory /var/www/eden.wise.F07.com>
	    Options +Indexes
    </Directory>
    <Directory /var/www/eden.wise.F07.com/public>
	    Options +Indexes
    </Directory>

    ErrorDocument 404 /error/404.html

    Alias "/js" "/var/www/eden.wise.F07.com/public/js"

    #LogLevel info ssl:warn

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
' > /etc/apache2/sites-available/eden.wise.F07.com.conf

service apache2 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
lynx eden.wise.F07.com/js
```

## Nomer 14
Loid meminta agar www.strix.operation.wise.yyy.com hanya bisa diakses dengan port 15000 dan port 15500

> Dalam terminal Eden
```
cp /etc/apache2/sites-available/000-default.conf /etc/apache2/sites-available/strix.operation.wise.F07.com.conf
#ubah document root
echo '
<VirtualHost *:15000 *:15500>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/strix.operation.wise.F07.com
    ServerName strix.operation.wise.F07.com
    ServerAlias www.strix.operation.wise.F07.com    

    <Directory /var/www/strix.operation.wise.F07.com>
	    Options +Indexes
    </Directory>

    #LogLevel info ssl:warn

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
' > /etc/apache2/sites-available/strix.operation.wise.F07.com.conf
#aktifkan konfigurasi strix.operation.wise.F07.com
a2ensite strix.operation.wise.F07.com.conf
#membuat directory document root
mkdir /var/www/strix.operation.wise.F07.com
#download resource
wget --no-check-certificate 'https://docs.google.com/uc?export=download&id=1bgd3B6VtDtVv2ouqyM8wLyZGzK5C9maT' -O strix.operation.wise.zip
unzip strix.operation.wise.zip
mv strix.operation.wise/* /var/www/strix.operation.wise.F07.com
#add ports
echo '
Listen 80
Listen 15000
Listen 15500
<IfModule ssl_module>
    Listen 443
</IfModule>

<IfModule mod_gnutls.c>
    Listen 443
</IfModule>
' > /etc/apache2/ports.conf
#restart apache
service apache2 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
lynx www.strix.operation.wise.F07.com:15000
```

## Nomer 15
dengan autentikasi username Twilight dan password opStrix dan file di /var/www/strix.operation.wise.yyy

> Dalam terminal Eden
```
#set username dan password
htpasswd -c -b /etc/apache2/.htpasswd Twilight opStrix
#update virtual host
echo '
<VirtualHost *:15000 *:15500>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/strix.operation.wise.F07.com
    ServerName strix.operation.wise.F07.com
    ServerAlias www.strix.operation.wise.F07.com    

    <Directory /var/www/strix.operation.wise.F07.com>
	    AuthType Basic
	    AuthName "Restricted Content"
	    AuthUserFile /etc/apache2/.htpasswd
	    Require valid-user
    </Directory>

    #LogLevel info ssl:warn

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
' > /etc/apache2/sites-available/strix.operation.wise.F07.com.conf
#restart apache2
service apache2 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
lynx www.strix.operation.wise.F07.com:15000
```

## Nomer 16
dan setiap kali mengakses IP Eden akan dialihkan secara otomatis ke www.wise.yyy.com

> Dalam terminal Eden
```
#update virtual host
echo '
<VirtualHost *:80>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/wise.F07.com

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

</VirtualHost>
' > /etc/apache2/sites-available/000-default.conf
#update htaccess pada wise
a2enmod rewrite
echo '
RewriteEngine On
RewriteBase /
RewriteCond %{HTTP_HOST} ^10\.32\.3\.3$
RewriteRule ^(.*)$ http://www.wise.F07.com/$1 [L,R=301]
' > /var/www/wise.F07.com/.htaccess
#restart apache
service apache2 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
lynx 10.32.3.3
```

## Nomer 17
arena website www.eden.wise.yyy.com semakin banyak pengunjung dan banyak modifikasi sehingga banyak gambar-gambar yang random, maka Loid ingin mengubah request gambar yang memiliki substring “eden” akan diarahkan menuju eden.png. Bantulah Agent Twilight dan Organisasi WISE menjaga perdamaian! 

> Dalam terminal Eden
```
#update htaccess eden
a2enmod rewrite
echo '
RewriteEngine On
RewriteBase /
RewriteCond %{REQUEST_URI} !\beden.png\b
RewriteRule eden http://eden.wise.F07.com/public/images/eden.png$1 [L,R=301]
' > /var/www/eden.wise.F07.com/.htaccess
#update virtual host eden
echo '
<VirtualHost *:80>

    ServerAdmin webmaster@localhost
    DocumentRoot /var/www/eden.wise.F07.com
    ServerName eden.wise.F07.com
    ServerAlias www.eden.wise.F07.com

    <Directory /var/www/eden.wise.F07.com/public>
	    Options +Indexes
    </Directory>

    Alias "/js" "/var/www/eden.wise.F07.com/public/js"

    <Directory /var/www/eden.wise.F07.com>
	    Options +FollowSymLinks -Multiviews
	    AllowOverride All
    </Directory>

    ErrorDocument 404 /error/404.html

    #LogLevel info ssl:warn

    ErrorLog ${APACHE_LOG_DIR}/error.log
    CustomLog ${APACHE_LOG_DIR}/access.log combined

    #Include conf-available/serve-cgi-bin.conf
</VirtualHost>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
' > /etc/apache2/sites-available/eden.wise.F07.com.conf
#Restart Apache
service apache2 restart
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
#testing
lynx eden.wise.F07.com/public/images/edenedenee.png
```

## Kendala
s u s a h
