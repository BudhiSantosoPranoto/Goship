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

### 4.1 Dual Approval Pihak Kapal Sebelum Offer
Ketika sebuah request dikirim ke pihak kapal, **nahkoda dan kantor/operator kapal sama-sama harus memberikan ACC** sebelum kapal/perusahaan dapat mengirim Offer atau Counter Offer kepada pemilik barang.

Aturan:
- Nahkoda **ACC** + Kantor/operator **ACC** → **boleh mengirim Offer/Counter Offer**.
- Nahkoda **ACC** + Kantor/operator **tidak ACC** → tidak boleh Offer/Counter Offer.
- Nahkoda **tidak ACC** + Kantor/operator **ACC** → tidak boleh Offer/Counter Offer.
- Keduanya tidak ACC → tidak boleh Offer/Counter Offer.

ACC pada tahap ini **bukan berarti menerima order**. ACC hanya berarti pihak kapal bersedia mengikuti proses penawaran. Order baru menjadi terpilih/terikat setelah pemilik barang memilih Offer dan proses Booking/Order dilakukan.

Secara otoritas, kantor/operator kapal merupakan pihak yang memiliki kewenangan akhir sebagai pihak yang bertanggung jawab atas kapal, tetapi persetujuan nahkoda tetap wajib. Dengan demikian, kantor tidak dapat memaksa kapal yang nahkodanya tidak bersedia, dan nahkoda tidak dapat mengajukan penawaran tanpa persetujuan kantor.

### 4.2 Status Kapal dan Tracking
Status operasional kapal sebaiknya **sebisa mungkin diperbarui otomatis** berdasarkan data posisi/tracking kapal, bukan bergantung sepenuhnya pada input manual nahkoda atau kantor pusat.

GoShip direncanakan dapat mengintegrasikan sumber data GPS/AIS/tracking kapal melalui API atau mekanisme integrasi resmi yang tersedia dari perangkat/provider tracking kapal. Ketersediaan API dan hak akses harus diverifikasi per provider sebelum implementasi production.

Data tracking dapat digunakan untuk membantu menentukan:
- posisi kapal saat ini;
- apakah kapal sedang bergerak atau berada di area pelabuhan;
- estimasi waktu tiba (ETA);
- status/availability operasional berdasarkan aturan bisnis GoShip.

Untuk prototype, tracking dan perubahan status dapat disimulasikan terlebih dahulu.

### 4.3 Status Operasional yang Memerlukan Konfirmasi
Tidak semua status dapat ditentukan hanya dari GPS/AIS. Status aktivitas seperti **Sedang Muat**, **Sedang Bongkar**, **Selesai Muat**, atau **Selesai Bongkar** memerlukan input dari pihak kapal dan konfirmasi operator/perusahaan.

Workflow status operasional:
1. **Nahkoda/pihak kapal** mengajukan perubahan status.
2. Sistem membuat status menjadi **Pending Approval**.
3. **Admin kantor/operator kapal** menerima notifikasi.
4. Admin melakukan review/konfirmasi, termasuk pengecekan lapangan bila diperlukan.
5. Jika disetujui, status menjadi **Approved/Verified** dan dipublikasikan kepada pemilik barang.
6. Jika ditolak, status dikembalikan untuk koreksi atau tetap pada status sebelumnya.

Pemilik barang hanya melihat status operasional sebagai status resmi setelah mendapat approval dari operator.

Setiap perubahan status penting harus memiliki **audit trail**, minimal berupa timestamp, pihak yang mengajukan perubahan, pihak yang menyetujui/menolak, status sebelum dan sesudah, serta catatan bila diperlukan.

Jenis kapal tidak harus selalu dipilih manual oleh pemilik barang. GoShip dapat menentukan kapal yang compatible berdasarkan kebutuhan muatan dan parameter matching. Untuk prototype, aturan matching dapat dibuat sederhana terlebih dahulu.

## 5. Mekanisme Penawaran
Beberapa perusahaan kapal dapat merespons kebutuhan pengiriman yang sama.

Contoh:
- Pemilik barang memasang target Rp200.000/ton.
- Perusahaan A menawarkan Rp250.000/ton.
- Pemilik barang dapat menerima atau melakukan counter-offer, misalnya Rp225.000/ton.
- Perusahaan lain yang memenuhi syarat juga dapat memberikan penawaran.

Model yang disarankan adalah **tender/RFQ singkat**, bukan percakapan tawar-menawar tanpa batas.

### 5.1 Isi Respon/Penawaran Pihak Kapal
Respon operator kapal terhadap request tidak hanya berisi harga. Minimal prototype perlu memuat:
- **Kapal** yang ditawarkan;
- **Harga penawaran**;
- **ETA ke Pelabuhan Asal** — perkiraan waktu kapal tiba untuk melakukan pemuatan;
- **Estimasi waktu tiba di Pelabuhan Tujuan**;
- **Estimasi durasi perjalanan**;
- **Kapasitas muatan kapal**;
- **Status/keterangan kesiapan kapal**;
- informasi teknis yang relevan bila diperlukan.

Data teknis kapal seperti kecepatan ekonomis dapat menjadi data master kapal. Operator memberikan estimasi waktu berdasarkan kondisi aktual kapal, rute, dan kesiapan operasional.

Dengan demikian, penawaran menggambarkan **harga + kemampuan/kapasitas + kesiapan + estimasi waktu**, bukan sekadar harga.

### 5.2 Pemilihan Penawaran
Satu request dapat menerima beberapa penawaran dari operator kapal. **Pemilik barang menjadi pihak yang menentukan penawaran/operator yang dipilih.**

Pemilik barang tidak wajib memilih harga paling murah. Perbandingan dapat mempertimbangkan harga, rating/reputasi, ETA, kapasitas, posisi kapal, performa, dan faktor lainnya.

Setelah pemilik barang memilih salah satu penawaran, proses dilanjutkan ke **Booking/Order**.

Alur inti prototype:

`Request → Matching → Dual Approval → Bidding/Offer → Selection → Booking/Order`

## 6. Data Master Perusahaan dan Kapal
### 6.1 Perusahaan/Operator
Data utama perusahaan:
- nama perusahaan/PT;
- nama singkat;
- NIB;
- NPWP;
- alamat;
- nomor telepon;
- email;
- PIC dan nomor HP PIC;
- status verifikasi GoShip;
- status aktif/nonaktif.

### 6.2 Kapal
Data master kapal yang disiapkan minimal untuk prototype:
- nama kapal;
- IMO Number (bila ada);
- MMSI;
- Call Sign;
- jenis kapal;
- kapasitas muatan;
- DWT;
- panjang, lebar, dan draft;
- tahun pembuatan;
- bendera kapal;
- pelabuhan pendaftaran;
- kecepatan ekonomis;
- kecepatan maksimum;
- jenis cargo yang dapat diangkut;
- foto kapal.

### 6.3 Legalitas/Dokumen
Data dokumen kapal dapat mencakup:
- jenis dokumen;
- nomor dokumen;
- tanggal terbit;
- tanggal berlaku/kedaluwarsa;
- status verifikasi;
- tanggal verifikasi;
- petugas/verifikator.

Data dokumen menjadi bagian penting dari proses verifikasi kapal GoShip.

### 6.4 Data Operasional
Data operasional dipisahkan dari master kapal karena nilainya berubah:
- status kapal (`Available`, `On Trip`, `Loading`, `Unloading`, `Maintenance`, `Inactive`, dll.);
- posisi kapal;
- pelabuhan/lokasi terakhir;
- tujuan perjalanan saat ini;
- ETA;
- jadwal available berikutnya;
- keterangan operasional.

Status dan posisi diharapkan dapat diperbarui otomatis melalui integrasi tracking jika tersedia; input manual dapat menjadi fallback/override dengan aturan audit yang jelas.

## 7. Dasar Pemilihan
Pemilihan kapal/operator sebaiknya tidak hanya berdasarkan harga. Faktor yang dapat ditampilkan:
- harga penawaran;
- reputasi/rating perusahaan;
- riwayat perjalanan/order sukses;
- performa ketepatan waktu;
- kapasitas kapal;
- posisi kapal saat ini;
- estimasi waktu tiba/availability;
- status dan kelengkapan dokumen.

## 8. Multi-Level Operator
Satu perusahaan dapat memiliki banyak kapal. Karena itu sistem perlu membedakan:
- perusahaan/operator kapal;
- armada/kapal milik atau dikelola perusahaan;
- user kantor pusat/operator;
- user kapal/nahkoda atau crew yang diberi kewenangan.

Notifikasi dan proses approval dapat melibatkan kantor pusat maupun user kapal sesuai aturan perusahaan.

## 9. Fitur Pengembangan Lanjutan
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

## 10. Nilai Utama GoShip
Nilai strategis GoShip bukan sekadar mempertemukan pemilik barang dan kapal, tetapi membangun **database armada dan availability kapal yang terverifikasi** sehingga pemilik barang dapat menemukan kapasitas angkutan yang terpercaya secara lebih cepat dan transparan.

> Catatan: angka harga dalam contoh di atas hanya ilustrasi. Satuan tarif (per ton, per kg, per trip, dll.) harus ditentukan secara eksplisit dalam desain bisnis dan sistem sebelum implementasi.
