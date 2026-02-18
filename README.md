Nama: Christoper Hiero Barus
Nim: 2481036
Prodi: Teknik Informatika
Kelas: Pemrograman WEB 2
Dosen Pengampu: Sir Jeremy Panjaitan
Tugas & Eksperimen
Eksperimen 1: @Controller vs @RestController
Buat file: src/main/java/com/example/springmvclab/controller/CompareController.java
Buat juga template: 
src/main/resources/templates/test.html
Buka /test/view = Apa yang muncul?
Hasil: Ini dari @Controller
Buka /test/text = Apa yang muncul?
Hasil: Ini dari @Controller + @ResponseBody text langsung
Apa perbedaannya?
Ketika membuka /test/view method di dalam @Controller mengembalikan nama file template (view), yang artinya bahwa Spring tidak langsung menampilkan teks dari method, Melainkan Spring akan mencari file template dahulu, misalnya src/main/resources/templates/test.html, setelah itu isi dari file test.html itulah yang akan ditampilkan di browser.
Sedangkan, ketika membuka /test/text method menggunakan @ResponseBody, yang artinya bahwa Spring tidak mencari file HTML, tetapi apapun Isi yang dikembalikan method (String) langsung dikirim sebagai isi HTTP response, dan Browser menampilkan teks tersebut apa adanya.

Eksperimen 2: Apa Terjadi Jika Template Tidak Ada?
Di ProductController, ubah return value method listProducts menjadi template yang tidak ada:
Jalankan ulang dan buka http://localhost:8080/products
Apakah berhasil? Tidak
HTTP Status code: 500
Error message:
Whitelabel Error Page
This application has no explicit mapping for /error, so you are seeing this as a fallback.
Wed Feb 18 10:52:41 WIB 2026
There was an unexpected error (type=Internal Server Error, status=500).
Jika Controller return nama view yang tidak ada, Spring akan mengembalikan error 500 (Internal Server Error) karena template/view yang direturn tidak ditemukan oleh View Resolver (misalnya Thymeleaf) sehingga terjadi kegagalan saat proses rendering halaman.

Eksperimen 3: Perbedaan @RequestParam dan @PathVariable
Buat file: src/main/java/com/example/springmvclab/controller/ParamController.java
Buat template: 
src/main/resources/templates/greet.html
Coba semua URL berikut dan catat hasilnya:
URL	Hasil di Halaman
/greet	                       Selamat Pagi, Mahasiswa!
/greet?name=Budi	           Selamat Pagi, Budi!
/greet?name=Budi&waktu=        Siang	Selamat Siang, Budi!
/greet/Ani	                   Halo, Ani!
/greet/Ani/detail	           Halo, Ani!
/greet/Ani/detail?lang=EN	   Hello, Ani!
Pertanyaan:
URL mana yang pakai @RequestParam?
@RequestParam digunakan untuk mengambil parameter dari query string (?key=value)
Jawaban:
/greet
/greet?name=Budi
/greet?name=Budi&waktu=Siang
Karena parameter seperti name dan waktu dikirim melalui query parameter.
URL mana yang pakai @PathVariable?
@PathVariable digunakan untuk mengambil nilai dari bagian path URL.
Jawaban:
/greet/Ani
/greet/Ani/detail
Karena Ani diambil langsung dari bagian URL (/greet/{name}).
URL mana yang pakai keduanya?
Jawaban:
/greet/Ani/detail?lang=EN
Karena Ani diambil dengan @PathVariable, dan
lang=EN diambil dengan @RequestParam

Pertanyaan Refleksi
1. Apa perbedaan antara @Controller dan @RestController? Dalam kasus apa kamu pakai masing-masing?
@Controller digunakan untuk mengembalikan view (halaman HTML). Method di dalamnya biasanya mengembalikan nama file template. Spring akan mencari file tersebut di folder templates, lalu menampilkannya di browser.
Sedangkan @RestController digunakan untuk mengembalikan data langsung (biasanya JSON atau teks). Tidak ada proses pencarian template. Apa pun yang dikembalikan method akan langsung menjadi isi HTTP response.
Penggunaan:
Pakai @Controller jika ingin membuat aplikasi web yang menampilkan halaman HTML (misalnya pakai Thymeleaf).
Pakai @RestController jika ingin membuat REST API yang mengirim data (misalnya untuk frontend React, mobile app, atau testing API).

2. Perhatikan bahwa template product/list.html dipakai oleh 3 endpoint berbeda (list all, filter by category, search). Apa keuntungannya membuat template yang reusable seperti ini?
Keuntungannya adalah:
Tidak perlu membuat banyak file HTML untuk fungsi yang mirip.
Kode lebih rapi dan tidak duplikat.
Jika ingin mengubah tampilan, cukup ubah satu file saja.
Lebih mudah dirawat dan dikembangkan.
Jadi, satu template bisa dipakai untuk menampilkan semua produk, hasil filter, dan hasil pencarian.

3. Kenapa Controller inject ProductService (bukan langsung akses data di ArrayList)? Apa yang terjadi kalau Controller langsung manage data?
Controller inject ProductService supaya tugasnya terpisah dengan jelas.
Controller bertugas menerima request dan mengatur response.
Service bertugas mengelola logika bisnis dan data.
Jika Controller langsung mengelola data:
Kode jadi bercampur antara logika bisnis dan pengaturan request.
Sulit dikembangkan.
Sulit diuji (testing).
Tidak mengikuti prinsip pemisahan tanggung jawab (Separation of Concerns).
Dengan adanya Service, struktur program menjadi lebih terorganisir.

4. Apa perbedaan model.addAttribute("products", products) dengan return products langsung seperti di @RestController?
model.addAttribute("products", products) digunakan pada @Controller untuk mengirim data ke template HTML. Data tersebut nanti akan dipakai di file HTML untuk ditampilkan.
Sedangkan jika langsung return products pada @RestController, maka data tersebut akan dikirim langsung sebagai response (biasanya dalam bentuk JSON), tanpa melalui template HTML.
Jadi:
model.addAttribute → untuk ditampilkan di halaman HTML.
return products → untuk dikirim sebagai data API.

5. Jika kamu buka http://localhost:8080/products/abc (ID bukan angka), apa yang terjadi? Kenapa?
Akan terjadi error (biasanya 400 Bad Request).
Karena di mapping biasanya ID didefinisikan sebagai tipe Integer atau Long.
Saat kita mengisi "abc", Spring mencoba mengubahnya menjadi angka, tetapi gagal.
Akibatnya terjadi error karena tipe data tidak sesuai.

6. Apa keuntungan pakai @RequestMapping("/products") di level class disbanding menulis full path di setiap @GetMapping?
Keuntungannya:
Tidak perlu menulis /products berulang kali.
Kode lebih singkat dan rapi.
Jika ingin mengubah base URL, cukup ubah di satu tempat saja.
Mengurangi kemungkinan salah tulis path.
Jadi lebih efisien dan mudah dirawat.

7. Dalam lab ini, kata "Model" muncul dalam beberapa konteks berbeda. Sebutkan minimal 2 arti yang berbeda dan jelaskan perbedaannya.
Hint: perhatikan Model di parameter method Controller, folder model/, dan class Product
Ada beberapa arti:
Model pada parameter method Controller
Ini adalah objek dari Spring yang digunakan untuk mengirim data dari Controller ke template HTML. Contohnya model.addAttribute().
Folder model/
Ini adalah package yang berisi class yang merepresentasikan struktur data atau entitas.
Class Product
Ini adalah model dalam arti objek data. Class ini merepresentasikan data produk, seperti id, nama, harga, dan kategori.
Perbedaannya:
Model (parameter Controller) → alat untuk mengirim data ke view.
Model (class seperti Product) → representasi data dalam program.
Folder model → tempat penyimpanan class data tersebut.
