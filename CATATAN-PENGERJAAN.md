# Jarkom Modul 4 - Serial Experiments Lain: The Wired Expansion - K-68

Controller: Group C, http://10.4.89.250/
Prefix IP: 192.245

## Pembagian kerja

| | Ryan (5027231046) | Made Gde Krisna Wangsa |
|---|---|---|
| Platform | GNS3 | Cisco Packet Tracer |
| Metode | CIDR | VLSM |
| Sheet | Penggabungan - CIDR, Pembagian IP - CIDR | Pembagian IP - VLSM |
| Pohon | pohon CIDR | pohon VLSM |
| README | bagian CIDR dan GNS3 | bagian VLSM dan CPT |
| Repo GitHub | dibuat Ryan | |
| Sheet Rute | berdua, hitung sendiri lalu bandingkan | |

## Data pasti

- 13 router, 13 switch, 25 node host, 1 NAT. Total 52 node
- 25 subnet: 16 segmen LAN dan 9 link antar router
- Satu node host mewakili banyak host. Karl tertulis 700 host tetapi tetap satu node
- Router pakai image debinet, seluruh host pakai alpinet
- Antar router disambung LANGSUNG tanpa switch, sesuai gambar soal
- Switch2 adalah segmen bersama empat router: Wired, Psyche, Copland, Schumann

## Hasil perhitungan CIDR

- Blok akar: 192.245.0.0/18
- Terpakai: 192.245.0.0 sampai 192.245.32.131
- Sudah dicek: tidak ada subnet yang tumpang tindih, semua di dalam /18
- Aturan penggabungan: netmask gabungan = 1 tingkat di atas netmask terbesar yang digabung
- Arah penggabungan: mulai dari subnet terjauh dari cloud, merambat ke Cyberia
- Di router yang punya tiga cabang atau lebih, dua blok TERKECIL digabung lebih dulu.
  Ini pilihan untuk menahan netmask membengkak. Kalau digabung asal urut hasilnya /15,
  yang bahkan tidak muat di prefix /16.

## Nomor eth tiap router

| Router | eth0 | eth1 | eth2 | eth3 |
|---|---|---|---|---|
| Cyberia | NAT1 | Switch1 | Wired | |
| Wired | Cyberia | Switch2 | Navi | Tachibana |
| Psyche | Switch2 | Switch3 | | |
| Copland | Switch2 | Switch4 | Juri | |
| Schumann | Switch2 | Akashic | Accela | |
| Accela | Schumann | Switch5 | | |
| Navi | Wired | Switch6 | Switch7 | Switch8 |
| Tachibana | Wired | Switch9 | Deus | |
| Deus | Tachibana | Switch10 | Protocol7 | |
| Protocol7 | Deus | Switch11 | Knights | |
| Knights | Protocol7 | Eiri | Phantoma | |
| Phantoma | Knights | Switch12 | Shibuya | |
| Shibuya | Phantoma | Switch13 | | |

Pola: eth0 selalu menghadap ke atas, ke arah internet.

## Berkas di folder ini

| Berkas | Isi |
|---|---|
| modul4-cidr-gns3.md | tiga sheet CIDR, pohon, alamat interface, static routing |
| modul4-config-final.md | isi interfaces dan route.sh siap tempel, nomor eth sudah final |
| K-68_Modul_4.xlsx | spreadsheet, tiga sheet CIDR terisi, sheet VLSM menunggu Made |
| screenshot/ | bukti pengerjaan |
| config/ | cadangan konfigurasi di luar project GNS3 |

## Catatan teknis dibawa dari modul sebelumnya

- Hanya /root dan /etc/network/interfaces yang BERTAHAN saat node restart
- Editor Edit network configuration GNS3 MEMBUANG baris yang memuat karakter '>'.
  Sembunyikan perintah redirect di dalam berkas .sh di /root, panggil lewat: up bash /root/route.sh
- Node harus MATI saat berkas interfaces disunting
- Jumlah adapter diubah PER NODE lewat klik kanan, Configure, Network, Adapters. Jangan lewat Preferences
- alpinet memakai BusyBox: opsi ping harus ditulis SEBELUM target.
  SALAH : ping 192.245.24.1 -c 3
  BENAR : ping -c 3 192.245.24.1

## Progress

- [x] Baca topologi, tentukan 25 subnet dan kebutuhan host tiap subnet
- [x] Susun penggabungan CIDR sampai akar /18
- [x] Turunkan pohon dan alokasikan alamat 25 subnet
- [x] Isi sheet Rute, Penggabungan - CIDR, Pembagian IP - CIDR
- [x] Bangun topologi GNS3, 52 node, kabel lengkap
- [x] Pasang IP di 13 router
- [x] Pasang IP di 25 node host
- [x] Static routing 13 router
- [x] Uji ping, minimal Mika di Shibuya ke Karl di Psyche
- [x] Export project GNS3  (K-68_Modul4.gns3project)
- [ ] Gambar pohon CIDR
- [x] README bagian CIDR dan GNS3  (README.md)
- [ ] Repo GitHub
- [ ] Samakan urutan label A1-A25 dengan Made
- [ ] Sepakati netmask di baris Total sheet Rute
- [ ] Submit

## Yang perlu diverifikasi Ryan

1. Angka kebutuhan host A17 Switch8 (tiga node) dan A18 Switch2 (empat router)
2. Satu baris penggabungan di tingkat II
3. Urutan label A1 sampai A25 supaya sama dengan punya Made.
   Punya Ryan mulai dari Shibuya, yaitu alamat terkecil.

---

## Urutan Pengerjaan Lengkap

Rincian tiap tahap beserta perintahnya ada di `modul4-langkah-pengerjaan.md`.

### Bagian 1: Perhitungan (selesai)

1. Baca topologi, tentukan 25 subnet. 16 segmen LAN ditambah 9 link antar router
2. Hitung kebutuhan IP tiap subnet. Jumlah host ditambah jumlah interface router di segmen itu
3. Tentukan netmask tiap subnet dari kebutuhan tersebut
4. Susun penggabungan CIDR dari subnet terjauh (Shibuya) menuju Cyberia.
   Aturan: netmask gabungan = 1 tingkat di atas netmask terbesar yang digabung.
   Di router bercabang tiga atau lebih, gabungkan dua blok terkecil lebih dulu
5. Hasil akhir penggabungan: blok akar 192.245.0.0/18 lewat 9 tingkat, A sampai J
6. Turunkan pohon dari akar ke bawah mengikuti struktur penggabungan.
   Blok besar mendapat alamat lebih dulu, dan tiap blok diselaraskan ke kelipatan ukurannya
7. Catat Network ID, netmask, broadcast, dan range tiap subnet
8. Isi sheet Rute, Penggabungan - CIDR, dan Pembagian IP - CIDR

### Bagian 2: Membangun topologi GNS3 (selesai)

9. Buat 13 router memakai image debinet
10. Atur adapter tiap router jadi 8 lewat klik kanan, Configure, Network, Adapters
11. Buat 13 switch
12. Buat 25 node host memakai image alpinet
13. Rename semua node sesuai nama di gambar soal
14. Tarik kabel antar router secara langsung tanpa switch, urut dari Cyberia ke bawah,
    supaya eth0 tiap router selalu menghadap ke arah internet
15. Tarik empat kabel ke Switch2 dari Wired, Psyche, Copland, dan Schumann
16. Tarik kabel router ke switch, lalu switch ke node host
17. Sambungkan Juri ke Copland, Akashic ke Schumann, dan Eiri ke Knights tanpa switch
18. Sambungkan NAT1 ke Cyberia

### Bagian 3: Memasang IP dan routing (selesai)

19. Isi interfaces tiap router lewat Edit network configuration, node dalam keadaan MATI.
    Urutan: Cyberia, Wired, Psyche, Copland, Schumann, Accela, Navi, Tachibana,
    Deus, Protocol7, Knights, Phantoma, Shibuya
20. Buat /root/route.sh di tiap router lewat console, pakai cat heredoc, lalu chmod dan jalankan
21. Uji tiap router ke router induknya sebelum lanjut ke router berikutnya
22. Isi interfaces 25 node host, masing-masing alamat, netmask, dan gateway
23. Jalankan ulang bash /root/route.sh di semua router, karena sebagian rute gagal
    saat pertama dijalankan ketika router tujuan belum punya IP

### Bagian 4: Pengujian dan pengumpulan (belum)

24. Uji ping ujung ke ujung. Dari Mika di Shibuya ke Karl di Psyche, ke Omnipresence
    di Accela, ke JJ di Cyberia, dan ke 8.8.8.8
25. Screenshot tiap bukti dan simpan ke folder screenshot
26. Export project GNS3
27. Gambar pohon CIDR dari struktur di modul4-cidr-gns3.md
28. Buat repo GitHub dan tulis README bagian CIDR dan GNS3
29. Samakan urutan label A1 sampai A25 dengan Made
30. Sepakati netmask yang ditulis di baris Total sheet Rute
31. Gabungkan berkas dari Made, lalu submit sebelum Kamis 5 November 2026 pukul 23.59

---

## Hasil pengujian, 5 November 2026

Semua ping 0% packet loss.

| Dari | Ke | TTL | Bukti |
|---|---|---|---|
| Mika (Shibuya) | Karl 192.245.24.2 (Psyche) | 56 | uji-ping-mika-ujung-ke-ujung.png |
| Mika (Shibuya) | Omnipresence 192.245.8.2 (Accela) | 55 | sama |
| Mika (Shibuya) | JJ 192.245.32.2 (Cyberia) | 56 | sama |
| Mika (Shibuya) | 8.8.8.8 | 104 | sama |
| Karl (Psyche) | Lain 192.245.0.2 (Shibuya) | 56 | uji-ping-karl-balik.png |
| Karl (Psyche) | Yasuo 192.245.18.2 (Navi) | 61 | sama |
| Taro (Navi) | Mika 192.245.0.3 (Shibuya) | 56 | uji-ping-taro.png |
| Taro (Navi) | 8.8.8.8 | 109 | sama |

Tabel routing terbukti ringkas berkat penggabungan CIDR:

- **Cyberia** cuma punya 1 rute statis, yaitu 192.245.0.0/19 via 192.245.32.130,
  untuk menjangkau 24 subnet sekaligus. Bukti: routing-cyberia.png
- **Wired** punya 5 rute, satu per cabang: /21 ke Tachibana, /21 ke Schumann,
  /22 ke Navi, /22 ke Psyche, /22 ke Copland. Bukti: routing-dan-interface-wired.png
- **Phantoma** cuma punya 1 rute statis, 192.245.0.0/26 via 192.245.0.98, plus default.
  Bukti: routing-phantoma.png

Tanpa penggabungan, Wired saja akan butuh lebih dari 20 rute.

## Isi folder screenshot

| Berkas | Isi |
|---|---|
| topologi-gns3-terbangun.png | topologi lengkap dengan kabel dan label eth |
| topologi-gns3-semua-node-jalan.png | 52 node menyala |
| uji-ping-mika-ujung-ke-ujung.png | 4 ping dari node terdalam |
| uji-ping-karl-balik.png | ping arah sebaliknya dari Psyche |
| uji-ping-taro.png | ping dari cabang Navi |
| routing-cyberia.png | tabel routing Cyberia |
| routing-phantoma.png | tabel routing Phantoma |
| routing-dan-interface-wired.png | tabel routing dan 4 interface Wired |
