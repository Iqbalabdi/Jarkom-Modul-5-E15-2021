# Jarkom-Modul-5-E15-2021
Lapres Praktikum Jarkom Modul 5  
Kelompok E15 : M Iqbal Abdi - 05111940000151

## Sebelum mulai
**(A)** Buat topologi dulu gan
![image](https://user-images.githubusercontent.com/75016595/145771100-f873c4df-884f-4f51-ab32-b5067eb68648.png)
Keterangan :
- Doriki adalah DNS Server
- Jipangu adalah DHCP Server
- Maingate dan Jorge adalah Web Server
- Jumlah Host pada Blueno adalah 100 host
- Jumlah Host pada Cipher adalah 700 host
- Jumlah Host pada Elena adalah 300 host
- Jumlah Host pada Fukurou adalah 200 host

### Subnetting (VLSM)

| Host | Jumlah | Network ID | Broadcast | Netmask
| ------------- | ------------- | ------------- | ------------- | ------------- | 
| CIPHER | 701 | 192.207.0.0 | 192.207.3.255 | 255.255.252.0 |
| ELENA | 301 | 192.207.4.0 | 192.207.5.255 | 255.255.254.0 |
| FUKUROU | 201 | 192.207.6.0 | 192.207.6.255 | 255.255.255.0 |
| BLUENO | 101 | 192.207.7.0 | 192.207.7.127 | 255.255.255.128 |
| WATER7-SERVER | 3 | 192.207.7.128 | 192.207.7.135 | 255.255.255.248 |
| GUANHAO-SERVER | 3 | 192.207.7.136 | 192.207.7.143 | 255.255.255.248 |
| WATER7-FOOSHA | 2 | 192.207.7.144 | 192.207.7.147 | 255.255.255.252 |
| GUANHAO-FOOSHA | 2 | 192.207.7.148 | 192.207.7.151 | 255.255.255.25 |  

* **Foosha**
```
auto eth0
iface eth0 inet static
	address 192.168.122.2
	netmask 255.255.255.252
        gateway 192.168.122.1

auto eth1
iface eth1 inet static
	address 192.207.7.145
	netmask 255.255.255.252

auto eth2
iface eth2 inet static
	address 192.207.7.149
	netmask 255.255.255.252
```
* **Water7**
```
auto eth0
iface eth0 inet static
	address 192.207.7.146
	netmask 255.255.255.252
        gateway 192.207.7.145

auto eth1
iface eth1 inet static
	address 192.207.7.1
	netmask 255.255.255.128

auto eth2
iface eth2 inet static
	address 192.207.0.1
	netmask 255.255.252.0

auto eth3
iface eth3 inet static
	address 192.207.7.129
	netmask 255.255.255.248

```
* **Guanhao**
```
auto eth0
iface eth0 inet static
	address 192.207.7.150
	netmask 255.255.255.252
        gateway 192.207.7.149

auto eth1
iface eth1 inet static
	address 192.207.4.1
	netmask 255.255.254.0

auto eth2
iface eth2 inet static
	address 192.207.7.137
	netmask 255.255.255.248

auto eth3
iface eth3 inet static
	address 192.207.6.1
	netmask 255.255.255.0
```
* **Doriki**
```
auto eth0
iface eth0 inet static
	address 192.207.7.130
	netmask 255.255.255.248
	gateway 192.207.7.129
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
* **Jipangu**
```
auto eth0
iface eth0 inet static
	address 192.207.7.131
	netmask 255.255.255.248
	gateway 192.207.7.129
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
* **Jorge**
```
auto eth0
iface eth0 inet static
	address 192.207.7.138
	netmask 255.255.255.248
	gateway 192.207.7.137
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
* **Maingate**
```
auto eth0
iface eth0 inet static
	address 192.207.7.139
	netmask 255.255.255.248
	gateway 192.207.7.137
	up echo nameserver 192.168.122.1 > /etc/resolv.conf
```
* **Blueno, Cipher, Elena, dan Fukurou**
```
auto eth0
iface eth0 inet dhcp
```

**(C)** Routing
* **Foosha**
```
route add -net 192.207.7.128 netmask 255.255.255.248 gw 192.207.7.146
route add -net 192.207.7.0 netmask 255.255.255.128 gw 192.207.7.146
route add -net 192.207.0.0 netmask 255.255.252.0 gw 192.207.7.146

route add -net 192.207.4.0 netmask 255.255.254.0 gw 192.207.7.150
route add -net 192.207.7.136 netmask 255.255.255.248 gw 192.207.7.150
route add -net 192.207.6.0 netmask 255.255.255.0 gw 192.207.7.150
```
* **Water7**
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.207.7.145
```
* **Guanhao**
```
route add -net 0.0.0.0 netmask 0.0.0.0 gw 192.207.7.149
```

**(D)** Set IP client menjadi **DHCP** dengan **Jipangu** sebagai DHCP servernya.
(**catatan**: jalankan program pada jawaban [nomor 1](#nomor-1) terlebih dahulu)
* **Jipangu**

Set nameserver pada `/etc/resolv.conf` ke **192.168.122.1** lalu install `isc-dhcp-server`
```bash
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt update && apt install isc-dhcp-server -y
```

Lalu pada `/etc/default/isc-dhcp-server` settinga agak konfigurasinya menjadi seperti ini
```
INTERFACES="eth0"
```

Kemudian, tambahkan konfigurasi DHCP pada `/etc/dhcp/dhcpd.conf` menjadi seperti ini
```
subnet 192.207.7.128 netmask 255.255.255.248 {
}

subnet 192.207.7.0 netmask 255.255.255.128 {
    range 192.207.7.2 192.207.7.126;
    option routers 192.207.7.1;
    option broadcast-address 192.207.7.127;
    option domain-name-servers 192.207.7.130;
}

subnet 192.207.0.0 netmask 255.255.252.0 {
    range 192.207.0.2 192.207.3.254;
    option routers 192.207.0.1;
    option broadcast-address 192.207.3.255;
    option domain-name-servers 192.207.7.130;
}

subnet 192.207.6.0 netmask 255.255.255.0 {
    range 192.207.6.2 range 192.207.6.254;
    option routers 192.207.6.1;
    option broadcast-address 192.207.6.255;
    option domain-name-servers 192.207.7.130;
}

subnet 192.207.4.0 netmask 255.255.254.0 {
    range 192.207.4.2 192.207.5.254;
    option routers 192.207.4.1;
    option broadcast-address 192.207.5.255;
    option domain-name-servers 192.207.7.130;
}
```

Restart `isc-dhcp-server`

* DHCP Relay (**Water7** dan **Guanhao**)

Set nameserver pada `/etc/resolv.conf` ke **192.168.122.1** lalu install `isc-dhcp-relay`
```bash
echo nameserver 192.168.122.1 > /etc/resolv.conf
apt update && apt install isc-dhcp-relay -y
```

Ubah konfigurasi `/etc/default/isc-dhcp-relay` menjadi
```
SERVERS="192.207.7.131"
INTERFACES=""
OPTIONS=""
```

Kemudian restart `isc-dhcp-relay`

### Nomor 1
Tambahkan rule berikut pada iptables **Foosha**
```
iptables -t nat -A POSTROUTING -o eth0 -j SNAT --to-source 192.168.122.2 -s 192.207.0.0/16
```
**Penjelasan** :
* `-t nat`: menggunakan tabel nat karena akan mengubah alamat paket
* `-A POSTROUTING`: menambahkan chain POSTROUTING karena mengubah alamat paket setelah routing
* `-o eth0`: interface tempat paket keluar (dalam hal ini ke NAT)
* `-s 192.207.0.0/16`: alamat asal yang akan diubah
* `-j SNAT`: menggunakan target SNAT untuk mengubah alamat asal
* `--to-source 192.168.122.2`: mengubah alamat asal menjadi 192.168.122.2 (interface eth0 **Foosha**)

Untuk mengecek apakah sudah berhasil, bisa dengan cara `ping google.com` pada setiap node.

### Nomor 2
Tambahkan rule berikut pada iptables **Foosha**
```
iptables -A FORWARD -i eth0 -p tcp --dport 80 -d 192.207.7.128/29 -j DROP
```
**Penjelasan** :
* `-A FORWARD`: menambahkan chain FORWARD karena berurusan dengan filtering
* `-i eth0`: interface tempat paket masuk (dalam hal ini dari NAT)
* `-p tcp`: menggunakan protokol TCP
* `--dport 80`: menggunakan port 80 (HTTP)
* `-d 192.207.7.128/29`: alamat tujuan paket, dalam hal ini adalah subnet A1 (DHCP dan DNS server)
* `-j DROP`: men-DROP paket yang melalui filter

### Nomor 3
Tambahkan rule berikut pada iptables di DHCP dan DNS server
```
iptables -A INPUT -p icmp -m connlimit --connlimit-above 3 --connlimit-mask 0 -j DROP
```
**Penjelasan**
* `-A INPUT`: menambahkan chain INPUT karena berurusan dengan paket yang masuk
* `-p icmp`: menggunakan protokol ICMP
* `-m connlimit`: menggunakan matchcase *connlimit*
* `--conlimit-above 3`: menerapkan rule apabila ada koneksi lebih dari 3
* `--conlimit-mask 0`: menggunakan netmask 0 (rule berlaku untuk semua network)
* `-j DROP`: men-DROP paket ketika koneksi melebihi 3

### Nomor 4
Tambahkan rule berikut pada iptables di **Doriki**
```
iptables -A INPUT -s 192.207.7.0/25,192.207.0.0/22 -m time --timestart 07:00 --timestop 15:00 --weekdays Mon,Tue,Wed,Thu -j ACCEPT
```
**Penjelasan** :
* `-A INPUT`: menambahkan chain INPUT karena berhubungan dengan paket yang masuk
* `-s 192.207.7.0/25,192.207.0.0/22`: mendefinisikan alamat asal (**Blueno** dan **Cipher**)
* `-m time`: menggunakan matchcase *time*
* `--timestart 07:00`: mendefinisikan waktu rule mulai, dalam hal ini 07:00
* `--timestop 15:00`: mendefinisikan waktu rule berhenti, dalam hal ini 15:00
* `--weekdays Mon,Tue,Wed,Thu`: mendefinisikan hari apa rule dijalankan (Senin, Selasa, Rabu, Kamis)
* `-j ACCEPT`: menggunakan target ACCEPT ketika rule memenuhi matchcase

Setelah itu tambahkan rule default, yakni ketika rule di atas tidak dijalankan (tidak memenuhi matchcase)
```
iptables -A INPUT -s 192.207.7.0/25,192.207.0.0/22 -j REJECT
```
**Penjelasan** :
* `-A INPUT`: menambahkan chain INPUT karena berhubungan dengan paket yang masuk
* `-s 192.207.7.0/25,192.207.0.0/22`: mendefinisikan alamat asal (**Blueno** dan **Cipher**)
* `-j REJECT`: menggunakan target REJECT ke semua paket yang melalui rule ini

### Nomor 5
Tambahkan rule berikut pada iptables di **Doriki**
```
iptables -A INPUT -s 192.207.4.0/23,192.207.6.0/24  -m time --timestart 07:00 --timestop 15:00 -j REJECT
```
**Penjelasan** :
* `-A INPUT`: menambahkan chain INPUT karena berhubungan dengan paket yang masuk
* `-s 192.207.4.0/23,192.207.6.0/24`: mendefinisikan alamat asal (**Elena** dan **Fukurou**)
* `-m time`: menggunakan matchcase *time*
* `--timestart 15:01`: mendefinisikan waktu rule mulai, dalam hal ini 15:00
* `--timestop 23:59:59`: mendefinisikan waktu rule berhenti, dalam hal ini 23:59:59
* `--timestart 00:00`: mendefinisikan waktu rule mulai lagi, dalam hal ini 00:00
* `--timestop 06:59`: mendefinisikan waktu rule berhenti lagi, dalam hal ini 06:59
* `-j ACCEPT`: menggunakan target ACCEPT ketika rule memenuhi matchcase

Setelah itu tambahkan rule default, yakni ketika rule di atas tidak dijalankan (tidak memenuhi matchcase)
```
iptables -A INPUT -s 192.207.4.0/23,192.207.6.0/24 -j REJECT
```
**Penjelasan** :
* `-A INPUT`: menambahkan chain INPUT karena berhubungan dengan paket yang masuk
* `-s 192.207.4.0/23,192.207.6.0/24`: mendefinisikan alamat asal (**Elena** dan **Fukurou**)
* `-j REJECT`: menggunakan target REJECT ke semua paket yang melalui rule ini

Untuk mengecek apakah berjalan atau tidak, lakukan ping pada **Elena**
![5-fail](images/5-failed.png)

Lalu ganti tanggal atau waktunya, kemudian ping lagi
![5-succeed](images/5-succeed.png)

### Nomor 6
Install bind9 pada **Doriki**
```bash
apt update && apt install bind9 -y
```
Buat domain baru pada **Doriki** yang mengarah ke IP random (dalam hal ini 192.207.7.140)
```
$TTL    604800
@       IN      SOA     jarkom.com. root.jarkom.com. (
                     2021120701         ; Serial
                         604800         ; Refresh
                          86400         ; Retry
                        2419200         ; Expire
                         604800 )       ; Negative Cache TTL
;
@       IN      NS      jarkom.com.
@       IN      A       192.207.7.140
```

Tambahkan zone pada **Doriki**
```
zone "jarkom.com" {
        type master;
        file "/etc/bind/jarkom/jarkom.com";
};
```

Tambahkan rule pada **Guanhao** seperti berikut
```
iptables -t nat -A PREROUTING -d 192.207.7.140 -m state --state NEW -m statistic --mode nth --every 2 --packet 0 -j DNAT --to-destination 192.207.7.138
iptables -t nat -A PREROUTING -d 192.207.7.140 -j DNAT --to-destination 192.207.7.139
```
**Penjelasan** :
* `-t nat`: menggunakan tabel NAT karena akan merubah alamat paket
* `-A PREROUTING`: menggunakan chain PREROUTING karena mengubah alamat sebelum routing
* `-d 192.207.7.140`: mendefinisikan alamat, dalam hal ini 192.207.7.140
* `-m statistic`: menggunakan matchcase *statistic*
* `--mode nth`: menggunakan mode *nth*
* `--every 2`: menerapkan rule setiap 2 paket
* `--packet 0`: mengambil paket ke-1 dari 2 paket yang melalui rule
* `-j DNAT`: menggunakan target DNAT untuk mengubah alamat tujuan paket
* `--to-destination 192.207.7.138`: mengubah alamat paket menjadi 192.207.7.138 (**Jorge**)

Tambahkan rule default atau ketika rule di atas tidak dijalankan
```
iptables -t nat -A PREROUTING -d 192.207.7.140 -j DNAT --to-destination 192.207.7.139
```
**Penjelasan** :
* `-t nat`: menggunakan tabel NAT karena akan merubah alamat paket
* `-A PREROUTING`: menggunakan chain PREROUTING karena mengubah alamat sebelum routing
* `-d 192.207.7.140`: mendefinisikan alamat, dalam hal ini 192.207.7.140
* `-j DNAT`: menggunakan target DNAT untuk mengubah alamat tujuan paket
* `--to-destination 192.207.7.139`: mengubah alamat paket menjadi 192.207.7.138 (**Maingate**)

Untuk mengecek apkah berhasil diubah alamat tujuannya, jalankan `tcpdump` pada **Jorge** dan **Maingate**
* **Jorge**
![image](https://user-images.githubusercontent.com/75016595/145813059-5251a69c-78db-4e42-a046-e6742a60b5ab.png)
* **Maingate**
![image](https://user-images.githubusercontent.com/75016595/145813258-7b86ed83-650f-46a1-b1e6-2e1097f99e4d.png)
