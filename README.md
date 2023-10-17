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

```.sh
// Client test
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

cp /etc/bind/db.local /etc/bind/jarkom/abimanyu.d10.com

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

```.sh
echo 'zone "abimanyu.d10.com" {
    type slave;
    masters { 192.196.2.2; }; // Masukan IP Yudhistira tanpa tanda petik
    file "/var/lib/bind/abimanyu.d10.com";
};
' > /etc/bind/named.conf.local
```

### Output
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/23633d4c-5129-44ed-a910-68e8afe3aa84)
![image](https://github.com/kenanargya/Jarkom-Modul-2-D10-2023/assets/92387421/94dd41b7-0c09-4b3e-9378-010ea5d34d10)

## 7.

###












