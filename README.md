# Franchise-Kafe-Satu-Jaringan-Kasir-Semua-Cabang-Terhubung-ke-Pusat-Lewat-Tunnel-Redundan
🔥☕ Franchise Kafe Satu Jaringan: 💳 Kasir Semua Cabang Terhubung ke Pusat Lewat Tunnel Redundan 🛡️⚡
🎬 1. Latar Belakang: Bencana Kecil yang Mahal
Bayangkan sebuah franchise kafe dengan tiga cabang ☕☕☕. Pada sore hari yang ramai, internet di salah satu cabang tiba-tiba putus. 😱 Akibatnya:
💳 Kasir tidak bisa memproses pembayaran digital dan QRIS.
📦 Stok bahan tidak tersinkron dengan gudang pusat.
🧾 Antrean memanjang, pelanggan kesal, dan sebagian pergi.
📉 Laporan penjualan cabang tidak masuk ke pusat sampai internet pulih.
Satu jam gangguan di jam ramai bisa menghapus omzet yang besar. 💸 Pertanyaan project ini:
Bagaimana membuat semua cabang terhubung ke pusat seperti satu jaringan, dengan jalur cadangan otomatis, tanpa perlu admin turun tangan? 🤔🔧
🧠 2. Fakta Menarik yang Jarang Orang Tahu
🚇 Fakta 1: Tunnel Itu Seperti "Jalan Tol Pribadi" di Internet
Data cabang tidak lewat jalur umum begitu saja. Tunnel membungkus paket data ke dalam paket lain, sehingga dua lokasi yang berjauhan terlihat seolah-olah terhubung langsung dengan satu kabel. 🛣️ Padahal di tengahnya ada internet publik dengan ribuan router.
🔐 Fakta 2: GRE Cepat tapi Telanjang, IPsec Aman tapi Terbatas
Dua teknologi ini punya sifat yang saling melengkapi:
Teknologi	Kelebihan	Kekurangan
🚇 GRE / IPIP	Ringan, mendukung routing dinamis, bisa membawa multicast	Tidak terenkripsi
🔒 IPsec	Enkripsi kuat	Tidak nyaman untuk routing dinamis jika dipakai sendirian
Solusinya: GRE di dalam IPsec. GRE membawa routing, IPsec mengenkripsinya. Gabungan ini menghasilkan tunnel yang aman dan tetap bisa menjalankan OSPF. 🤝✨
🧬 Fakta 3: Router Bisa "Mengobrol" Tentang Jalan Terbaik
Dengan OSPF di atas tunnel, router pusat dan cabang saling bertukar informasi rute secara otomatis. 🗣️ Admin tidak perlu menulis rute manual setiap kali ada cabang baru. Menambah cabang ke-4 sampai ke-10 cukup membuat tunnel dan mengumumkan jaringannya.
🏷️ Fakta 4: "Jalur Utama" dan "Jalur Cadangan" Diatur Lewat Angka
Di OSPF, setiap jalur punya cost. Jalur dengan cost lebih kecil dipilih sebagai jalur utama. 🧮 Dengan mengatur cost, Anda menentukan:
🥇 Tunnel lewat ISP A sebagai jalur utama.
🥈 Tunnel lewat ISP B sebagai jalur cadangan.
Saat jalur utama gagal, router berpindah sendiri ke jalur cadangan tanpa campur tangan manusia. 🤖
🔍 Fakta 5: Router Bisa "Mengecek Nadi" Jalur Internet
Fitur seperti check-gateway dan recursive routing membuat router memantau apakah gateway ISP benar-benar hidup. 💓 Ini penting karena kabel yang masih tersambung belum tentu internetnya jalan. Jika router hanya melihat "link up", ia bisa terus mengirim data ke jalur yang sebenarnya mati. 🕳️
⚡ Fakta 6: Kecepatan Berpindah Jalur Bisa Dipercepat
Ada mekanisme tambahan seperti BFD yang mendeteksi putusnya jalur jauh lebih cepat dibanding menunggu timer routing biasa. Selisih waktunya bisa menentukan apakah kasir sempat "berhenti" atau tidak. ⏱️ (Ketersediaan fitur bergantung versi RouterOS, jadi cek versi yang Anda pakai.)
👯 Fakta 7: Dua Router, Satu Alamat Gateway
Dengan VRRP, dua router di pusat berbagi satu alamat gateway virtual. 🎭 Jika router utama mati, router kedua langsung mengambil alih alamat itu, dan perangkat di belakangnya tidak menyadari apa pun.
🚦 Fakta 8: Trafik Kasir Bisa Diberi "Jalur Prioritas"
Dengan policy routing, trafik tertentu diarahkan ke jalur khusus:
💳 Kasir dan pembayaran lewat jalur paling stabil.
📹 CCTV lewat jalur kedua.
📱 WiFi pengunjung lewat jalur internet biasa.
Pelanggan yang menonton video tidak akan mengganggu transaksi kasir. 🎯
🛡️ Fakta 9: Tidak Semua Rute Boleh Masuk
Dengan route filter, router hanya menerima rute yang memang seharusnya ada. 🚫 Jika ada kesalahan konfigurasi di satu cabang, rute yang salah tidak akan menyebar ke seluruh jaringan.

🗺️ 3. Gambaran Arsitektur
                     ISP A          ISP B
                       |              |
              +--------+--------------+--------+
              |       KANTOR PUSAT (HQ)        |
              |  Router HQ-1  <-VRRP->  HQ-2   |
              |   Server POS, Gudang, Laporan  |
              +----+---------------------+-----+
                   |                     |
         Tunnel utama + cadangan   Tunnel utama + cadangan
                   |                     |
          +--------+-------+     +-------+--------+
          |  CABANG 1      |     |  CABANG 2      |
          |  Router + 2 ISP|     |  Router + 2 ISP|
          |  Kasir, CCTV   |     |  Kasir, CCTV   |
          +----------------+     +----------------+
Setiap cabang punya dua jalur ISP, dan masing-masing jalur membentuk tunnel ke pusat. Total ada tunnel utama dan tunnel cadangan untuk tiap cabang. 🔀
🧩 4. Komponen Project
Komponen	Fungsi	Analogi
🖥️ Router HQ (2 unit)	Pusat jaringan dan gateway redundan	Kantor pusat dengan dua manajer
🖥️ Router Cabang	Terhubung ke pusat lewat tunnel ganda	Kepala cabang
🚇 Tunnel GRE over IPsec	Jalur privat terenkripsi	Jalan tol khusus
🧠 OSPF	Pertukaran rute otomatis	Navigasi yang selalu diperbarui
👯 VRRP	Gateway cadangan di pusat	Kunci duplikat
🔍 Check Gateway / Recursive Route	Pemantauan kesehatan ISP	Dokter jaringan
🚦 Policy Routing	Pemisahan jalur kasir, CCTV, dan pengunjung	Pengatur lalu lintas
🛡️ Route Filter	Menyaring rute yang diterima	Petugas pemeriksa
💻 Server POS dan Gudang	Aplikasi kasir dan stok	Otak operasional
🏪 5. Cerita Penerapan: Sehari di "Kopi Nusantara" ☕🇮🇩
🌅 07.00 Tiga cabang buka. Semua kasir otomatis tersambung ke server pusat lewat tunnel.
🕛 12.30 Jam makan siang. Transaksi mencapai puncak, dan trafik kasir diprioritaskan lewat policy routing.
💥 14.15 ISP A di Cabang 2 mati. Router mendeteksi gateway tidak merespons.
🔄 14.15 (beberapa detik kemudian) Router berpindah ke tunnel cadangan lewat ISP B. OSPF memperbarui jalur secara otomatis.
💳 14.16 Kasir Cabang 2 tetap bisa memproses pembayaran. Pelanggan tidak sadar ada gangguan. 😎
📲 14.17 Admin menerima notifikasi: "ISP A Cabang 2 down, failover aktif."
🔧 15.30 ISP A pulih. Router kembali memakai jalur utama secara otomatis.
🌙 22.00 Laporan penjualan semua cabang terkumpul di pusat tanpa jeda.
Gangguan terjadi, tetapi operasional tidak berhenti. 🏆
🛠️ 6. Tahapan Pengerjaan
🔎 Analisis kebutuhan: jumlah cabang, jenis trafik, dan kebutuhan ketersediaan.
🗺️ Perancangan topologi dan skema IP (jaringan LAN tiap cabang, alamat tunnel, loopback).
🔌 Konfigurasi dasar router: interface, IP, NAT, dan DNS.
🚇 Pembuatan tunnel GRE antara pusat dan tiap cabang, masing-masing lewat dua ISP.
🔒 Pengamanan tunnel dengan IPsec.
🧠 Konfigurasi OSPF di atas interface tunnel, termasuk pengaturan cost utama dan cadangan.
🔍 Konfigurasi check-gateway dan recursive routing untuk deteksi ISP mati.
👯 Konfigurasi VRRP di router pusat.
🚦 Policy routing untuk memisahkan trafik kasir, CCTV, dan pengunjung.
🛡️ Route filtering dan autentikasi OSPF.
🧪 Pengujian failover: putus ISP, putus tunnel, dan matikan router utama.
📊 Pengukuran: waktu pindah jalur, packet loss, dan latensi.
📝 Dokumentasi dan laporan.
🧪 7. Skenario Pengujian yang Seru
Skenario	Cara Uji	Yang Diamati
💥 ISP utama cabang mati	Cabut kabel ISP A	Berapa detik tunnel berpindah ke ISP B
🔌 Tunnel putus	Nonaktifkan interface tunnel	Apakah OSPF memilih jalur cadangan
👯 Router pusat mati	Matikan HQ-1	Apakah HQ-2 mengambil alih lewat VRRP
🔁 ISP pulih	Pasang kembali kabel	Apakah jalur kembali ke utama tanpa putus
🚦 Beban trafik tinggi	Unduh file besar dari WiFi pengunjung	Apakah transaksi kasir tetap lancar
🛡️ Rute salah diumumkan	Umumkan rute palsu dari cabang	Apakah route filter menolaknya
Uji dengan ping terus-menerus lalu hitung berapa paket yang hilang saat failover. Angka inilah bukti nyata keberhasilan project Anda. 📈
⚔️ 8. Tantangan dan Solusinya
Tantangan	Risiko	Solusi
📏 Ukuran paket membesar karena enkapsulasi	Paket terpotong, koneksi lambat	Sesuaikan MTU dan MSS pada tunnel
🕳️ Link aktif tapi internet mati	Router tetap memakai jalur rusak	Check-gateway dan recursive routing
🔁 Jalur sering bolak-balik	Jaringan tidak stabil	Atur cost dan timer dengan tepat
🔐 Kunci IPsec bocor	Tunnel disadap	Kunci kuat dan ganti berkala
📡 ISP memakai CGNAT	Tunnel tidak bisa dibentuk dari luar	Pusat sebagai pihak penerima dengan IP publik
🧮 Cabang banyak	Konfigurasi manual melelahkan	Template konfigurasi dan penamaan yang konsisten
🚀 9. Pengembangan Lanjutan
🌐 Tambah cabang baru hanya dengan membuat tunnel dan mengumumkan jaringannya lewat OSPF.
🧬 WireGuard sebagai alternatif tunnel yang lebih ringan (RouterOS v7).
🧭 BGP untuk skenario franchise besar dengan banyak wilayah.
📲 Notifikasi Telegram saat failover terjadi.
📊 Dashboard monitoring untuk melihat status tunnel dan ISP secara real-time.
💾 Backup konfigurasi otomatis ke pusat.
🛰️ Jalur cadangan ketiga lewat LTE atau jaringan seluler.
🏆 10. Nilai dan Manfaat Project
🎓 Untuk pembuat project
Menunjukkan penguasaan tunnel, OSPF, VRRP, policy routing, dan route filtering dalam satu skenario.
Mudah dipresentasikan karena ceritanya nyata dan hasilnya terlihat saat failover. 🎤
🏪 Untuk pemilik franchise
Kasir tetap berjalan meski satu ISP bermasalah.
Data seluruh cabang terpusat dan real-time.
Penambahan cabang baru menjadi cepat dan terstandar.
😊 Untuk pelanggan
Pembayaran tidak tertunda dan antrean tetap lancar.
📏 11. Indikator Keberhasilan
✅ Semua cabang bisa saling ping dan mengakses server pusat lewat tunnel.
✅ Trafik tunnel terenkripsi (terbukti lewat status IPsec).
✅ Failover ISP berjalan otomatis dan waktunya terukur.
✅ Router cadangan di pusat mengambil alih saat router utama dimatikan.
✅ Trafik kasir tetap lancar saat jaringan pengunjung padat.
✅ Rute palsu ditolak oleh route filter.
✅ Ada dokumentasi topologi, skema IP, dan hasil pengujian.
