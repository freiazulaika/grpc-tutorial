# Module 8 Advanced Programming

> What are the key differences between unary, server streaming, and bi-directional streaming
RPC (Remote Procedure Call) methods, and in what scenarios would each be most suitable?

Secara garis besar, ketiga metode tersebut dapat dibedakan berdasarkan cara komunikasi antara client dengan server. Unary menggunakan alur satu permintaan client dan satu respons server sehingga cocok untuk operasi CRUD sederhana misalnya seperti ketika client membuka halaman homepage e-commerce dan server akan menampilkan halaman tersebut secara statis. Server streaming bekerja ketika client mengirimkan satu permintaan dan server mengirim aliran respons berkelanjutan terhadap satu permintaan klien. Server streaming cocok digunakan untuk melakukan monitoring secara real-time, notifikasi, atau pengunduhan data besar. Bi-directional streaming bekerja dengan kedua pihak mengirim pesan secara independen dan bersamaan dalam satu koneksi. Jenis ini cocok untuk aplikasi interaktif seperti chat, game online, atau sistem kolaborasi yang membutuhkan komunikasi dua arah.

> What are the potential security considerations involved in implementing a gRPC service in Rust, particularly regarding authentication, authorization, and data encryption?

Untuk mengimplementasikan gRPC di Rust terutama dalam ketiga hal tersebut, kita harus memperhatikan keamanannya. Pada autentikasi, kita bisa menggunakan mTLS dengan menggunakan `tonic` melalui `rustls` atau `openssl`. Autentikasi juga dapat memanfaatkan token JWT atau OAuth2. Dalam otorisasi, kita dapat menggunakan sistem RBAC untuk membatasi akses ke tingkat-tingkat tertentu sesuai dengan jenis pengguna tersebut. Enkripsi data dapat diterapkan dengan TLS/SSL yang bisa dikonfigurasikan menggunakan crate seperti `rustls`. 

> What are the potential challenges or issues that may arise when handling bidirectional streaming in Rust gRPC, especially in scenarios like chat applications? 

Permasalahan yang dapat muncul dalam bidirectional streaming yaitu terkait dengan sinkronisasi dalam alur pengiriman. Ketika terjadi error di salah satu pihak, maka pihak lain akan tetap terus berjalan.  Hal ini dapat menyebabkan data yang tidak konsisten dan kesulitan untuk pengelolaan status koneksi. Untuk aplikasi chat, salah satu tantangan yang dimiliki termasuk penanganan pesan yang terlewat saat koneksi terputus sementara dan kesulitan memastikan urutan pesan yang benar.

> What are the advantages and disadvantages of using the tokio_stream::wrappers::ReceiverStream for streaming responses in Rust gRPC services? 

Keuntungan penggunaan `tokio_stream::wrappers::ReceiverStream` adalah memudahkan pengiriman data streaming karena menyediakan cara yang sederhana untuk mengubah channel `Tokio` menjadi format stream yang sesuai dengan gRPC. Sehingga, pengembang tidak perlu memikirkan proses teknis koneksi lebih dalam. Kekurangannya adalah kerumitan tambahan dalam kode. Selain itu, penggunaan `ReceiverStream` bisa membuat debugging lebih sulit karena beberapa proses berjalan secara asinkron.

> In what ways could the Rust gRPC code be structured to facilitate code reuse and modularity, promoting maintainability and extensibility over time?

Struktur kode gRPC Rust yang baik dapat diterapkan dengan:
- Memisahkan definisi layanan (proto) dari implementasinya dalam modul-modul terpisah. Sehingga, pengembang dapat melakukan perubahan tanpa memengaruhi interface yang digunakan klien. 
- Menggunakan trait untuk fungsionalitas umum sehingga berbagai layanan dapat menggunakan kode yang sama untuk operasi seperti logging atau validasi.
- Menyusun struktur direktori yang logis seperti memisahkan client, server, dan kode yang dibagikan ke dalam modul terpisah.

> In the MyPaymentService implementation, what additional steps might be necessary to handle more complex payment processing logic?

Dalam implementasi `MyPaymentService` yang lebih lengkap, kita dapat menambahkan integrasi dengan third-party payment gateway untuk memproses transaksi secara aman dan efisien. Selain itu, perlu juga meng-handle penyimpanan data transaksi dalam database yang mudah diakses sehingga status transaksi mudah dilacak. 

> What impact does the adoption of gRPC as a communication protocol have on the overall architecture and design of distributed systems, particularly in terms of interoperability with other technologies and platforms?

gRPC sebagai protokol komunikasi mengubah arsitektur sistem terdistribusi dengan menggunakan pendekatan berbasis kontrak dengan Protocol Buffers. Penggunaan pendekatan ini meningkatkan efisiensi komunikasi dengan adanya serialisasi biner dan kompresi dibandingkan REST/JSON. gRPC juga menyediakan dukungan multilanguage sehingga program-program dalam berbagai bahasa tetap bisa berkomunikasi dengan baik.

> What are the advantages and disadvantages of using HTTP/2, the underlying protocol for gRPC, compared to HTTP/1.1 or HTTP/1.1 with WebSocket for REST APIs?

Keuntungan HTTP/2:
- Multiplexing: banyak permintaan dan respons berjalan secara paralel dalam satu koneksi TCP
- Kompresi header (HPACK): mengurangi ukuran data yang dikirim, lebih hemat bandwidth, dan lebih cepat.
- Server Push: server bisa kirim resource penting lebih awal tanpa menunggu permintaan klien.
- Faster Page Loads: penggunaan satu koneksi TCP dan lebih sedikit round trip mempercepat loading halaman.

Kekurangan HTTP/2:
- Head-of-Line Blocking (TCP level): jika satu paket terlambat, bisa menunda paket lainnya dalam koneksi yang sama.
- Kompatibilitas Terbatas: tidak semua sistem lama mendukung HTTP/2 dengan baik.
- Caching Sulit: Server push bisa membuat klien menerima resource yang sebenarnya tidak dibutuhkan.
- Wajib HTTPS: Umumnya hanya berjalan di atas HTTPS, menyulitkan integrasi dengan sistem lama yang belum pakai SSL/TLS.

> How does the request-response model of REST APIs contrast with the bidirectional streaming capabilities of gRPC in terms of real-time communication and responsiveness?

Model request-response dari REST API kurang responsif jika dibandingkan dengan model bi-directional streaming milik gRPC. Pada REST API, satu koneksi hanya digunakan untuk satu permintaan dan satu respons. Proses ini menimbulkan overhead karena setiap koneksi baru membutuhkan beberapa tahapan seperti handshaking dan verifikasi ulang. Sedangkan, gRPC menggunakan koneksi persisten berbasis HTTP/2 yang memungkinkan komunikasi dua arah secara terus-menerus antara klien dan server. Dengan streaming ini, data dapat dikirim dan diterima secara real-time tanpa perlu membuka koneksi baru. Sehingga, gRPC jauh lebih efisien dan responsif untuk aplikasi seperti chat, live dashboard, atau sistem pemantauan yang membutuhkan pembaruan instan.

> What are the implications of the schema-based approach of gRPC, using Protocol Buffers, compared to the more flexible, schema-less nature of JSON in REST API payloads?

Pendekatan gRPC yang menggunakan Protocol Buffers memiliki kelebihan seperti efisiensi yang tinggi dalam transmisi data, validasi otomatis melalui `.proto`, dan performa lebih baik karena menggunakan format biner yang ringkas. Namun penggunaan gRCP lebih kompleks dibandingkan dengan REST API. REST API dengan JSON memiliki fleksibilitas yang lebih tinggi, mudah dibaca dan dipahami. Namun penggunaan REST API menghasilkan payload yang lebih besar, proses lebih lambat, dan tidak terlalu konsisten.