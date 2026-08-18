# GoShip — Project Overview

## 1. Konsep
GoShip adalah platform marketplace/logistics untuk mempertemukan pemilik barang dengan perusahaan/operator kapal kargo dan kapal tongkang untuk pengangkutan barang tambang, terutama batu bara. Konsep interaksi mirip marketplace seperti Gojek, tetapi objek transportasinya adalah kapal.

## 2. Prinsip Verifikasi Kapal
- Kapal dan perusahaan/operator didaftarkan melalui proses verifikasi GoShip.
- Legalitas dan data kapal diverifikasi terhadap sumber resmi pemerintah/otoritas terkait.
- GoShip tidak menerima kapal yang tidak jelas asal-usulnya.
- Hanya kapal yang memenuhi persyaratan dan berstatus available/open position yang dapat menerima request.

## 3. Request Pemilik Barang
Informasi utama request:
- Pelabuhan Asal — tempat pemuatan barang.
- Pelabuhan Tujuan — tempat pembongkaran barang.
- Jenis barang/kargo.
- Jumlah/tonase.
- Jadwal/tanggal kebutuhan kapal.
- Budget/target harga; boleh diisi atau `0`/kosong.
- Catatan kebutuhan.
- Dokumen pendukung/cargo documents bila diperlukan.

Budget > 0 berarti pemilik barang memberikan target harga dan operator dapat menerima atau melakukan counter-offer. Budget 0/kosong berarti operator menentukan harga penawarannya sendiri.

## 4. Matching Kapal
Matching mempertimbangkan antara lain:
- availability/open position;
- posisi kapal terhadap Pelabuhan Asal;
- radius jangkauan;
- kapasitas kapal;
- jenis kapal dan kesesuaian cargo;
- jadwal/kesiapan kapal.

Request dikirim kepada **semua kapal/operator yang eligible hasil matching**, bukan satu per satu secara bergiliran. Untuk prototype jumlah kandidat dapat dibatasi berdasarkan skor matching.

## 5. Dual Approval Pihak Kapal
Sebelum kapal/operator boleh mengirim Offer atau Counter Offer, **nahkoda dan kantor/operator kapal sama-sama harus ACC**.

| Nahkoda | Kantor | Boleh Offer/Counter Offer |
|---|---|---|
| ACC | ACC | Ya |
| ACC | Tidak ACC | Tidak |
| Tidak ACC | ACC | Tidak |
| Tidak ACC | Tidak ACC | Tidak |

ACC ini **bukan penerimaan order**. ACC hanya berarti pihak kapal bersedia mengikuti proses penawaran. Order baru terikat setelah pemilik barang memilih Offer dan proses Booking/Order dilanjutkan.

Kantor/operator merupakan pihak yang memiliki kewenangan akhir sebagai pihak yang bertanggung jawab atas kapal, tetapi persetujuan nahkoda tetap wajib. Kantor tidak dapat memaksa kapal yang nahkodanya tidak bersedia, dan nahkoda tidak dapat mengajukan penawaran tanpa persetujuan kantor.

## 6. Penawaran dan Pemilihan
Satu request dapat menerima beberapa penawaran dari perusahaan kapal. Pemilik barang menjadi pihak yang memilih penawaran/operator.

Isi minimal Offer:
- kapal yang ditawarkan;
- harga;
- ETA ke Pelabuhan Asal;
- estimasi waktu tiba Pelabuhan Tujuan;
- estimasi durasi perjalanan;
- kapasitas muatan;
- status/keterangan kesiapan kapal;
- informasi teknis relevan.

Pemilik barang tidak wajib memilih harga termurah. Perbandingan dapat mempertimbangkan harga, reputasi, ETA, kapasitas, posisi kapal, performa, dan kelengkapan dokumen.

Alur inti:
`Request → Matching → Dual Approval → Offer/Counter Offer → Selection → Booking/Order → Escrow → Delivery → Release`

## 7. Status Kapal dan Tracking
Status posisi/perjalanan kapal sebisa mungkin diperbarui otomatis melalui GPS/AIS/tracking provider dan API resmi bila tersedia. Untuk prototype, tracking dapat disimulasikan.

Tracking dapat membantu menentukan posisi kapal, apakah kapal bergerak atau berada di area pelabuhan, ETA, dan status/availability berdasarkan aturan bisnis.

Status aktivitas seperti **Sedang Muat**, **Sedang Bongkar**, **Selesai Muat**, dan **Selesai Bongkar** tidak cukup ditentukan oleh GPS/AIS dan memerlukan input pihak kapal.

Workflow status manual:
1. Nahkoda/pihak kapal mengajukan perubahan status.
2. Status menjadi Pending Approval.
3. Admin kantor/operator menerima notifikasi.
4. Admin melakukan review/konfirmasi, termasuk pengecekan lapangan bila diperlukan.
5. Jika disetujui, status menjadi Approved/Verified dan tampil kepada pemilik barang.
6. Jika ditolak, status dikembalikan untuk koreksi atau tetap pada status sebelumnya.

Setiap perubahan status penting memiliki audit trail: timestamp, pengaju, approver, status sebelum/sesudah, dan catatan bila diperlukan.

## 8. Escrow
GoShip menggunakan konsep escrow untuk mengurangi risiko pembayaran pemilik barang dan risiko operator bekerja tanpa kepastian pembayaran.

Setelah Offer dipilih dan deal terjadi:
1. Sistem membuat Order/Checkout.
2. Pemilik barang diberi batas waktu pembayaran ke mekanisme escrow.
3. Transaksi belum aktif/confirmed sebelum dana berhasil diterima dan escrow terkonfirmasi.
4. Setelah dana terkonfirmasi, order menjadi Paid/Confirmed dan kapal dapat menjalankan pekerjaan.
5. Setelah delivery memenuhi milestone, escrow dapat dilepas kepada operator.

Untuk prototype, batas awal pembayaran adalah **1 x 24 jam sejak checkout/deal**. Parameter sebaiknya configurable dan dapat dikembangkan menjadi 24–48 jam untuk kebutuhan B2B.

Jika dana tidak diterima sampai batas waktu, Order menjadi Expired/Cancelled sesuai aturan availability kapal.

### 8.1 Mekanisme Escrow Production
GoShip tidak boleh menganggap rekening operasional perusahaan sebagai escrow hanya karena dana ditampung sementara. Untuk production, mekanisme penampungan dan settlement harus menggunakan bank/PJP/payment provider yang secara legal menyediakan mekanisme yang sesuai. Detail legal, KYC/AML, settlement, dan perlindungan dana harus divalidasi sebelum production.

## 9. Cargo dan Batas Tanggung Jawab
GoShip adalah facilitator/platform pengangkutan, bukan quality inspector cargo.

Kualitas atau spesifikasi komersial barang, misalnya kadar/grade batu bara, warna/kematangan, ukuran, kualitas mineral, atau kesesuaian barang dengan kontrak jual beli, pada dasarnya merupakan ranah **penjual dan pembeli**, bukan tanggung jawab komersial kapal atau GoShip.

GoShip tidak perlu datang ke lokasi untuk memeriksa kualitas barang hanya berdasarkan deklarasi seller.

Namun kapal/operator tetap wajib mengetahui informasi cargo yang diperlukan untuk keselamatan, legalitas, manifest, dokumen pengangkutan, kapasitas, penanganan muatan, dan persyaratan pelabuhan/otoritas. Untuk cargo tertentu, sifat/klasifikasi muatan juga dapat wajib diketahui.

Prinsipnya:
> Kapal bertanggung jawab atas jasa pengangkutan serta proses muat/bongkar sesuai order dan kewajiban keselamatan/legalitasnya. Kapal tidak bertanggung jawab atas kualitas atau nilai komersial barang di luar kewajiban carrier yang secara eksplisit disepakati dan diwajibkan hukum.

## 10. Dokumen Pengiriman dan Bukti Delivery
**Dokumen pengiriman merupakan bagian wajib dari evidence transaksi**, bukan sekadar informasi tambahan.

Dokumen yang relevan dengan proses pengiriman harus dapat **di-upload ke aplikasi GoShip** dan dikaitkan dengan Order/Trip. Jenis dokumen ditentukan berdasarkan jenis cargo, proses bisnis, dan persyaratan operasional/legal.

Khusus penyelesaian delivery:
- Status **Selesai Bongkar** tidak cukup hanya dengan menekan tombol selesai.
- Pihak yang berwenang harus meng-upload dokumen/bukti penyelesaian bongkar dan/atau Proof of Delivery/serah-terima yang relevan.
- Sistem menyimpan dokumen tersebut sebagai evidence transaksi.
- Setelah evidence lengkap dan milestone delivery terpenuhi, order masuk proses verifikasi/release sesuai aturan escrow.

Alur delivery prototype:
`Tiba Pelabuhan Tujuan → Bongkar → Selesai Bongkar → Upload Dokumen/POD → Verifikasi Delivery → Dispute Window → Escrow Release`

GoShip menyimpan audit trail untuk upload dokumen: siapa yang upload, waktu upload, jenis dokumen, status verifikasi, dan bila diperlukan siapa yang memverifikasi.

## 11. Data Master Perusahaan dan Kapal
### 11.1 Perusahaan/Operator
- nama perusahaan/PT;
- NIB;
- NPWP;
- alamat;
- nomor telepon;
- email;
- PIC;
- status verifikasi GoShip;
- status aktif/nonaktif.

### 11.2 Kapal
- nama kapal;
- IMO Number bila ada;
- MMSI;
- Call Sign;
- jenis kapal;
- kapasitas/DWT;
- panjang, lebar, draft;
- tahun pembuatan;
- bendera;
- pelabuhan pendaftaran;
- kecepatan ekonomis/maksimum;
- jenis cargo yang dapat diangkut;
- foto kapal.

### 11.3 Dokumen/Legalitas Kapal
- jenis dokumen;
- nomor dokumen;
- tanggal terbit;
- tanggal berlaku/kedaluwarsa;
- status verifikasi;
- tanggal verifikasi;
- petugas/verifikator.

### 11.4 Data Operasional
Dipisahkan dari master kapal:
- status kapal;
- posisi;
- pelabuhan/lokasi terakhir;
- tujuan perjalanan;
- ETA;
- jadwal available berikutnya;
- keterangan operasional.

## 12. Multi-Level Operator
Satu perusahaan dapat memiliki banyak kapal. Sistem membedakan:
- perusahaan/operator;
- armada/kapal;
- user kantor pusat/operator;
- user kapal/nahkoda/crew yang diberi kewenangan.

Notifikasi dan approval dapat melibatkan kantor maupun kapal sesuai role.

## 13. Nilai Utama GoShip
Nilai strategis GoShip adalah membangun database armada dan availability kapal yang terverifikasi sehingga pemilik barang dapat menemukan kapasitas angkutan yang terpercaya secara cepat dan transparan.

## 14. Roadmap Potensial
- tracking posisi kapal/AIS;
- status kapal real-time;
- kontrak digital;
- e-signature;
- pembayaran/escrow;
- rating/review;
- dashboard operasional kantor;
- histori order dan perjalanan;
- verifikasi dokumen dan masa berlaku;
- matching/rekomendasi kapal.

> Catatan: angka harga dan tarif dalam contoh pembicaraan adalah ilustrasi. Satuan tarif final (per ton, per trip, dll.) harus ditentukan dalam desain bisnis sebelum implementasi production.
