# Jawaban Pertanyaan

## a. Berapa banyak data yang dikirim program publisher ke message broker dalam satu kali eksekusi?

Program publisher mengirimkan 5 pesan ke message broker dalam satu kali eksekusi. Setiap pesan berisi objek `UserCreatedEventMessage` yang memiliki dua field string: `user_id` dan `user_name`. Program mengirimkan data untuk 5 user berbeda (Amir, Budi, Cica, Dira, dan Emir) dengan ID 1 sampai 5.

## b. URL "amqp://guest:guest@localhost:5672" sama dengan yang ada di program subscriber, apa artinya?

URL koneksi "amqp://guest:guest@localhost:5672" yang sama antara publisher dan subscriber menunjukkan bahwa:

- Kedua program terhubung ke message broker (RabbitMQ) yang sama
- Keduanya menggunakan server RabbitMQ yang berjalan di komputer lokal (localhost)
- Keduanya menggunakan port standar AMQP (5672)
- Keduanya menggunakan kredensial yang sama (username: guest, password: guest)

Ini memungkinkan komunikasi tidak langsung antara publisher dan subscriber melalui message broker yang sama. Publisher mengirim pesan ke broker, dan subscriber menerima pesan dari broker yang sama, sehingga menciptakan sistem komunikasi asinkron yang terdekopel.

## Screenshot RabbitMQ yang Berjalan

### Interface Management RabbitMQ
![RabbitMQ Management Interface](images/chrome_Fst0z3jVGw.png)

Screenshot ini menunjukkan halaman overview RabbitMQ Management Interface yang diakses melalui browser pada alamat localhost:15672. Terlihat bahwa server RabbitMQ versi 3.13.7 telah berhasil dijalankan dan dapat diakses menggunakan kredensial default (username: guest, password: guest). Interface ini memungkinkan kita untuk memonitor dan mengelola pesan, koneksi, channel, dan queue pada message broker.

### Terminal Saat Menjalankan Subscriber
![Subscriber Terminal](images/Cursor_2K9WOIHBhs.png)

Screenshot ini menampilkan terminal saat menjalankan program subscriber dengan perintah `cargo run`. Output menunjukkan beberapa peringatan terkait variabel yang tidak digunakan (`thread`, `ten_millis`, `now`). Yang paling penting, terlihat bahwa subscriber berhasil menerima dan memproses pesan dari RabbitMQ. Pesan yang diterima adalah event `UserCreatedEventMessage` dengan data user yang telah dikirim oleh publisher, termasuk id dan nama pengguna. Pesan ditampilkan "In Husin's Computer [2306152481]".

### Terminal Saat Menjalankan Publisher
![Publisher Terminal](images/Cursor_hf8ubWAXVO.png)

Screenshot ini menunjukkan terminal yang digunakan untuk menjalankan program publisher dengan perintah `cargo run` beberapa kali. Output menampilkan proses kompilasi dan eksekusi program publisher. Program berjalan dengan sukses, meskipun ada beberapa peringatan terkait kompatibilitas paket nom v4.2.3 dengan versi Rust di masa depan. Tidak ada output yang menampilkan detail pesan yang dikirim karena publisher tidak mencetak informasi tersebut ke terminal, tetapi program telah berhasil mengirim 5 pesan UserCreatedEventMessage ke RabbitMQ.

### Grafik Message Rates di RabbitMQ Management
![Message Rates Chart](images/chrome_fGZ8M5lGnG.png)

Screenshot ini menampilkan grafik "Message rates" pada RabbitMQ Management Interface yang menunjukkan dua lonjakan (spike) pada grafik yang berwarna ungu. Lonjakan ini terjadi karena program publisher dijalankan dua kali secara berurutan. Setiap kali program publisher dijalankan, program tersebut mengirim 5 pesan ke message broker dalam waktu yang sangat singkat, sehingga menyebabkan lonjakan pada grafik throughput pesan.

Grafik ungu ini mewakili "Consumer ack", yang berarti bahwa pesan-pesan yang dikirim oleh publisher berhasil diterima dan di-acknowledge oleh consumer (dalam hal ini, program subscriber). Tinggi dari masing-masing lonjakan menunjukkan kecepatan/rate pemrosesan pesan per detik. Hal ini membuktikan bahwa komunikasi asinkron antara publisher dan subscriber melalui RabbitMQ berhasil dilakukan dengan baik, dan memvisualisasikan bagaimana message broker menangani burst traffic ketika publisher mengirimkan banyak pesan dalam waktu singkat.

### Simulasi Antrian Pesan saat Subscriber Lambat
![Queued Messages](images/chrome_e6RnKd8kAd.png)


Pada simulasi ini, subscriber dibuat lambat dengan menambahkan delay 1 detik untuk setiap proses. Hasilnya dapat dilihat pada screenshot di atas. Hal ini menunjukkan bahwa producer dapat terus mengirim permintaan, dan permintaan tersebut (sebagai event) ditempatkan dalam antrian pesan. Secara perlahan, consumer akan memprosesnya satu per satu.

Pada grafik "Queued messages", terlihat lonjakan jumlah pesan yang tertunda dalam antrian (36 pesan) yang belum diproses (Unacked). Grafik "Message rates" menunjukkan garis merah yang tinggi, yang merepresentasikan tingkat publish yang tinggi, sementara garis ungu (consumer ack) tetap rendah karena subscriber memproses pesan dengan lambat. Ini adalah contoh sempurna bagaimana message broker dapat berfungsi sebagai buffer antara sistem yang memiliki kecepatan pemrosesan berbeda, mencegah kehilangan data dan kegagalan sistem saat terjadi lonjakan beban. 