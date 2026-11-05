# Langkah Pengerjaan Modul 4 CIDR di GNS3

Blok akar **192.245.0.0/18**. Kerjakan berurutan dari Tahap 1 sampai Tahap 27.
Setiap router diuji dulu sebelum lanjut ke router berikutnya.

**Dua aturan yang wajib diingat**

1. Berkas `interfaces` disunting lewat Configure, Edit network configuration, dan **node harus MATI**
2. Berkas `route.sh` dibuat lewat console node yang **sudah menyala**. Jangan ditempel ke editor interfaces, karena editor GNS3 membuang baris yang memuat karakter `>`

---

## Tahap 1: Router Cyberia

### 1.1 Matikan node Cyberia, buka Configure, Edit network configuration, tempel:

```
# eth0 -> NAT1
auto eth0
iface eth0 inet dhcp

# eth1 -> Switch1
auto eth1
iface eth1 inet static
	address 192.245.32.1
	netmask 255.255.255.128

# eth2 -> Wired
auto eth2
iface eth2 inet static
	address 192.245.32.129
	netmask 255.255.255.252
	up bash /root/route.sh

```

### 1.2 Nyalakan node Cyberia, buka console, tempel:

```bash
cat > /root/route.sh <<'EOF'
#!/bin/bash
sysctl -w net.ipv4.ip_forward=1
iptables -t nat -C POSTROUTING -o eth0 -j MASQUERADE 2>/dev/null || \
    iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE
ip route add 192.245.0.0/19 via 192.245.32.130
EOF
chmod +x /root/route.sh
bash /root/route.sh
```

Perintah `ip route add` menuju Wired akan gagal sekarang karena router itu belum punya IP. Itu normal, jalankan ulang `bash /root/route.sh` setelah mereka selesai dikonfigurasi.

### 1.3 Uji

```bash
ip a
ping -c 3 8.8.8.8
```

- `eth0` = alamat dari NAT, biasanya 192.168.122.x
- `eth1` = `192.245.32.1`
- `eth2` = `192.245.32.129`
- Ping ke 8.8.8.8 tembus

---

## Tahap 2: Router Wired

### 2.1 Matikan node Wired, buka Configure, Edit network configuration, tempel:

```
# eth0 -> Cyberia
auto eth0
iface eth0 inet static
	address 192.245.32.130
	netmask 255.255.255.252

# eth1 -> Switch2
auto eth1
iface eth1 inet static
	address 192.245.20.1
	netmask 255.255.255.248

# eth2 -> Navi
auto eth2
iface eth2 inet static
	address 192.245.20.9
	netmask 255.255.255.252

# eth3 -> Tachibana
auto eth3
iface eth3 inet static
	address 192.245.20.13
	netmask 255.255.255.252
	up bash /root/route.sh

```

### 2.2 Nyalakan node Wired, buka console, tempel:

```bash
cat > /root/route.sh <<'EOF'
#!/bin/bash
ip route add 192.245.16.0/22 via 192.245.20.10
ip route add 192.245.0.0/21 via 192.245.20.14
ip route add 192.245.24.0/22 via 192.245.20.2
ip route add 192.245.28.0/22 via 192.245.20.3
ip route add 192.245.8.0/21 via 192.245.20.4
ip route add default via 192.245.32.129
EOF
chmod +x /root/route.sh
bash /root/route.sh
```

Perintah `ip route add` menuju Navi, Tachibana, Psyche, Copland, Schumann akan gagal sekarang karena router itu belum punya IP. Itu normal, jalankan ulang `bash /root/route.sh` setelah mereka selesai dikonfigurasi.

### 2.3 Uji

```bash
ip a
ping -c 3 192.245.32.129      # ke Cyberia
```

- `eth0` = `192.245.32.130`
- `eth1` = `192.245.20.1`
- `eth2` = `192.245.20.9`
- `eth3` = `192.245.20.13`
- Ping ke Cyberia tembus

---

## Tahap 3: Router Psyche

### 3.1 Matikan node Psyche, buka Configure, Edit network configuration, tempel:

```
# eth0 -> Switch2
auto eth0
iface eth0 inet static
	address 192.245.20.2
	netmask 255.255.255.248

# eth1 -> Switch3
auto eth1
iface eth1 inet static
	address 192.245.24.1
	netmask 255.255.252.0
	up bash /root/route.sh

```

### 3.2 Nyalakan node Psyche, buka console, tempel:

```bash
cat > /root/route.sh <<'EOF'
#!/bin/bash
ip route add default via 192.245.20.1
EOF
chmod +x /root/route.sh
bash /root/route.sh
```

### 3.3 Uji

```bash
ip a
ping -c 3 192.245.20.1      # ke Wired
```

- `eth0` = `192.245.20.2`
- `eth1` = `192.245.24.1`
- Ping ke Wired tembus

---

## Tahap 4: Router Copland

### 4.1 Matikan node Copland, buka Configure, Edit network configuration, tempel:

```
# eth0 -> Switch2
auto eth0
iface eth0 inet static
	address 192.245.20.3
	netmask 255.255.255.248

# eth1 -> Switch4
auto eth1
iface eth1 inet static
	address 192.245.28.1
	netmask 255.255.254.0

# eth2 -> Juri
auto eth2
iface eth2 inet static
	address 192.245.30.1
	netmask 255.255.255.192
	up bash /root/route.sh

```

### 4.2 Nyalakan node Copland, buka console, tempel:

```bash
cat > /root/route.sh <<'EOF'
#!/bin/bash
ip route add default via 192.245.20.1
EOF
chmod +x /root/route.sh
bash /root/route.sh
```

### 4.3 Uji

```bash
ip a
ping -c 3 192.245.20.1      # ke Wired
```

- `eth0` = `192.245.20.3`
- `eth1` = `192.245.28.1`
- `eth2` = `192.245.30.1`
- Ping ke Wired tembus

---

## Tahap 5: Router Schumann

### 5.1 Matikan node Schumann, buka Configure, Edit network configuration, tempel:

```
# eth0 -> Switch2
auto eth0
iface eth0 inet static
	address 192.245.20.4
	netmask 255.255.255.248

# eth1 -> Akashic
auto eth1
iface eth1 inet static
	address 192.245.12.1
	netmask 255.255.255.248

# eth2 -> Accela
auto eth2
iface eth2 inet static
	address 192.245.12.9
	netmask 255.255.255.252
	up bash /root/route.sh

```

### 5.2 Nyalakan node Schumann, buka console, tempel:

```bash
cat > /root/route.sh <<'EOF'
#!/bin/bash
ip route add 192.245.8.0/22 via 192.245.12.10
ip route add default via 192.245.20.1
EOF
chmod +x /root/route.sh
bash /root/route.sh
```

Perintah `ip route add` menuju Accela akan gagal sekarang karena router itu belum punya IP. Itu normal, jalankan ulang `bash /root/route.sh` setelah mereka selesai dikonfigurasi.

### 5.3 Uji

```bash
ip a
ping -c 3 192.245.20.1      # ke Wired
```

- `eth0` = `192.245.20.4`
- `eth1` = `192.245.12.1`
- `eth2` = `192.245.12.9`
- Ping ke Wired tembus

---

## Tahap 6: Router Accela

### 6.1 Matikan node Accela, buka Configure, Edit network configuration, tempel:

```
# eth0 -> Schumann
auto eth0
iface eth0 inet static
	address 192.245.12.10
	netmask 255.255.255.252

# eth1 -> Switch5
auto eth1
iface eth1 inet static
	address 192.245.8.1
	netmask 255.255.252.0
	up bash /root/route.sh

```

### 6.2 Nyalakan node Accela, buka console, tempel:

```bash
cat > /root/route.sh <<'EOF'
#!/bin/bash
ip route add default via 192.245.12.9
EOF
chmod +x /root/route.sh
bash /root/route.sh
```

### 6.3 Uji

```bash
ip a
ping -c 3 192.245.12.9      # ke Schumann
```

- `eth0` = `192.245.12.10`
- `eth1` = `192.245.8.1`
- Ping ke Schumann tembus

---

## Tahap 7: Router Navi

### 7.1 Matikan node Navi, buka Configure, Edit network configuration, tempel:

```
# eth0 -> Wired
auto eth0
iface eth0 inet static
	address 192.245.20.10
	netmask 255.255.255.252

# eth1 -> Switch6
auto eth1
iface eth1 inet static
	address 192.245.16.1
	netmask 255.255.255.0

# eth2 -> Switch7
auto eth2
iface eth2 inet static
	address 192.245.17.1
	netmask 255.255.255.192

# eth3 -> Switch8
auto eth3
iface eth3 inet static
	address 192.245.18.1
	netmask 255.255.255.0
	up bash /root/route.sh

```

### 7.2 Nyalakan node Navi, buka console, tempel:

```bash
cat > /root/route.sh <<'EOF'
#!/bin/bash
ip route add default via 192.245.20.9
EOF
chmod +x /root/route.sh
bash /root/route.sh
```

### 7.3 Uji

```bash
ip a
ping -c 3 192.245.20.9      # ke Wired
```

- `eth0` = `192.245.20.10`
- `eth1` = `192.245.16.1`
- `eth2` = `192.245.17.1`
- `eth3` = `192.245.18.1`
- Ping ke Wired tembus

---

## Tahap 8: Router Tachibana

### 8.1 Matikan node Tachibana, buka Configure, Edit network configuration, tempel:

```
# eth0 -> Wired
auto eth0
iface eth0 inet static
	address 192.245.20.14
	netmask 255.255.255.252

# eth1 -> Switch9
auto eth1
iface eth1 inet static
	address 192.245.4.1
	netmask 255.255.254.0

# eth2 -> Deus
auto eth2
iface eth2 inet static
	address 192.245.6.1
	netmask 255.255.255.252
	up bash /root/route.sh

```

### 8.2 Nyalakan node Tachibana, buka console, tempel:

```bash
cat > /root/route.sh <<'EOF'
#!/bin/bash
ip route add 192.245.0.0/22 via 192.245.6.2
ip route add default via 192.245.20.13
EOF
chmod +x /root/route.sh
bash /root/route.sh
```

Perintah `ip route add` menuju Deus akan gagal sekarang karena router itu belum punya IP. Itu normal, jalankan ulang `bash /root/route.sh` setelah mereka selesai dikonfigurasi.

### 8.3 Uji

```bash
ip a
ping -c 3 192.245.20.13      # ke Wired
```

- `eth0` = `192.245.20.14`
- `eth1` = `192.245.4.1`
- `eth2` = `192.245.6.1`
- Ping ke Wired tembus

---

## Tahap 9: Router Deus

### 9.1 Matikan node Deus, buka Configure, Edit network configuration, tempel:

```
# eth0 -> Tachibana
auto eth0
iface eth0 inet static
	address 192.245.6.2
	netmask 255.255.255.252

# eth1 -> Switch10
auto eth1
iface eth1 inet static
	address 192.245.2.1
	netmask 255.255.255.128

# eth2 -> Protocol7
auto eth2
iface eth2 inet static
	address 192.245.2.129
	netmask 255.255.255.252
	up bash /root/route.sh

```

### 9.2 Nyalakan node Deus, buka console, tempel:

```bash
cat > /root/route.sh <<'EOF'
#!/bin/bash
ip route add 192.245.0.0/23 via 192.245.2.130
ip route add default via 192.245.6.1
EOF
chmod +x /root/route.sh
bash /root/route.sh
```

Perintah `ip route add` menuju Protocol7 akan gagal sekarang karena router itu belum punya IP. Itu normal, jalankan ulang `bash /root/route.sh` setelah mereka selesai dikonfigurasi.

### 9.3 Uji

```bash
ip a
ping -c 3 192.245.6.1      # ke Tachibana
```

- `eth0` = `192.245.6.2`
- `eth1` = `192.245.2.1`
- `eth2` = `192.245.2.129`
- Ping ke Tachibana tembus

---

## Tahap 10: Router Protocol7

### 10.1 Matikan node Protocol7, buka Configure, Edit network configuration, tempel:

```
# eth0 -> Deus
auto eth0
iface eth0 inet static
	address 192.245.2.130
	netmask 255.255.255.252

# eth1 -> Switch11
auto eth1
iface eth1 inet static
	address 192.245.1.1
	netmask 255.255.255.192

# eth2 -> Knights
auto eth2
iface eth2 inet static
	address 192.245.1.65
	netmask 255.255.255.252
	up bash /root/route.sh

```

### 10.2 Nyalakan node Protocol7, buka console, tempel:

```bash
cat > /root/route.sh <<'EOF'
#!/bin/bash
ip route add 192.245.0.0/24 via 192.245.1.66
ip route add default via 192.245.2.129
EOF
chmod +x /root/route.sh
bash /root/route.sh
```

Perintah `ip route add` menuju Knights akan gagal sekarang karena router itu belum punya IP. Itu normal, jalankan ulang `bash /root/route.sh` setelah mereka selesai dikonfigurasi.

### 10.3 Uji

```bash
ip a
ping -c 3 192.245.2.129      # ke Deus
```

- `eth0` = `192.245.2.130`
- `eth1` = `192.245.1.1`
- `eth2` = `192.245.1.65`
- Ping ke Deus tembus

---

## Tahap 11: Router Knights

### 11.1 Matikan node Knights, buka Configure, Edit network configuration, tempel:

```
# eth0 -> Protocol7
auto eth0
iface eth0 inet static
	address 192.245.1.66
	netmask 255.255.255.252

# eth1 -> Eiri
auto eth1
iface eth1 inet static
	address 192.245.0.129
	netmask 255.255.255.248

# eth2 -> Phantoma
auto eth2
iface eth2 inet static
	address 192.245.0.137
	netmask 255.255.255.252
	up bash /root/route.sh

```

### 11.2 Nyalakan node Knights, buka console, tempel:

```bash
cat > /root/route.sh <<'EOF'
#!/bin/bash
ip route add 192.245.0.0/25 via 192.245.0.138
ip route add default via 192.245.1.65
EOF
chmod +x /root/route.sh
bash /root/route.sh
```

Perintah `ip route add` menuju Phantoma akan gagal sekarang karena router itu belum punya IP. Itu normal, jalankan ulang `bash /root/route.sh` setelah mereka selesai dikonfigurasi.

### 11.3 Uji

```bash
ip a
ping -c 3 192.245.1.65      # ke Protocol7
```

- `eth0` = `192.245.1.66`
- `eth1` = `192.245.0.129`
- `eth2` = `192.245.0.137`
- Ping ke Protocol7 tembus

---

## Tahap 12: Router Phantoma

### 12.1 Matikan node Phantoma, buka Configure, Edit network configuration, tempel:

```
# eth0 -> Knights
auto eth0
iface eth0 inet static
	address 192.245.0.138
	netmask 255.255.255.252

# eth1 -> Switch12
auto eth1
iface eth1 inet static
	address 192.245.0.65
	netmask 255.255.255.224

# eth2 -> Shibuya
auto eth2
iface eth2 inet static
	address 192.245.0.97
	netmask 255.255.255.252
	up bash /root/route.sh

```

### 12.2 Nyalakan node Phantoma, buka console, tempel:

```bash
cat > /root/route.sh <<'EOF'
#!/bin/bash
ip route add 192.245.0.0/26 via 192.245.0.98
ip route add default via 192.245.0.137
EOF
chmod +x /root/route.sh
bash /root/route.sh
```

Perintah `ip route add` menuju Shibuya akan gagal sekarang karena router itu belum punya IP. Itu normal, jalankan ulang `bash /root/route.sh` setelah mereka selesai dikonfigurasi.

### 12.3 Uji

```bash
ip a
ping -c 3 192.245.0.137      # ke Knights
```

- `eth0` = `192.245.0.138`
- `eth1` = `192.245.0.65`
- `eth2` = `192.245.0.97`
- Ping ke Knights tembus

---

## Tahap 13: Router Shibuya

### 13.1 Matikan node Shibuya, buka Configure, Edit network configuration, tempel:

```
# eth0 -> Phantoma
auto eth0
iface eth0 inet static
	address 192.245.0.98
	netmask 255.255.255.252

# eth1 -> Switch13
auto eth1
iface eth1 inet static
	address 192.245.0.1
	netmask 255.255.255.192
	up bash /root/route.sh

```

### 13.2 Nyalakan node Shibuya, buka console, tempel:

```bash
cat > /root/route.sh <<'EOF'
#!/bin/bash
ip route add default via 192.245.0.97
EOF
chmod +x /root/route.sh
bash /root/route.sh
```

### 13.3 Uji

```bash
ip a
ping -c 3 192.245.0.97      # ke Phantoma
```

- `eth0` = `192.245.0.98`
- `eth1` = `192.245.0.1`
- Ping ke Phantoma tembus

---

## Tahap 14: Node host di bawah Cyberia

**Switch1**, subnet A24 `192.245.32.0/25`, gateway `192.245.32.1`

JJ, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.32.2
	netmask 255.255.255.128
	gateway 192.245.32.1
```
MyuMyu, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.32.3
	netmask 255.255.255.128
	gateway 192.245.32.1
```

Uji dari JJ:
```bash
ip a
ping -c 3 192.245.32.1      # gateway
ping -c 3 192.245.32.1      # Cyberia
```

---

## Tahap 15: Node host di bawah Psyche

**Switch3**, subnet A21 `192.245.24.0/22`, gateway `192.245.24.1`

Karl, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.24.2
	netmask 255.255.252.0
	gateway 192.245.24.1
```
Lin, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.24.3
	netmask 255.255.252.0
	gateway 192.245.24.1
```

Uji dari Karl:
```bash
ip a
ping -c 3 192.245.24.1      # gateway
ping -c 3 192.245.32.1      # Cyberia
```

---

## Tahap 16: Node host di bawah Copland

**Switch4**, subnet A22 `192.245.28.0/23`, gateway `192.245.28.1`

Masayuki, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.28.2
	netmask 255.255.254.0
	gateway 192.245.28.1
```

**Juri**, subnet A23 `192.245.30.0/26`, gateway `192.245.30.1`

Juri, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.30.2
	netmask 255.255.255.192
	gateway 192.245.30.1
```

Uji dari Masayuki:
```bash
ip a
ping -c 3 192.245.28.1      # gateway
ping -c 3 192.245.32.1      # Cyberia
```

---

## Tahap 17: Node host di bawah Schumann

**Akashic**, subnet A13 `192.245.12.0/29`, gateway `192.245.12.1`

Akashic, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.12.2
	netmask 255.255.255.248
	gateway 192.245.12.1
```

Uji dari Akashic:
```bash
ip a
ping -c 3 192.245.12.1      # gateway
ping -c 3 192.245.32.1      # Cyberia
```

---

## Tahap 18: Node host di bawah Accela

**Switch5**, subnet A12 `192.245.8.0/22`, gateway `192.245.8.1`

Omnipresence, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.8.2
	netmask 255.255.252.0
	gateway 192.245.8.1
```
Denpa, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.8.3
	netmask 255.255.252.0
	gateway 192.245.8.1
```

Uji dari Omnipresence:
```bash
ip a
ping -c 3 192.245.8.1      # gateway
ping -c 3 192.245.32.1      # Cyberia
```

---

## Tahap 19: Node host di bawah Navi

**Switch6**, subnet A15 `192.245.16.0/24`, gateway `192.245.16.1`

Alice, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.16.2
	netmask 255.255.255.0
	gateway 192.245.16.1
```
Reika, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.16.3
	netmask 255.255.255.0
	gateway 192.245.16.1
```

**Switch7**, subnet A16 `192.245.17.0/26`, gateway `192.245.17.1`

Taro, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.17.2
	netmask 255.255.255.192
	gateway 192.245.17.1
```

**Switch8**, subnet A17 `192.245.18.0/24`, gateway `192.245.18.1`

Yasuo, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.18.2
	netmask 255.255.255.0
	gateway 192.245.18.1
```
Miho, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.18.3
	netmask 255.255.255.0
	gateway 192.245.18.1
```
Kuma, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.18.4
	netmask 255.255.255.0
	gateway 192.245.18.1
```

Uji dari Alice:
```bash
ip a
ping -c 3 192.245.16.1      # gateway
ping -c 3 192.245.32.1      # Cyberia
```

---

## Tahap 20: Node host di bawah Tachibana

**Switch9**, subnet A10 `192.245.4.0/23`, gateway `192.245.4.1`

Collective, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.4.2
	netmask 255.255.254.0
	gateway 192.245.4.1
```
Ghost, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.4.3
	netmask 255.255.254.0
	gateway 192.245.4.1
```

Uji dari Collective:
```bash
ip a
ping -c 3 192.245.4.1      # gateway
ping -c 3 192.245.32.1      # Cyberia
```

---

## Tahap 21: Node host di bawah Deus

**Switch10**, subnet A8 `192.245.2.0/25`, gateway `192.245.2.1`

Lilith, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.2.2
	netmask 255.255.255.128
	gateway 192.245.2.1
```
Specter, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.2.3
	netmask 255.255.255.128
	gateway 192.245.2.1
```

Uji dari Lilith:
```bash
ip a
ping -c 3 192.245.2.1      # gateway
ping -c 3 192.245.32.1      # Cyberia
```

---

## Tahap 22: Node host di bawah Protocol7

**Switch11**, subnet A6 `192.245.1.0/26`, gateway `192.245.1.1`

Chisa, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.1.2
	netmask 255.255.255.192
	gateway 192.245.1.1
```
Hodgeson, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.1.3
	netmask 255.255.255.192
	gateway 192.245.1.1
```

Uji dari Chisa:
```bash
ip a
ping -c 3 192.245.1.1      # gateway
ping -c 3 192.245.32.1      # Cyberia
```

---

## Tahap 23: Node host di bawah Knights

**Eiri**, subnet A4 `192.245.0.128/29`, gateway `192.245.0.129`

Eiri, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.0.130
	netmask 255.255.255.248
	gateway 192.245.0.129
```

Uji dari Eiri:
```bash
ip a
ping -c 3 192.245.0.129      # gateway
ping -c 3 192.245.32.1      # Cyberia
```

---

## Tahap 24: Node host di bawah Phantoma

**Switch12**, subnet A2 `192.245.0.64/27`, gateway `192.245.0.65`

Semi, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.0.66
	netmask 255.255.255.224
	gateway 192.245.0.65
```

Uji dari Semi:
```bash
ip a
ping -c 3 192.245.0.65      # gateway
ping -c 3 192.245.32.1      # Cyberia
```

---

## Tahap 25: Node host di bawah Shibuya

**Switch13**, subnet A1 `192.245.0.0/26`, gateway `192.245.0.1`

Lain, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.0.2
	netmask 255.255.255.192
	gateway 192.245.0.1
```
Mika, matikan node lalu tempel ke Edit network configuration:
```
auto eth0
iface eth0 inet static
	address 192.245.0.3
	netmask 255.255.255.192
	gateway 192.245.0.1
```

Uji dari Lain:
```bash
ip a
ping -c 3 192.245.0.1      # gateway
ping -c 3 192.245.32.1      # Cyberia
```

---

## Tahap 26: Jalankan ulang semua route.sh

Setelah semua router dan host terpasang, buka console tiap router lalu jalankan:

```bash
bash /root/route.sh
ip route
```

Ini perlu karena sebagian perintah `ip route add` gagal saat pertama dijalankan, waktu router tujuannya belum punya IP.

---

## Tahap 27: Uji akhir

Uji ujung ke ujung, dari node terjauh ke node terjauh.

Dari **Mika** (Shibuya, paling dalam):
```bash
ping -c 3 192.245.24.2      # Karl, di Psyche
ping -c 3 192.245.8.2       # Omnipresence, di Accela
ping -c 3 192.245.32.2      # JJ, di Cyberia
ping -c 3 8.8.8.8           # internet
```

Dari **Karl** (Psyche):
```bash
ping -c 3 192.245.0.2       # Lain, di Shibuya
ping -c 3 192.245.18.2      # Yasuo, di Navi
```

Kalau empat ping dari Mika tembus, hampir semua jalur sudah benar, karena Mika berada sembilan router dari Cyberia.

**Ingat:** alpinet memakai BusyBox, opsi ping harus ditulis SEBELUM target.
