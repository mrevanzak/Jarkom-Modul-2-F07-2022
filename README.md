# JARKOM Modul 2

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

## Anggota Kelompok

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
```

Untuk mengetest hasilnya
> Dalam terminal SSS & Garden
```
```

## Nomer 3
Setelah itu ia juga ingin membuat subdomain eden.wise.yyy.com dengan alias www.eden.wise.yyy.com yang diatur DNS-nya di WISE dan mengarah ke Eden

## Nomer 4
Buat juga reverse domain untuk domain utama

## Nomer 5
Agar dapat tetap dihubungi jika server WISE bermasalah, buatlah juga Berlint sebagai DNS Slave untuk domain utama

## Nomer 6
Karena banyak informasi dari Handler, buatlah subdomain yang khusus untuk operation yaitu operation.wise.yyy.com dengan alias www.operation.wise.yyy.com yang didelegasikan dari WISE ke Berlint dengan IP menuju ke Eden dalam folder operation

## Nomer 7
Untuk informasi yang lebih spesifik mengenai Operation Strix, buatlah subdomain melalui Berlint dengan akses strix.operation.wise.yyy.com dengan alias www.strix.operation.wise.yyy.com yang mengarah ke Eden 

## Nomer 8
Setelah melakukan konfigurasi server, maka dilakukan konfigurasi Webserver. Pertama dengan webserver www.wise.yyy.com. Pertama, Loid membutuhkan webserver dengan DocumentRoot pada /var/www/wise.yyy.com

## Nomer 9
Setelah itu, Loid juga membutuhkan agar url www.wise.yyy.com/index.php/home dapat menjadi menjadi www.wise.yyy.com/home

## Nomer 10
Setelah itu, pada subdomain www.eden.wise.yyy.com, Loid membutuhkan penyimpanan aset yang memiliki DocumentRoot pada /var/www/eden.wise.yyy.com

## Nomer 11
Akan tetapi, pada folder /public, Loid ingin hanya dapat melakukan directory listing saja

## Nomer 12
Tidak hanya itu, Loid juga ingin menyiapkan error file 404.html pada folder /error untuk mengganti error kode pada apache

## Nomer 13
Loid juga meminta Franky untuk dibuatkan konfigurasi virtual host. Virtual host ini bertujuan untuk dapat mengakses file asset www.eden.wise.yyy.com/public/js menjadi www.eden.wise.yyy.com/js 

## Nomer 14
Loid meminta agar www.strix.operation.wise.yyy.com hanya bisa diakses dengan port 15000 dan port 15500

## Nomer 15
dengan autentikasi username Twilight dan password opStrix dan file di /var/www/strix.operation.wise.yyy

## Nomer 16
dan setiap kali mengakses IP Eden akan dialihkan secara otomatis ke www.wise.yyy.com

## Nomer 17
arena website www.eden.wise.yyy.com semakin banyak pengunjung dan banyak modifikasi sehingga banyak gambar-gambar yang random, maka Loid ingin mengubah request gambar yang memiliki substring “eden” akan diarahkan menuju eden.png. Bantulah Agent Twilight dan Organisasi WISE menjaga perdamaian! 


## Kendala
Kebingungan dengan password untuk decrypt :D
