# GoShip — Project Overview

## 1. Konsep
GoShip adalah platform marketplace/logistics untuk mempertemukan pemilik barang dengan perusahaan/operator kapal kargo dan kapal tongkang. Konsep interaksi mirip marketplace seperti Gojek, tetapi objek transportasinya adalah kapal untuk pengangkutan barang tambang, terutama batu bara.

## 2. Prinsip Utama
- GoShip tidak menerima kapal yang tidak jelas asal-usulnya.
- Kapal dan perusahaan/operator didaftarkan melalui proses verifikasi.
- Legalitas dan data kapal diverifikasi terhadap sumber resmi pemerintah/otoritas terkait.
- Hanya kapal yang memenuhi persyaratan dan berstatus available/open position yang dapat menerima order.

## 3. Sisi Pemilik Barang
Pemilik barang membuat kebutuhan pengiriman dengan informasi utama:
- **Pelabuhan Asal** — pelabuhan tempat pemuatan barang.
- **Pelabuhan Tujuan** — pelabuhan tempat pembongkaran barang.
- **Jenis barang/kargo** — misalnya batu bara, nikel, pasir, atau barang tambang lainnya.
- **Jumlah/tonase** — jumlah muatan yang akan diangkut.
- **Tanggal/jadwal muat** — kapan kapal dibutuhkan untuk melakukan pemuatan.
- **Budget/target harga** — boleh diisi dengan nilai tertentu atau `0`/kosong bila pemilik barang ingin menerima penawaran harga dari operator kapal.
- **Catatan kebutuhan** — informasi tambahan yang relevan dengan pengiriman.
- **Dokumen pendukung** — opsional pada prototype, dapat dikembangkan untuk dokumen cargo/order.

### 3.1 Aturan Budget
Terdapat dua mode request:

**Budget > 0**
- Pemilik barang memberikan target harga.
- Operator kapal dapat menerima harga tersebut atau memberikan counter-offer.

**Budget = 0 / kosong**
- Pemilik barang tidak menentukan harga.
- Operator kapal yang memenuhi syarat dapat menentukan harga penawarannya sendiri.
- Pemilik barang membandingkan beberapa penawaran dan memilih yang paling sesuai.

### 3.2 Prinsip Asal dan Tujuan
Untuk kebutuhan pengangkutan laut, field UI dan model bisnis menggunakan istilah **Pelabuhan Asal** dan **Pelabuhan Tujuan**, bukan sekadar "Asal" dan "Tujuan". Ini menjadi keputusan penting karena matching kapal dilakukan terhadap pelabuhan muat/bongkar dan radius posisi kapal dari pelabuhan asal.

## 4. Matching Kapal
Sistem mencari kapal yang sesuai berdasarkan antara lain:
- status availability/open position;
- posisi/lokasi kapal terhadap **Pelabuhan Asal**;
- radius jangkauan dari pelabuhan asal;
- kapasitas kapal;
- jenis kapal dan kesesuaian dengan kargo;
- jadwal/kesiapan kapal.

Order yang cocok dikirim sebagai notifikasi kepada **semua kapal/operator yang eligible hasil matching**, bukan satu per satu secara bergiliran. Untuk prototype, jumlah kandidat dapat dibatasi (misalnya 10 kandidat terbaik) berdasarkan skor matching agar notifikasi tetap relevan.

Jenis kapal tidak harus selalu dipilih manual oleh pemilik barang. GoShip dapat menentukan kapal yang compatible berdasarkan kebutuhan muatan dan parameter matching. Untuk prototype, aturan matching dapat dibuat sederhana terlebih dahulu.

## 5. Mekanisme Penawaran
Beberapa perusahaan kapal dapat merespons kebutuhan pengiriman yang sama.

Contoh:
- Pemilik barang memasang target Rp200.000/ton.
- Perusahaan A menawarkan Rp250.000/ton.
- Pemilik barang dapat menerima atau melakukan counter-offer, misalnya Rp225.000/ton.
- Perusahaan lain yang memenuhi syarat juga dapat memberikan penawaran.

Model yang disarankan adalah **tender/RFQ singkat**, bukan percakapan tawar-menawar tanpa batas.

### 5.1 Pemilihan Penawaran
Satu request dapat menerima beberapa penawaran dari operator kapal. **Pemilik barang menjadi pihak yang menentukan penawaran/operator yang dipilih.**

Pemilik barang tidak wajib memilih harga paling murah. Perbandingan dapat mempertimbangkan harga, rating/reputasi, ETA, kapasitas, posisi kapal, performa, dan faktor lainnya.

Setelah pemilik barang memilih salah satu penawaran, proses dilanjutkan ke **Booking/Order**.

Alur inti prototype:

`Request → Matching → Bidding/Offer → Selection → Booking/Order`

## 6. Dasar Pemilihan
Pemilihan kapal/operator sebaiknya tidak hanya berdasarkan harga. Faktor yang dapat ditampilkan:
- harga penawaran;
- reputasi/rating perusahaan;
- riwayat perjalanan/order sukses;
- performa ketepatan waktu;
- kapasitas kapal;
- posisi kapal saat ini;
- estimasi waktu tiba/availability;
- status dan kelengkapan dokumen.

## 7. Multi-Level Operator
Satu perusahaan dapat memiliki banyak kapal. Karena itu sistem perlu membedakan:
- perusahaan/operator kapal;
- armada/kapal milik atau dikelola perusahaan;
- user kantor pusat/operator;
- user kapal/nahkoda atau crew yang diberi kewenangan.

Notifikasi dan proses approval dapat melibatkan kantor pusat maupun user kapal sesuai aturan perusahaan.

## 8. Fitur Pengembangan Lanjutan
Roadmap awal yang potensial:
- tracking posisi kapal/AIS;
- status kapal real-time;
- kontrak digital;
- e-signature;
- pembayaran dan escrow;
- rating/review perusahaan dan kapal;
- dashboard operasional kantor pusat;
- histori order dan perjalanan;
- verifikasi dokumen dan masa berlaku dokumen;
- sistem matching dan rekomendasi kapal.

## 9. Nilai Utama GoShip
Nilai strategis GoShip bukan sekadar mempertemukan pemilik barang dan kapal, tetapi membangun **database armada dan availability kapal yang terverifikasi** sehingga pemilik barang dapat menemukan kapasitas angkutan yang terpercaya secara lebih cepat dan transparan.

> Catatan: angka harga dalam contoh di atas hanya ilustrasi. Satuan tarif (per ton, per kg, per trip, dll.) harus ditentukan secara eksplisit dalam desain bisnis dan sistem sebelum implementasi.
