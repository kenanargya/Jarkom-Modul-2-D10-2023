# Jarkom-Modul-2-D10-2023

| Nama | NRP |
| ------- | ------- |
| Muhammad Rafi Insan Fillah | 5025211169  |
| Ken Anargya Alkausar | 5025211168  |

## 1. 
Yudhistira akan digunakan sebagai DNS Master, Werkudara sebagai DNS Slave, Arjuna merupakan Load Balancer yang terdiri dari beberapa Web Server yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Buatlah topologi dengan pembagian sebagai berikut. Folder topologi dapat diakses pada

### Penyelesaian
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/3da73ed9-8697-4098-abd3-e6013684f8bc)

## 2.
Buatlah website utama pada node arjuna dengan akses ke arjuna.yyy.com dengan alias www.arjuna.yyy.com dengan yyy merupakan kode kelompok.

### Penyelesaian

Yudhistira
```.sh
echo 'zone "arjuna.d10.com" {
    type master;
    file "/etc/bind/jarkom/arjuna.d10.com";
};
' > /etc/bind/named.conf.local

mkdir /etc/bind/jarkom

cp /etc/bind/db.local /etc/bind/jarkom/arjuna.d10.com
```

```.sh
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     arjuna.d10.com. root.arjuna.d10.com. (
                        2022100601      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      arjuna.d10.com.
@       IN      A       192.196.3.5     ; IP Arjuna
www     IN      CNAME   arjuna.d10.com.
@       IN      AAAA    ::1' > /etc/bind/jarkom/arjuna.d10.com

service bind9 restart
```

Client
```.sh
apt-get update &&
apt-get install dnsutils -y &&
apt-get install lynx -y

echo 'nameserver 192.196.2.2 # IP Yudhistira
nameserver 192.196.2.3 # IP Werkudara' > /etc/resolv.conf
```

### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/0b9ed8cf-5f6d-4af1-8261-e75edc58d030)

## 3.
Dengan cara yang sama seperti soal nomor 2, buatlah website utama dengan akses ke abimanyu.yyy.com dan alias www.abimanyu.yyy.com

### Penyelesaian

Yudhistira
```.sh
echo 'zone "arjuna.d10.com" {
    type master;
    file "/etc/bind/jarkom/arjuna.d10.com";
};
zone "abimanyu.d10.com" {
    type master;
    file "/etc/bind/jarkom/abimanyu.d10.com";
};
' > /etc/bind/named.conf.local

mkdir /etc/bind/jarkom

cp /etc/bind/db.local /etc/bind/jarkom/abimanyu.d10.com
```

```.sh
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d10.com. root.abimanyu.d10.com. (
                        2022100601      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       	IN      NS      abimanyu.d10.com.
@       	IN      A       192.196.3.3     ; IP abimanyu
www     	IN      CNAME   abimanyu.d10.com.
@       	IN      AAAA    ::1' > /etc/bind/jarkom/abimanyu.d10.com
```

### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/756ece06-621a-4fbe-95db-bd937b85a2fe)

## 4. Kemudian, karena terdapat beberapa web yang harus di-deploy, buatlah subdomain parikesit.abimanyu.yyy.com yang diatur DNS-nya di Yudhistira dan mengarah ke Abimanyu.

### Penyelesaian

Yudhistira
```.sh
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d10.com. root.abimanyu.d10.com. (
                        2022100601      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       	IN      NS      abimanyu.d10.com.
@       	IN      A       192.196.3.3     ; IP abimanyu
www     	IN      CNAME   abimanyu.d10.com.
parikesit	IN	A	192.196.3.3	; IP Abimanyu
www.parikesit	IN	CNAME	parikesit 
@       	IN      AAAA    ::1' > /etc/bind/jarkom/abimanyu.d10.com
```

### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/39920992-0031-4a98-a7d4-1aee7c894b15)

## 5.
Buat juga reverse domain untuk domain utama. (Abimanyu saja yang direverse)

### Penyelesaian

Yudhistira
```.sh
echo 'zone "arjuna.d10.com" {
    type master;
    file "/etc/bind/jarkom/arjuna.d10.com";
};

zone "abimanyu.d10.com" {
    type master;
    file "/etc/bind/jarkom/abimanyu.d10.com";
};

zone "3.196.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/3.196.192.in-addr.arpa";
};

cp /etc/bind/db.local /etc/bind/jarkom/3.196.192.in-addr.arpa
' > /etc/bind/named.conf.local
```

```.sh
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d10.com. root.abimanyu.d10.com. (
                        2022100601      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
3.196.192.in-addr.arpa. IN      NS      abimanyu.d10.com.
3                       IN      PTR     abimanyu.d10.com. ; Byte ke-5 abimanyu
' > /etc/bind/jarkom/3.196.192.in-addr.arpa
```

### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/68969851-8cad-4050-a4f4-caa3e2807809)

## 6. 
Agar dapat tetap dihubungi ketika DNS Server Yudhistira bermasalah, buat juga Werkudara sebagai DNS Slave untuk domain utama.

### Penyelesaian

Yudhsitira
```.sh
echo 'zone "arjuna.d10.com" {
    type master;
    file "/etc/bind/jarkom/arjuna.d10.com";
};
zone "abimanyu.d10.com" {
    type master;
    also-notify { 192.196.2.3; };
    allow-transfer { 192.196.2.3; };
    file "/etc/bind/jarkom/abimanyu.d10.com";
};
zone "3.196.192.in-addr.arpa" {
    type master;
    file "/etc/bind/jarkom/3.196.192.in-addr.arpa";
};
' > /etc/bind/named.conf.local

service bind9 restart
```

Werkudara
```.sh
echo 'zone "abimanyu.d10.com" {
    type slave;
    masters { 192.196.2.2; }; // Masukan IP Yudhistira tanpa tanda petik
    file "/var/lib/bind/abimanyu.d10.com";
};
' > /etc/bind/named.conf.local

mkdir /etc/bind/baratayuda

cp /etc/bind/db.local /etc/bind/baratayuda/baratayuda.abimanyu.d10.com
```

### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/23633d4c-5129-44ed-a910-68e8afe3aa84)
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/94dd41b7-0c09-4b3e-9378-010ea5d34d10)

## 7.
Seperti yang kita tahu karena banyak sekali informasi yang harus diterima, buatlah subdomain khusus untuk perang yaitu baratayuda.abimanyu.yyy.com dengan alias www.baratayuda.abimanyu.yyy.com yang didelegasikan dari Yudhistira ke Werkudara dengan IP menuju ke Abimanyu dalam folder Baratayuda.

### Penyelesaian

Yudhistira
```.sh
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     abimanyu.d10.com. root.abimanyu.d10.com. (
                        2022100601      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       	IN      NS      abimanyu.d10.com.
@       	IN      A       192.196.3.3     ; IP abimanyu
www     	IN      CNAME   abimanyu.d10.com.
parikesit	IN	A	192.196.3.3	; IP Abimanyu
www.parikesit	IN	CNAME	parikesit 
ns1		    IN	A	192.196.2.3	; IP Werkudara
baratayuda	IN	NS	ns1
@       	IN      AAAA    ::1' > /etc/bind/jarkom/abimanyu.d10.com

echo 'options {
        directory "/var/cache/bind";

        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};' > /etc/bind/named.conf.options
```

Werkudara
```.sh
echo 'options {
        directory "/var/cache/bind";

        allow-query{any;};

        auth-nxdomain no;    # conform to RFC1035
        listen-on-v6 { any; };
};' > /etc/bind/named.conf.options

echo 'zone "abimanyu.d10.com" {
    type slave;
    masters { 192.196.2.2; }; // Masukan IP Yudhistira tanpa tanda petik
    file "/var/lib/bind/abimanyu.d10.com";
};
zone "baratayuda.abimanyu.d10.com" {
    type master;
    file "/etc/bind/baratayuda/baratayuda.abimanyu.d10.com";
};' > /etc/bind/named.conf.local

mkdir /etc/bind/baratayuda

cp /etc/bind/db.local /etc/bind/baratayuda/baratayuda.abimanyu.d10.com
```

### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/b53eb277-9116-492c-9093-01582a271747)

## 8.
Untuk informasi yang lebih spesifik mengenai Ranjapan Baratayuda, buatlah subdomain melalui Werkudara dengan akses rjp.baratayuda.abimanyu.yyy.com dengan alias www.rjp.baratayuda.abimanyu.yyy.com yang mengarah ke Abimanyu.

### Penyelesaian

Werkudara
```.sh
echo ';
; BIND data file for local loopback interface
;
$TTL    604800
@       IN      SOA     baratayuda.abimanyu.d10.com. root.baratayuda.abimanyu.d10.com. (
                        2022100601      ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      baratayuda.abimanyu.d10.com.
@       IN      A       192.196.3.3                     ; IP abimanyu
www     IN      CNAME   baratayuda.abimanyu.d10.com.
rjp     IN      A       192.196.3.3                     ; IP abimanyu
www.rjp IN      CNAME   rjp
' > /etc/bind/baratayuda/baratayuda.abimanyu.d10.com
```

### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/a0ed5487-0df6-4aca-8b0a-b3815c6d07e3)

## 9.
Arjuna merupakan suatu Load Balancer Nginx dengan tiga worker (yang juga menggunakan nginx sebagai webserver) yaitu Prabakusuma, Abimanyu, dan Wisanggeni. Lakukan deployment pada masing-masing worker.

### Penyelesaian

Prabukusuma
```.sh
mkdir /var/www/arjuna.d10

wget -O /var/www/arjuna.d10/arjuna.yyy.com.zip "https://drive.google.com/u/0/uc?id=17tAM_XDKYWDvF-JJix1x7txvTBEax7vX&export=download"

unzip -d /var/www/arjuna.d10 /var/www/arjuna.d10/arjuna.yyy.com.zip && rm /var/www/arjuna.d10/arjuna.yyy.com.zip

mv /var/www/arjuna.d10/arjuna.yyy.com/index.php /var/www/arjuna.d10/

rm -r /var/www/arjuna.d10/arjuna.yyy.com/

echo ' server {

 	listen 8001;

 	root /var/www/arjuna.d10;

 	index index.php index.html index.htm;
 	server_name arjuna.d10.com;

 	location / {
 			try_files $uri $uri/ /index.php?$query_string;
 	}

 	# pass PHP scripts to FastCGI server
 	location ~ \.php$ {
 	include snippets/fastcgi-php.conf;
 	fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
 	}

 location ~ /\.ht {
 			deny all;
 	}

 	error_log /var/log/nginx/arjuna_error.log;
 	access_log /var/log/nginx/arjuna_access.log;
 }' > /etc/nginx/sites-available/arjuna.d10

ln -s /etc/nginx/sites-available/arjuna.d10 /etc/nginx/sites-enabled

service php7.0-fpm start

service php7.0-fpm restart

rm -rf /etc/nginx/sites-enabled/default
```

Abimanyu
```.sh
mkdir /var/www/arjuna.d10

wget -O /var/www/arjuna.d10/arjuna.yyy.com.zip "https://drive.google.com/u/0/uc?id=17tAM_XDKYWDvF-JJix1x7txvTBEax7vX&export=download"

unzip -d /var/www/arjuna.d10 /var/www/arjuna.d10/arjuna.yyy.com.zip && rm /var/www/arjuna.d10/arjuna.yyy.com.zip

mv /var/www/arjuna.d10/arjuna.yyy.com/index.php /var/www/arjuna.d10/

rm -r /var/www/arjuna.d10/arjuna.yyy.com/

echo ' server {

 	listen 8002;

 	root /var/www/arjuna.d10;

 	index index.php index.html index.htm;
 	server_name arjuna.d10.com;

 	location / {
 			try_files $uri $uri/ /index.php?$query_string;
 	}

 	# pass PHP scripts to FastCGI server
 	location ~ \.php$ {
 	include snippets/fastcgi-php.conf;
 	fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
 	}

 location ~ /\.ht {
 			deny all;
 	}

 	error_log /var/log/nginx/arjuna_error.log;
 	access_log /var/log/nginx/arjuna_access.log;
 }' > /etc/nginx/sites-available/arjuna.d10

ln -s /etc/nginx/sites-available/arjuna.d10 /etc/nginx/sites-enabled

service php7.0-fpm start

service php7.0-fpm restart

rm -rf /etc/nginx/sites-enabled/default
```

Wisanggeni
```script.sh
mkdir /var/www/arjuna.d10

wget -O /var/www/arjuna.d10/arjuna.yyy.com.zip "https://drive.google.com/u/0/uc?id=17tAM_XDKYWDvF-JJix1x7txvTBEax7vX&export=download"

unzip -d /var/www/arjuna.d10 /var/www/arjuna.d10/arjuna.yyy.com.zip && rm /var/www/arjuna.d10/arjuna.yyy.com.zip

mv /var/www/arjuna.d10/arjuna.yyy.com/index.php /var/www/arjuna.d10/

rm -r /var/www/arjuna.d10/arjuna.yyy.com/

echo ' server {

 	listen 8003;

 	root /var/www/arjuna.d10;

 	index index.php index.html index.htm;
 	server_name arjuna.d10.com;

 	location / {
 			try_files $uri $uri/ /index.php?$query_string;
 	}

 	# pass PHP scripts to FastCGI server
 	location ~ \.php$ {
 	include snippets/fastcgi-php.conf;
 	fastcgi_pass unix:/var/run/php/php7.0-fpm.sock;
 	}

 location ~ /\.ht {
 			deny all;
 	}

 	error_log /var/log/nginx/arjuna_error.log;
 	access_log /var/log/nginx/arjuna_access.log;
 }' > /etc/nginx/sites-available/arjuna.d10

ln -s /etc/nginx/sites-available/arjuna.d10 /etc/nginx/sites-enabled

service php7.0-fpm start

service php7.0-fpm restart

rm -rf /etc/nginx/sites-enabled/default
```

### Output
Prabukusuma   
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/646881df-edfe-4d18-a04a-8f525651a8a6)

Abimanyu  
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/267ad921-7a18-4117-b454-6cc55d5da96a)

Wisanggeni  
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/5419cb98-5a9c-43a6-b9e5-3ba587a572b5)

## 10.
Kemudian gunakan algoritma Round Robin untuk Load Balancer pada Arjuna. Gunakan server_name pada soal nomor 1. Untuk melakukan pengecekan akses alamat web tersebut kemudian pastikan worker yang digunakan untuk menangani permintaan akan berganti ganti secara acak. Untuk webserver di masing-masing worker wajib berjalan di port 8001-8003. Contoh
* Prabakusuma:8001
* Abimanyu:8002
* Wisanggeni:8003

### Penyelesaian

Arjuna
```.sh
echo '# Default menggunakan Round Robin
 upstream myweb  {
 	server 192.196.3.2:8001; #IP Prabukusuma
    server 192.196.3.3:8002; #IP Abimanyu
    server 192.196.3.4:8003; #IP Wisanggeni
 }

 server {
 	listen 80;
 	server_name arjuna.d10.com www.arjuna.d10.com;

 	location / {
 	proxy_pass http://myweb;
 	}
 }' > /etc/nginx/sites-available/lb-arjuna.d10

ln -s /etc/nginx/sites-available/lb-arjuna.d10 /etc/nginx/sites-enabled
```

### Output
Prabukusuma   
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/bd1fda61-5455-45c3-9535-c9445fb0c07b)

Abimanyu  
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/de5f8ca4-854b-4152-8ae9-fec21c439f90)

Wisanggeni  
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/5c20133d-a98d-4a6a-9026-648a420cd708)

## 11.
Selain menggunakan Nginx, lakukan konfigurasi Apache Web Server pada worker Abimanyu dengan web server www.abimanyu.yyy.com. Pertama dibutuhkan web server dengan DocumentRoot pada /var/www/abimanyu.yyy

### Penyelesaian
```.sh
echo '
<VirtualHost *:80>
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/html
 
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
 
   	</VirtualHost>
' > /etc/apache2/sites-available/000-default.conf

mkdir /var/www/abimanyu.d10

wget -O /var/www/abimanyu.d10/abimanyu.yyy.com.zip "https://drive.google.com/u/0/uc?id=1a4V23hwK9S7hQEDEcv9FL14UkkrHc-Zc&export=download"

unzip -d /var/www/abimanyu.d10 /var/www/abimanyu.d10/abimanyu.yyy.com.zip && rm /var/www/abimanyu.d10/abimanyu.yyy.com.zip

mv /var/www/abimanyu.d10/abimanyu.yyy.com/index.php /var/www/abimanyu.d10/

mv /var/www/abimanyu.d10/abimanyu.yyy.com/home.html /var/www/abimanyu.d10/

mv /var/www/abimanyu.d10/abimanyu.yyy.com/abimanyu.webp /var/www/abimanyu.d10/

rm -r /var/www/abimanyu.d10/abimanyu.yyy.com/

echo '
<VirtualHost *:80>
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/abimanyu.d10
		ServerName abimanyu.d10.com
		ServerAlias www.abimanyu.d10.com

		<Directory /var/www/abimanyu.d10>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
    	</Directory>

    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
		 
   	</VirtualHost>
' > /etc/apache2/sites-available/abimanyu.d10.com.conf
```

### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/84fe3729-7f8c-4d80-b0a1-f35eceb72ad8)

## 12.
Setelah itu ubahlah agar url www.abimanyu.yyy.com/index.php/home menjadi www.abimanyu.yyy.com/home.

### Penyelesaian
```.sh
a2enmod rewrite

echo '
RewriteEngine On

RewriteCond %{REQUEST_FILENAME} !-d

RewriteRule ^([^\.]+)$ index.php/$1 [NC,L]
' > /var/www/abimanyu.d10/.htaccess
```

### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/bbb6e875-5005-4eaf-a51d-a1eff9c748c7)

## 13.
Selain itu, pada subdomain www.parikesit.abimanyu.yyy.com, DocumentRoot disimpan pada /var/www/parikesit.abimanyu.yyy

### Penyelesaian
```sh
wget -O /var/www/parikesit.abimanyu.d10.zip "https://drive.google.com/u/0/uc?id=1LdbYntiYVF_NVNgJis1GLCLPEGyIOreS&export=download"

unzip -d /var/www /var/www/parikesit.abimanyu.d10.zip && rm /var/www/parikesit.abimanyu.d10.zip

mv /var/www/parikesit.abimanyu.yyy.com /var/www/parikesit.abimanyu.d10

rm -r /var/www/parikesit.abimanyu.d10/parikesit.abimanyu.yyy.com

mkdir /var/www/parikesit.abimanyu.d10/secret

echo '
<VirtualHost *:80>
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/parikesit.abimanyu.d10
		ServerName parikesit.abimanyu.d10.com
		ServerAlias www.parikesit.abimanyu.d10.com
 
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined

   	</VirtualHost>
' > /etc/apache2/sites-available/parikesit.abimanyu.d10.com.conf

a2ensite parikesit.abimanyu.d10.com
```
### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/4050e2ef-16ce-4416-a6a3-7713af702064)

## 14.
Pada subdomain tersebut folder /public hanya dapat melakukan directory listing sedangkan pada folder /secret tidak dapat diakses (403 Forbidden).

### Penyelesaian
```.sh
echo '
<VirtualHost *:80>
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/parikesit.abimanyu.d10
		ServerName parikesit.abimanyu.d10.com
		ServerAlias www.parikesit.abimanyu.d10.com

		<Directory /var/www/parikesit.abimanyu.d10/secret>
                Options -Indexes
    	</Directory>

		<Directory /var/www/parikesit.abimanyu.d10/public>
                Options +Indexes
    	</Directory>

        <Directory /var/www/parikesit.abimanyu.d10/public/images>
			AllowOverride All
		</Directory>
 
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined

		ErrorDocument 404 /error/404.html
   		ErrorDocument 403 /error/403.html
 
   	</VirtualHost>
' > /etc/apache2/sites-available/parikesit.abimanyu.d10.com.conf
```

### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/45a2cd59-bc18-4c48-b619-c1b9efa015e9)  
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/08e30fd8-54be-4612-ad4e-394d3fcd3293)

## 15.
Buatlah kustomisasi halaman error pada folder /error untuk mengganti error kode pada Apache. Error kode yang perlu diganti adalah 404 Not Found dan 403 Forbidden.

### Penyelesaian
```.sh
echo '
<VirtualHost *:80>
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/parikesit.abimanyu.d10
		ServerName parikesit.abimanyu.d10.com
		ServerAlias www.parikesit.abimanyu.d10.com

		<Directory /var/www/parikesit.abimanyu.d10/secret>
                Options -Indexes
    	</Directory>

		<Directory /var/www/parikesit.abimanyu.d10/public>
                Options +Indexes
    	</Directory>

		<Directory /var/www/parikesit.abimanyu.d10/public/images>
			AllowOverride All
		</Directory>
 
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined

		ErrorDocument 404 /error/404.html
   		ErrorDocument 403 /error/403.html
 
   	</VirtualHost>
' > /etc/apache2/sites-available/parikesit.abimanyu.d10.com.conf
```
### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/a7babdc3-bb26-4335-89a1-71da6895da59)

## 16.
Buatlah suatu konfigurasi virtual host agar file asset www.parikesit.abimanyu.yyy.com/public/js menjadi www.parikesit.abimanyu.yyy.com/js

### Penyelesaian
```.sh
echo '
<VirtualHost *:80>
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/parikesit.abimanyu.d10
		ServerName parikesit.abimanyu.d10.com
		ServerAlias www.parikesit.abimanyu.d10.com

		<Directory /var/www/parikesit.abimanyu.d10/secret>
                Options -Indexes
    	</Directory>

		<Directory /var/www/parikesit.abimanyu.d10/public>
                Options +Indexes
    	</Directory>

		<Directory /var/www/parikesit.abimanyu.d10/public/images>
			AllowOverride All
		</Directory>

		Alias "/js" "/var/www/parikesit.abimanyu.d10/public/js"
 
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined

		ErrorDocument 404 /error/404.html
   		ErrorDocument 403 /error/403.html
 
   	</VirtualHost>
' > /etc/apache2/sites-available/parikesit.abimanyu.d10.com.conf
```
### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/dbf18adb-f32b-4c08-86c9-a61caea67d31)

## 17.
Agar aman, buatlah konfigurasi agar www.rjp.baratayuda.abimanyu.yyy.com hanya dapat diakses melalui port 14000 dan 14400.

### Penyelesaian
```.sh
wget -O /var/www/rjp.baratayuda.abimanyu.d10.zip "https://drive.google.com/u/0/uc?id=1pPSP7yIR05JhSFG67RVzgkb-VcW9vQO6&export=download"

unzip -d /var/www /var/www/rjp.baratayuda.abimanyu.d10.zip && rm /var/www/rjp.baratayuda.abimanyu.d10.zip

mv /var/www/rjp.baratayuda.abimanyu.yyy.com /var/www/rjp.baratayuda.abimanyu.d10

rm -r /var/www/rjp.baratayuda.abimanyu.d10/rjp.baratayuda.abimanyu.yyy.com

echo '
<VirtualHost *:14000>
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/rjp.baratayuda.abimanyu.d10
		ServerName rjp.baratayuda.abimanyu.d10.com
		ServerAlias www.rjp.baratayuda.abimanyu.d10.com

		<Directory /var/www/rjp.baratayuda.abimanyu.d10>
                Options +Indexes
                AllowOverride All
                Require all granted
    	</Directory>

    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
 
   	</VirtualHost>

	<VirtualHost *:14400>
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/rjp.baratayuda.abimanyu.d10
		ServerName rjp.baratayuda.abimanyu.d10.com
		ServerAlias www.rjp.baratayuda.abimanyu.d10.com

		<Directory /var/www/rjp.baratayuda.abimanyu.d10>
                Options +Indexes
                AllowOverride All
                Require all granted
    	</Directory>
 
    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
 
   	</VirtualHost>
' > /etc/apache2/sites-available/rjp.baratayuda.abimanyu.d10.com.conf

echo '
# If you just change the port or add more ports here, you will likely also
# have to change the VirtualHost statement in
# /etc/apache2/sites-enabled/000-default.conf

Listen 80
Listen 14000
Listen 14400

<IfModule ssl_module>
        Listen 443
</IfModule>

<IfModule mod_gnutls.c>
        Listen 443
</IfModule>

# vim: syntax=apache ts=4 sw=4 sts=4 sr noet
' > /etc/apache2/ports.conf
```
### Output
Port 14400  
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/d6d3fed2-7c09-4eaf-a593-333786562bab)

Port 14000  
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/b7bc34f6-96a7-421d-b16b-e9c9b3701f70)

## 18.
Untuk mengaksesnya buatlah autentikasi username berupa “Wayang” dan password “baratayudayyy” dengan yyy merupakan kode kelompok. Letakkan DocumentRoot pada /var/www/rjp.baratayuda.abimanyu.yyy.

### Penyelesaian
```.sh
echo 'Wayang:$apr1$bqoKoaRn$LezUrevcJ5SQN6MT9DcbA/' > /var/www/rjp.baratayuda.abimanyu.d10/.htpasswd

echo 'AuthType Basic
AuthName "Authentication Required"
AuthUserFile /var/www/rjp.baratayuda.abimanyu.d10/.htpasswd
Require valid-user' > /var/www/rjp.baratayuda.abimanyu.d10/.htaccess
```

### Output
Username  
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/18f6067d-00e0-4382-b4d4-7a7e8e40447f)

Password  
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/7c6aaef1-73a6-44a8-815c-0df81bd72b98)

## 19.
Buatlah agar setiap kali mengakses IP dari Abimanyu akan secara otomatis dialihkan ke www.abimanyu.yyy.com (alias)

### Penyelesaian
Abimanyu  
```.sh
echo '
<VirtualHost *:80>
    	ServerAdmin webmaster@localhost
    	DocumentRoot /var/www/abimanyu.d10
		ServerName abimanyu.d10.com
		ServerAlias www.abimanyu.d10.com
		ServerAlias 192.196.3.3

		<Directory /var/www/abimanyu.d10>
                Options +FollowSymLinks -Multiviews
                AllowOverride All
    	</Directory>

    	ErrorLog ${APACHE_LOG_DIR}/error.log
    	CustomLog ${APACHE_LOG_DIR}/access.log combined
		 
   	</VirtualHost>
' > /etc/apache2/sites-available/abimanyu.d10.com.conf
```

### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/891378f6-7ca7-46ec-9743-c26d78333fba)

## 20.
Karena website www.parikesit.abimanyu.yyy.com semakin banyak pengunjung dan banyak gambar gambar random, maka ubahlah request gambar yang memiliki substring “abimanyu” akan diarahkan menuju abimanyu.png.

### Penyelesaian
```.sh
echo '
RedirectMatch 302 /public/images/.*abimanyu.*\.(png|jpg|jpeg)$ /public/images/abimanyu.png
' > /var/www/parikesit.abimanyu.d10/public/images/.htaccess
```

### Output
Dengan Substring Abimanyu  
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/6d4327bf-dba1-408f-a5c3-eb45350294b6)

Tanpa Substring Abimanyu  
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/b5bca74a-2e7d-4f8d-9f4a-f631a9dc31dc)







































