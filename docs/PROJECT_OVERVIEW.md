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
- lokasi/pelabuhan asal;
- lokasi/pelabuhan tujuan;
- jenis barang/kargo;
- jumlah/tonase;
- jadwal atau tanggal kebutuhan muat;
- budget/target harga, yang boleh dikosongkan (Rp 0) bila pemilik barang ingin menerima penawaran pasar.

## 4. Matching Kapal
Sistem mencari kapal yang sesuai berdasarkan antara lain:
- status availability/open position;
- posisi/lokasi kapal terhadap pelabuhan asal;
- radius jangkauan dari lokasi muat;
- kapasitas kapal;
- jenis kapal dan kesesuaian dengan kargo;
- jadwal/kesiapan kapal.

Order yang cocok dikirim sebagai notifikasi kepada perusahaan/operator kapal dan, bila diperlukan, nahkoda/pihak kapal yang berwenang.

## 5. Mekanisme Penawaran
Beberapa perusahaan kapal dapat merespons kebutuhan pengiriman yang sama.

Contoh:
- Pemilik barang memasang target Rp200.000/ton.
- Perusahaan A menawarkan Rp250.000/ton.
- Pemilik barang dapat menerima atau melakukan counter-offer, misalnya Rp225.000/ton.
- Perusahaan lain yang memenuhi syarat juga dapat memberikan penawaran.

Model yang disarankan adalah **tender/RFQ singkat**, bukan percakapan tawar-menawar tanpa batas. Pemilik barang kemudian membandingkan dan memilih penawaran terbaik.

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
