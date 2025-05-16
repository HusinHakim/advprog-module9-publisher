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