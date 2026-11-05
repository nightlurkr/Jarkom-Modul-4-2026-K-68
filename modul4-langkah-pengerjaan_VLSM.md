# Langkah Pengerjaan Modul 4 VLSM di Cisco Packet Tracer

Blok akar **192.245.0.0/18**. Pengerjaan CPT dilakukan secara manual (GUI dan CLI).

**Dua aturan yang wajib diingat:**
1. Router seri **2811** hanya memiliki 2 port (`Fa0/0` dan `Fa0/1`). Jika butuh lebih dari 2 koneksi, **wajib mematikan router (tab Physical), menambahkan modul `NM-1FE-TX` atau `NM-2FE2W`, lalu menyalakannya kembali**.
2. Setiap kali memasukkan IP ke interface router, **wajib** diakhiri dengan perintah `no shutdown` agar port menyala.

---

## Tahap 1: Konfigurasi Router Inti (Cyberia & Wired)

### 1.1 Router Cyberia
*(Wajib tambah 1 modul untuk port ke Switch1)*

Buka CLI Cyberia:
```cisco
enable
configure terminal
hostname Cyberia

! Ke Cloud
interface FastEthernet0/0
 ip address 192.168.100.2 255.255.255.0
 ip nat outside
 no shutdown
 exit

! Ke Wired
interface FastEthernet0/1
 ip address 192.245.16.89 255.255.255.252
 ip nat inside
 no shutdown
 exit

! Ke Switch1 (LAN Cyberia)
interface FastEthernet1/0
 ip address 192.245.14.1 255.255.255.128
 ip nat inside
 no shutdown
 exit

! NAT Overload
access-list 1 permit 192.245.0.0 0.0.255.255
ip nat inside source list 1 interface FastEthernet0/0 overload

! Summary Route ke bawah
ip route 192.245.0.0 255.255.0.0 192.245.16.90
ip route 0.0.0.0 0.0.0.0 192.168.100.1
exit
```

### 1.2 Router Wired
*(Wajib tambah 2 modul untuk Navi & Tachibana)*

Buka CLI Wired:
```cisco
enable
configure terminal
hostname Wired

! Ke Cyberia
interface FastEthernet0/0
 ip address 192.245.16.90 255.255.255.252
 no shutdown
 exit

! Ke Switch2
interface FastEthernet0/1
 ip address 192.245.16.41 255.255.255.248
 no shutdown
 exit

! Ke Navi
interface FastEthernet1/0
 ip address 192.245.16.81 255.255.255.252
 no shutdown
 exit

! Ke Tachibana
interface FastEthernet1/1
 ip address 192.245.16.85 255.255.255.252
 no shutdown
 exit

! Default Route ke Cyberia
ip route 0.0.0.0 0.0.0.0 192.245.16.89

! Static Route ke semua cabang
ip route 192.245.0.0 255.255.252.0 192.245.16.42
ip route 192.245.8.0 255.255.254.0 192.245.16.43
ip route 192.245.15.192 255.255.255.192 192.245.16.43
ip route 192.245.16.32 255.255.255.248 192.245.16.44
ip route 192.245.4.0 255.255.252.0 192.245.16.44
ip route 192.245.12.0 255.255.255.0 192.245.16.82
ip route 192.245.13.0 255.255.255.0 192.245.16.82
ip route 192.245.15.128 255.255.255.192 192.245.16.82
ip route 192.245.10.0 255.255.254.0 192.245.16.86
ip route 192.245.14.128 255.255.255.128 192.245.16.86
ip route 192.245.15.0 255.255.255.192 192.245.16.86
ip route 192.245.15.64 255.255.255.192 192.245.16.86
ip route 192.245.16.0 255.255.255.224 192.245.16.86
ip route 192.245.16.48 255.255.255.248 192.245.16.86
ip route 192.245.16.56 255.255.255.252 192.245.16.86
ip route 192.245.16.60 255.255.255.252 192.245.16.86
ip route 192.245.16.64 255.255.255.252 192.245.16.86
ip route 192.245.16.68 255.255.255.252 192.245.16.86
ip route 192.245.16.72 255.255.255.252 192.245.16.86
exit
```

---

## Tahap 2: Konfigurasi Router Cabang Kiri

### 2.1 Router Psyche
Buka CLI Psyche:
```cisco
enable
configure terminal
hostname Psyche
interface FastEthernet0/0
 ip address 192.245.16.42 255.255.255.248
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.245.0.1 255.255.252.0
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 192.245.16.41
exit
```

### 2.2 Router Copland
*(Wajib tambah 1 modul untuk Juri. Sesuaikan port dengan kabelmu)*
Buka CLI Copland:
```cisco
enable
configure terminal
hostname Copland
interface FastEthernet0/0
 ip address 192.245.16.43 255.255.255.248
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.245.15.193 255.255.255.192
 no shutdown
 exit
interface FastEthernet1/0
 ip address 192.245.8.1 255.255.254.0
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 192.245.16.41
exit
```

### 2.3 Router Schumann
*(Wajib tambah 1 modul untuk Accela)*
Buka CLI Schumann:
```cisco
enable
configure terminal
hostname Schumann
interface FastEthernet0/0
 ip address 192.245.16.44 255.255.255.248
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.245.16.33 255.255.255.248
 no shutdown
 exit
interface FastEthernet1/0
 ip address 192.245.16.77 255.255.255.252
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 192.245.16.41
ip route 192.245.4.0 255.255.252.0 192.245.16.78
exit
```

### 2.4 Router Accela
Buka CLI Accela:
```cisco
enable
configure terminal
hostname Accela
interface FastEthernet0/0
 ip address 192.245.16.78 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.245.4.1 255.255.252.0
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 192.245.16.77
exit
```

---

## Tahap 3: Konfigurasi Router Cabang Kanan

### 3.1 Router Navi
*(Wajib tambah 2 modul untuk Switch7 & Switch8)*
Buka CLI Navi:
```cisco
enable
configure terminal
hostname Navi
interface FastEthernet0/0
 ip address 192.245.16.82 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.245.12.1 255.255.255.0
 no shutdown
 exit
interface FastEthernet1/0
 ip address 192.245.15.129 255.255.255.192
 no shutdown
 exit
interface FastEthernet1/1
 ip address 192.245.13.1 255.255.255.0
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 192.245.16.81
exit
```

---

## Tahap 4: Konfigurasi Router Cabang Bawah

### 4.1 Router Tachibana
*(Wajib tambah 1 modul untuk Deus)*
Buka CLI Tachibana:
```cisco
enable
configure terminal
hostname Tachibana
interface FastEthernet0/0
 ip address 192.245.16.86 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.245.10.1 255.255.254.0
 no shutdown
 exit
interface FastEthernet1/0
 ip address 192.245.16.73 255.255.255.252
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 192.245.16.85
ip route 192.245.14.128 255.255.255.128 192.245.16.74
ip route 192.245.15.0 255.255.255.192 192.245.16.74
ip route 192.245.15.64 255.255.255.192 192.245.16.74
ip route 192.245.15.128 255.255.255.192 192.245.16.74
ip route 192.245.16.0 255.255.255.224 192.245.16.74
ip route 192.245.16.48 255.255.255.248 192.245.16.74
ip route 192.245.16.56 255.255.255.252 192.245.16.74
ip route 192.245.16.60 255.255.255.252 192.245.16.74
ip route 192.245.16.64 255.255.255.252 192.245.16.74
ip route 192.245.16.68 255.255.255.252 192.245.16.74
exit
```

### 4.2 Router Deus
*(Wajib tambah 1 modul untuk Switch9)*
Buka CLI Deus:
```cisco
enable
configure terminal
hostname Deus
interface FastEthernet0/0
 ip address 192.245.16.74 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.245.16.69 255.255.255.252
 no shutdown
 exit
interface FastEthernet1/0
 ip address 192.245.14.129 255.255.255.128
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 192.245.16.73
ip route 192.245.15.0 255.255.255.192 192.245.16.70
ip route 192.245.15.64 255.255.255.192 192.245.16.70
ip route 192.245.15.128 255.255.255.192 192.245.16.70
ip route 192.245.16.0 255.255.255.224 192.245.16.70
ip route 192.245.16.48 255.255.255.248 192.245.16.70
ip route 192.245.16.56 255.255.255.252 192.245.16.70
ip route 192.245.16.60 255.255.255.252 192.245.16.70
ip route 192.245.16.64 255.255.255.252 192.245.16.70
exit
```

### 4.3 Router Protocol7
*(Wajib tambah 1 modul untuk Switch11)*
Buka CLI Protocol7:
```cisco
enable
configure terminal
hostname Protocol7
interface FastEthernet0/0
 ip address 192.245.16.70 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.245.16.65 255.255.255.252
 no shutdown
 exit
interface FastEthernet1/0
 ip address 192.245.15.129 255.255.255.192
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 192.245.16.69
ip route 192.245.15.0 255.255.255.192 192.245.16.66
ip route 192.245.15.64 255.255.255.192 192.245.16.66
ip route 192.245.16.0 255.255.255.224 192.245.16.66
ip route 192.245.16.48 255.255.255.248 192.245.16.66
ip route 192.245.16.56 255.255.255.252 192.245.16.66
ip route 192.245.16.60 255.255.255.252 192.245.16.66
exit
```

### 4.4 Router Knights
*(Wajib tambah 1 modul untuk Erin)*
Buka CLI Knights:
```cisco
enable
configure terminal
hostname Knights
interface FastEthernet0/0
 ip address 192.245.16.66 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.245.16.61 255.255.255.252
 no shutdown
 exit
interface FastEthernet1/0
 ip address 192.245.16.49 255.255.255.248
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 192.245.16.65
ip route 192.245.15.0 255.255.255.192 192.245.16.62
ip route 192.245.15.64 255.255.255.192 192.245.16.62
ip route 192.245.16.0 255.255.255.224 192.245.16.62
ip route 192.245.16.56 255.255.255.252 192.245.16.62
exit
```

### 4.5 Router Phantoma
*(Wajib tambah 1 modul untuk Switch12)*
Buka CLI Phantoma:
```cisco
enable
configure terminal
hostname Phantoma
interface FastEthernet0/0
 ip address 192.245.16.62 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.245.16.57 255.255.255.252
 no shutdown
 exit
interface FastEthernet1/0
 ip address 192.245.16.1 255.255.255.224
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 192.245.16.61
ip route 192.245.15.0 255.255.255.192 192.245.16.58
ip route 192.245.15.64 255.255.255.192 192.245.16.58
exit
```

### 4.6 Router Shibuya
Buka CLI Shibuya:
```cisco
enable
configure terminal
hostname Shibuya
interface FastEthernet0/0
 ip address 192.245.16.58 255.255.255.252
 no shutdown
 exit
interface FastEthernet0/1
 ip address 192.245.15.65 255.255.255.192
 no shutdown
 exit
ip route 0.0.0.0 0.0.0.0 192.245.16.57
exit
```

---

## Tahap 5: Konfigurasi End Devices (GUI)
Klik tiap PC/Laptop/Server, masuk ke tab **Desktop > IP Configuration**, isi:
- **Karl**: IP `192.245.0.2`, Mask `255.255.252.0`, Gateway `192.245.0.1`
- **Lin**: IP `192.245.0.3`, Mask `255.255.252.0`, Gateway `192.245.0.1`
- **Omnipresence**: IP `192.245.4.2`, Mask `255.255.252.0`, Gateway `192.245.4.1`
- **Denpa**: IP `192.245.4.3`, Mask `255.255.252.0`, Gateway `192.245.4.1`
- **Masayuki**: IP `192.245.8.2`, Mask `255.255.254.0`, Gateway `192.245.8.1`
- **Juri**: IP `192.245.15.194`, Mask `255.255.255.192`, Gateway `192.245.15.193`
- **Collective**: IP `192.245.10.2`, Mask `255.255.254.0`, Gateway `192.245.10.1`
- **Ghost**: IP `192.245.10.3`, Mask `255.255.254.0`, Gateway `192.245.10.1`
- **Alice**: IP `192.245.12.2`, Mask `255.255.255.0`, Gateway `192.245.12.1`
- **Reika**: IP `192.245.12.3`, Mask `255.255.255.0`, Gateway `192.245.12.1`
- **Yasuo**: IP `192.245.13.2`, Mask `255.255.255.0`, Gateway `192.245.13.1`
- **Miho**: IP `192.245.13.3`, Mask `255.255.255.0`, Gateway `192.245.13.1`
- **Kuma**: IP `192.245.13.4`, Mask `255.255.255.0`, Gateway `192.245.13.1`
- **JJ**: IP `192.245.14.2`, Mask `255.255.255.128`, Gateway `192.245.14.1`
- **MyuMyu**: IP `192.245.14.3`, Mask `255.255.255.128`, Gateway `192.245.14.1`
- **Lillith**: IP `192.245.14.130`, Mask `255.255.255.128`, Gateway `192.245.14.129`
- **Spector**: IP `192.245.14.131`, Mask `255.255.255.128`, Gateway `192.245.14.129`
- **Taro**: IP `192.245.15.66`, Mask `255.255.255.192`, Gateway `192.245.15.65`
- **Chisa**: IP `192.245.15.130`, Mask `255.255.255.192`, Gateway `192.245.15.129`
- **Hodgson**: IP `192.245.15.131`, Mask `255.255.255.192`, Gateway `192.245.15.129`
- **Serni**: IP `192.245.16.2`, Mask `255.255.255.224`, Gateway `192.245.16.1`
- **Akashic**: IP `192.245.16.34`, Mask `255.255.255.248`, Gateway `192.245.16.33`
- **Erin**: IP `192.245.16.50`, Mask `255.255.255.248`, Gateway `192.245.16.49`
- **Lain**: IP `192.245.15.2`, Mask `255.255.255.192`, Gateway `192.245.15.1`
- **Mika**: IP `192.245.15.3`, Mask `255.255.255.192`, Gateway `192.245.15.1`

---

## Tahap 6: Uji Akhir
Uji dari ujung ke ujung menggunakan **Command Prompt** pada Laptop/PC.

Dari **Laptop Lain** (Shibuya):
```cmd
ping 192.245.0.2
ping 192.245.4.3
ping 192.245.14.3
```

Dari **Laptop Alice** (Navi):
```cmd
ping 192.245.15.2
ping 192.245.0.2
```
*(Catatan: Paket pertama biasanya Request Timed Out karena proses ARP, paket kedua dan seterusnya akan Reply).*