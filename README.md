# Jarkom-Modul-2-E20-2023

## Anggota
1. Ahmad Hafiz Mahardika (5025201196)
2. Moch. Taslam Gustino P. (5025211011)

# Lapres
## Soal 1
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi sesuai pembagian. 
## Jawaban
Topologi kami yaitu sebagai berikut
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/36770853-29a9-4cc1-8cb9-931edb83b63e)

Kemudian melakukan konfigurasi pada setiap node yang ada
### Pandudewanata sebagai router
```
iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 192.216.0.0/16
```
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
	address 192.216.1.1
	netmask 255.255.255.0

auto eth2
iface eth2 inet static
	address 192.216.2.1
	netmask 255.255.255.0

auto eth3
iface eth3 inet static
	address 192.216.3.1
	netmask 255.255.255.0
```
### Nakula dan Sadewa sebagai client
```
echo "nameserver 192.168.122.1" > /etc/resolv.conf  
apt-get update
apt-get install dnsutils -y
apt-get install lynx -y
echo '
nameserver 192.216.2.2
nameserver 192.216.2.3
' > /etc/resolv.conf
```
```
// Nakula
auto eth0
iface eth0 inet static
	address 192.216.1.2
	netmask 255.255.255.0
	gateway 192.216.1.1

// Sadewa
auto eth0
iface eth0 inet static
	address 192.216.1.3
	netmask 255.255.255.0
	gateway 192.216.1.1
```
### Yudhistira sebagai DNS master
```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
```
```
auto eth0
iface eth0 inet static
	address 192.216.2.2
	netmask 255.255.255.0
	gateway 192.216.2.1
```
### Werkudara sebagai DNS slave
```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install bind9 -y
```
```
auto eth0
iface eth0 inet static
	address 192.216.2.3
	netmask 255.255.255.0
	gateway 192.216.2.1
```
### Arjuna sebagai load balancer
```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt-get install nginx -y
```
```
auto eth0
iface eth0 inet static
	address 192.216.3.2
	netmask 255.255.255.0
	gateway 192.216.3.1
```
### Wisanggeni, Abimanyu, dan Prabukusuma sebagai worker
```
echo "nameserver 192.168.122.1" > /etc/resolv.conf
apt-get update
apt install nginx php php-fpm -y
```
```
// Wisanggeni
auto eth0
iface eth0 inet static
	address 192.216.3.3
	netmask 255.255.255.0
	gateway 192.216.3.1

// Abimanyu
auto eth0
iface eth0 inet static
	address 192.216.3.4
	netmask 255.255.255.0
	gateway 192.216.3.1

// Prabukusuma
auto eth0
iface eth0 inet static
	address 192.216.3.5
	netmask 255.255.255.0
	gateway 192.216.3.1
```
## Soal 2
Buatlah website utama pada node arjuna dengan akses ke `arjuna.yyy.com` dengan alias `www.arjuna.yyy.com` dengan yyy merupakan kode kelompok.
## Jawaban
Pada Node **Yudhistira** melakukan konfigurasi pada file `/etc/bind/named.conf.local`.
```
zone "arjuna.E20.com" {
    type master;
    file "/etc/bind/jarkom/arjuna.E20.com";
};
```
Membuat directory `/etc/bind/jarkom` dan copy file `/etc/bind/db.local` menjadi `/etc/bind/jarkom/arjuna.E20.com`. Kemudian melakukan konfigurasi pada file `/etc/bind/jarkom/arjuna.E20.com`.
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/e24994f1-33f7-4947-b7d1-5b460ec57b7b)

Lakukan restart bind
```service bind9 restart```
## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/1735c022-8b7c-4c5a-97f9-931ac1bfbbf5)
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/7eb691e4-0174-4614-af61-aa7a32a5437c)
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/037eee59-fa48-4c6b-acac-2929f8831cda)

## Soal 3
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com
## Jawaban
Pada Node **Yudhistira** menambahkan konfigurasi pada file `/etc/bind/named.conf.local`.
```
zone "abimanyu.E20.com" {
    type master;
    allow-transfer { 192.216.2.3; };
    file "/etc/bind/jarkom/abimanyu.E20.com";
};
```
Copy file `/etc/bind/db.local` menjadi `/etc/bind/jarkom/abimanyu.E20.com`. Kemudian melakukan konfigurasi pada file `/etc/bind/jarkom/abimanyu.E20.com`.
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/2331546c-83a7-4086-9248-4af6542eba7f)

Lakukan restart bind
```service bind9 restart```
## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/98da8139-6c38-4b56-989d-21e118adf497)

## Soal 4
Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.
## Jawaban
Pada Node **Yudhistira** menambahkan konfigurasi pada file `/etc/bind/jarkom/abimanyu.E20.com` sehingga menjadi
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/55f03bae-a2c9-4295-82a2-1ae5631c6af1)

Lakukan restart bind
```service bind9 restart```
## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/fa8bd8d8-98a6-4548-b662-e6a67be8d708)

## Soal 5
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)
## Jawaban
Pada node **Yudhistira** menambahkan konfigurasi pada file /etc/bind/named.conf.local
```
zone "3.216.192.in-addr.arpa" {
    type master;
    allow-transfer { 192.216.2.3; };
    file "/etc/bind/jarkom/3.216.192.in-addr.arpa";
};
```
Copy file `/etc/bind/db.local` menjadi `/etc/bind/jarkom/3.216.192.in-addr.arpa`. Kemudian melakukan konfigurasi pada file `/etc/bind/jarkom/3.216.192.in-addr.arpa`.
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/c887be19-57fe-4346-a8fc-4b6907d87f06)

Lakukan restart bind
```service bind9 restart```
## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/7b0ca680-6bd1-42da-b348-dd0335929b30)

## Soal 6
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.
## Jawaban
Pada node **Yudhistira** menambahkan konfigurasi pada file `/etc/bind/named.conf.local`
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/1713599b-530a-4547-bf58-32ac1b6e6b87)

Lakukan restart bind
```service bind9 restart```
Pada node **Werkudara** melakukan konfigurasi pada file `/etc/bind/named.conf.local`.
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/7efb6d95-b01b-408d-bb9a-0e8ebd388465)

Lakukan restart bind
```service bind9 restart```
## Testing
Pada node **Yudhistira** matika service bind9
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/06f5076b-1d84-4403-b8ac-2504008d3acc)

Pada client lakukan ping pada abimanyu.E20.com karena yang diminta hanya abimanyu saja
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/e3482ea3-b630-4752-a9cc-13946fed3732)

## Soal 7
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.
## Jawaban
Pada Node **Yudhistira** menambahkan konfigurasi pada file `/etc/bind/jarkom/abimanyu.E20.com` sehingga menjadi
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/1c3ff702-fc72-4802-987c-9e32e8ee2f41)

Edit file `/etc/bind/named.conf.options` menjadi
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/024c69b8-fa5a-4994-8f40-9145c897a59a)

Lakukan restart bind
```service bind9 restart```

Pada node **Werkudara** melakukan konfigurasi pada file `/etc/bind/named.conf.options` menjadi
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/cec2b0fe-09cc-45cb-8969-2d2ec415ae15)

Edit file `/etc/bind/named.conf.local` menambahkan
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/bf3c7393-7bc0-41fe-92d7-8bf2da63e638)

Kemudian buat directory `/etc/bind/Baratayuda` dan Copy file `/etc/bind/db.local` menjadi `/etc/bind/Baratayuda/baratayuda.abimanyu.E20.com`. Kemudian melakukan konfigurasi pada file `/etc/bind/Baratayuda/baratayuda.abimanyu.E20.com`.
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/c8b1a8c6-49dc-4a29-ba40-909356f16e55)

Lakukan restart bind
```service bind9 restart```
## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/298e93da-c1d4-463d-8b73-c57d3e6dc43c)
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/0f4acefe-ea99-47a4-9f33-c3cb775fec0e)

## Soal 8
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.
## Jawaban
Pada node **Werkudara** melakukan konfigurasi pada file `/etc/bind/Baratayuda/baratayuda.abimanyu.E20.com` menjadi
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/a8fec028-8da5-42c4-8954-f6bc93c78d8c)

Lakukan restart bind
```service bind9 restart```
## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/ca9101cc-e619-4d0f-9914-53ee9ea337df)
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/a1b31b7b-aa83-4a29-a5c5-956f196462df)

## Soal 9
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.
## Jawaban
**Node Wisanggeni**
Buat directory `mkdir /var/www/jarkom` dan buat file `index.php` yang berisi
```
 <?php
 echo "Ini Wisanggeni Gan!";
 ?>
```
Membuat file `jarkom` pada directory `/etc/nginx/sites-available` dengan konfigurasi berikut
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/6b6a4021-9a9b-4315-aa85-9657e828b4bd)

lalu membuat symlink `ln -s /etc/nginx/sites-available/jarkom /etc/nginx/sites-enabled`, menghapus file `/etc/nginx/sites-enabled/default`, dan menjalankan `service php7.0-fpm start`
Lakukan restart nginx
```service nginx restart```
Untuk node **Abimanyu & Prabukusuma** melakukan hal yang sama namun berbeda pada isi dari file `/var/www/jarkom/index.php` diberikan perbedaan untuk memastikan pengacakan pada saat akses server karena algoritma Round Robin pada load balancer. Juga konfigurasi pada file `/etc/nginx/sites-available/jarkom` berbeda pada listen server nya sesuai dengan port di nomor 10

## Soal 10
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
    - Prabakusuma:8001
    - Abimanyu:8002
    - Wisanggeni:8003
## Jawaban
**Node Arjuna**
Buat file baru di direktori `/etc/nginx/sites-available` dengan nama `lb-jarkom` dengan konfigurasi
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/3b365f20-7474-4632-af74-00797f5f9ac2)

lalu membuat symlink `ln -s /etc/nginx/sites-available/lb-jarkom /etc/nginx/sites-enabled` dan menghapus file `/etc/nginx/sites-enabled/default`
Lakukan restart nginx
```service nginx restart```
## Testing
Jalankan perintah `lynx arjuna.E20.com` pada client dan akan muncul secara acak seperti berikut
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/0924a3ad-a11d-496f-8acf-e4c8dbced083)
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/9a3f8204-bacc-45d2-951e-79c8ae867f60)
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/93267604/e857921b-fb0d-41c5-ad20-b9f090709fd6)

## Soal 11
**Node Abimanyu**
Install apache2 pada node Abimanyu, kemudian membuat file configurasi www.abimanyu.e20.com dengan script :
```
echo '
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/abimanyu.E20
        ServerName abimanyu.e20.com
        ServerAlias www.abimanyu.e20.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
' > /etc/apache2/sites-available/abimanyu.E20.com.conf
```
Kemudian membuat directory file root dan copy isi file root.

```
mkdir /var/www/abimanyu.E20

cp /root/abimanyu.yyy.com/abimanyu.webp /var/www/abimanyu.E20
cp /root/abimanyu.yyy.com/home.html /var/www/abimanyu.E20
cp /root/abimanyu.yyy.com/index.php /var/www/abimanyu.E20
```
Enable site dan restart apache2 dengan script:
```
a2ensite abimanyu.E20.com.conf

apt-get install libapache2-mod-php7.0 -y

service apache2 restart
```

## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/95972129/557b17d5-8cb4-4b85-a4c7-ff9f3bd84055)


## Soal 12
Mengaktifkan module rewrite dan membuat file .htaccess pada folder root dari abimanyu.e20.com menggunakan script :
```
a2enmod rewrite
service apache2 restart

echo '
RewriteEngine On
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule (.*) /index.php/\$1 [NC,L]
' > /var/www/abimanyu.E20/.htaccess
```

Kemudian menambahkan 
```
<Directory /var/www/abimanyu.E20>
        Options +FollowSymLinks -Multiviews
	AllowOverride All
</Directory>
```

Lalu restart apache2

## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/95972129/0222349e-afdb-4667-b7db-354b103e4ba5)
Dengan menggunakan `lynx abimanyu.e20.com/home`, maka akan muncul page yang telah di atur.

## Soal 13

Membuat file configurasi www.parikesit.abimanyu.e20.com dengan script :
```
echo '
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.E20
        ServerName parikesit.abimanyu.e20.com
        ServerAlias www.parikesit.abimanyu.e20.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
' > /etc/apache2/sites-available/parikesit.abimanyu.E20.com.conf
```
Lalu buat directory pada documentroot dan mengcopy isi dari website tersebut dengan script :
```
mkdir /var/www/parikesit.abimanyu.E20

cp -r /root/parikesit.abimanyu.yyy.com/error /var/www/parikesit.abimanyu.E20
cp -r /root/parikesit.abimanyu.yyy.com/public /var/www/parikesit.abimanyu.E20

a2ensite parikesit.abimanyu.E20.com.conf

service apache2 restart
```

## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/95972129/491c2895-8841-418e-a119-07659c7d6c76)
Mengakses web server dengan `lynx parikesit.abimanyu.e20.com` maka akan muncul halaman tersebut.

## Soal 14
Membuat direktori /secret dan menulis html sederhana dengan script :
```
mkdir /var/www/parikesit.abimanyu.E20/secret
echo '
<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta http-equiv="X-UA-Compatible" content="IE=edge">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
</head>
<body>
        <h1>Ini Folder Rahasia!!!!!</h1>
</body>
' > /var/www/parikesit.abimanyu.E20/secret/secret.html
```
Kemudian edit file config parikesit.abimanyu.e20.com dengan script :
```
echo '
<VirtualHost *:80>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/parikesit.abimanyu.E20
        ServerName parikesit.abimanyu.e20.com
        ServerAlias www.parikesit.abimanyu.e20.com

        <Directory /var/www/parikesit.abimanyu.E20/public>
                Options +Indexes
        </Directory>

        <Directory /var/www/parikesit.abimanyu.E20/secret>
                Options -Indexes
        </Directory>

	ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
' > /etc/apache2/sites-available/parikesit.abimanyu.E20.com.conf
```
Kemudian restart apache2

## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/95972129/55267cb9-5db5-43c9-8a52-76738d0913f5)
Access parikesit.abimanyu.e20.com/secret

![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/95972129/f7d5cdb7-3a58-45c7-9756-c5fa3a8b9d75)
Access parikesit.abimanyu.e20.com/public

## Soal 15

Kustomisasi halaman error dapat dilakukan dengan menambahkan kode 
```
ErrorDocument 403 /error/403.html
ErrorDocument 404 /error/404.html
```
pada file config parikesit.abimanyu.e20.com. Kemudian restart apache2

## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/95972129/c4e7eb4a-99f5-4e72-b758-300fff830357)
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/95972129/9e8ef05f-8e3e-4279-957b-32c651ccd701)

## Soal 16

Konfigurasi virtual host pada www.parikesit.abimanyu.yyy.com/public/js menjadi www.parikesit.abimanyu.yyy.com/js yaitu menambahkan
```
Alias "/js" "/var/www/parikesit.abimanyu.E20/public/js"
```
Pada file config dari parikesit.abimanyu.e20.com

## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/95972129/82b2e7a5-10d4-4d18-a84e-aed2827cb048)

## Soal 17
Untuk mengubah rjp.baratayuda.e20.com hanya bisa diakses oleh port 14000 dan 14400 adalah dengan mengubah virtual host port menjadi
```
<VirtualHost *:14000 *:14400>
```
Sehingga file konfigurasi dari web server tersebut adalah 
```
echo '
<VirtualHost *:14000 *:14400>
        ServerAdmin webmaster@localhost
        DocumentRoot /var/www/rjp.baratayuda.abimanyu.E20
        ServerName rjp.baratayuda.abimanyu.e20.com
        ServerAlias www.rjp.baratayuda.abimanyu.e20.com

        ErrorLog ${APACHE_LOG_DIR}/error.log
        CustomLog ${APACHE_LOG_DIR}/access.log combined
</VirtualHost>
' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.E20.com.conf
```
Kemudian tambahkan port tersebut ke ports.conf dengan
```
echo '
Listen 14000
Listen 14400
' >> /etc/apache2/ports.conf
```
Lalu buat direktori DocumentRoot dan copy isi webserver dari resources. 
```
mkdir /var/www/rjp.baratayuda.abimanyu.E20

cp -r /root/rjp.baratayuda.abimanyu.yyy.com/. /var/www/rjp.baratayuda.abimanyu.E20
```
Kemudian enable site dan restart apache2

## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/95972129/1a48646d-b1d9-44b5-b52a-a35410dda484)

## Soal 18

Tambahkan directory pada file config rjp.baratayuda.e20.com dengan :
```
<Directory /var/www/rjp.baratayuda.abimanyu.E20>
        AuthType Basic
        AuthName "RJP"
        AuthBasicProvider file
        AuthUserFile "/etc/apache2/passrjp"
	Require valid-user
</Directory>
```

Lalu buat file password dengan :
```
htpasswd -c -b /etc/apache2/passrjp Wayang baratayudae20
```

## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/95972129/9e26d15e-6b2d-4a1d-85c4-e51328ae5a8a)

## Soal 19

Agar setiap kali mengakses ip address abimanyu akan menuju ke abimanyu.e20.com maka tambah IP address Abimanyu sebagai ServerAlias pada file config.
```
ServerAlias www.abimanyu.e20.com 192.216.3.4
```

## Testing 
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/95972129/18e1b80e-6bdf-4d0b-a172-9909045c1b24)
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/95972129/a0dd7702-5ef3-4ef2-8145-1177d48a8ba3)

## Soal 20

Agar dapat mengarahkan segala request file gambar yang mengandung substring "abimanyu" maka buat folder .htaccess pada var/www/parikesit.abimanyu.e20.com
```
echo '
RewriteEngine On
RewriteCond %{REQUEST_URI} ^/public/images/((.*)abimanyu(.*)+\.(jpe?g|png|gif|bmp))$
RewriteCond %{REQUEST_URI} !/public/images/abimanyu.png
RewriteRule /.* http://parikesit.abimanyu.e20.com/public/images/abimanyu.png ' > /var/www/parikesit.abimanyu.E20/.htaccess

```

Lalu tambahkan kode :
```
	<Directory var/www/parikesit.abimanyu.E20>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
        </Directory>
```
pada file config parikesit.abimanyu.e20.com

## Testing
![image](https://github.com/gustino7/Jarkom-Modul-2-E20-2023/assets/95972129/5508115c-75a2-4a17-a015-75c39415b4ec)

