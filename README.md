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

Latihan 2: Navigasi Multi-Halaman & Layout Sederhana
Tugas & Eksperimen

Eksperimen 1: Fragment yang Tidak Ada
1.	Di template mana saja, ubah referensi fragment menjadi nama yang tidak ada:
<div th :replace="~{fragments/layout :: navbar-yang-salah}"></div>
1.	Jalankan dan buka halaman tersebut.

Apakah error? [Ya/Tidak] Ya Erro r
Erro r message:
Whitelabel Error Page
This application has no explicit mapping for /error, so you are seeing this as a fallback.
Wed Feb 18 14:59:42 WIB 2026
There was an unexpected error (type=Internal Server Error, status=500).
Kesimpulan: Jika nama fragment salah, Thymeleaf akan menampilkan error (HTTP 500 – Internal Server Error), karena Thymeleaf tidak dapat menemukan fragment yang dimaksud sehingga proses render halaman gagal.

Eksperimen 2: Static Resource Path
1.	Coba hapus th: dari href CSS:
<!-- Dari: -->
<link rel="stylesheet" th :href="@{/css/style.css}">
<!-- Menjadi: -->
<link rel="stylesheet" href="/css/style.css">
      Jalankan dan buka halaman. Apakah CSS masih ter-load?
      CSS masih bekerja? [Ya/Tidak] Ya Masih Bekerja
      Ringkasan: CSS tetap ter-load karena file style.css memang berada pada folder static/css,
      sehingga masih bisa diakses langsung melalui path /css/style.css. Dalam kondisi aplikasi
      berjalan di root context (misalnya http://localhost:8080), cara ini tetap berhasil.
      2. Sekarang coba path yang salah:
<link rel="stylesheet" th :href="@{/css/tidak-ada.css}">
Apakah halaman error?      [Ya/Tidak] Tidak
Apakah CSS diterapkan?     [Ya/Tidak] Tidak
Ringkasan: Halaman tetap bisa ditampilkan karena kesalahan file CSS tidak memengaruhi proses render template Thymeleaf. Namun, karena file tidak-ada.css memang tidak tersedia, maka browser tidak dapat memuat CSS tersebut sehingga gaya tidak diterapkan.
Kesimpulan: th:href=@{} lebih baik karena Thymeleaf akan menyesuaikan path secara otomatis sesuai dengan context path aplikasi. Cara ini lebih aman dan fleksibel, terutama jika aplikasi tidak dijalankan di root (/), misalnya dijalankan dengan nama project tertentu.
Jika file CSS tidak ada, halaman tetap dapat dibuka, tetapi tampilan tidak akan menggunakan CSS tersebut karena browser tidak menemukan file yang dimaksud.

Pertanyaan Refleksi:
1. Apa keuntungan menggunakan Thymeleaf Fragment untuk navbar dan footer?
Keuntungan utamanya adalah efisiensi dan konsistensi.
Dengan fragment, navbar dan footer cukup dibuat satu kali saja di file terpisah. Setelah itu, semua halaman tinggal memanggilnya. Jika suatu saat kita ingin mengubah isi navbar atau footer, kita cukup mengubah satu file saja. Semua halaman otomatis ikut berubah.
Selain itu, cara ini membuat kode lebih rapi dan tidak berulang-ulang. Struktur proyek juga lebih terorganisir karena bagian layout dipisahkan dari konten halaman.

2. Apa bedanya file di static/ dan templates/? Kenapa CSS ada di static/ bukan templates/?
Folder templates/ berisi file HTML yang diproses oleh Thymeleaf di server sebelum dikirim ke browser. Artinya, file di dalamnya bisa menggunakan atribut seperti th:text, th:each, dan lain-lain.
Sedangkan folder static/ berisi file yang langsung dikirim ke browser tanpa diproses oleh Thymeleaf. Contohnya adalah CSS, JavaScript, gambar, dan file statis lainnya.
CSS diletakkan di static/ karena CSS tidak perlu diproses oleh Thymeleaf. Browser cukup mengambil file tersebut dan menerapkannya ke halaman. Jika CSS diletakkan di templates/, maka file itu tidak akan bisa diakses langsung sebagai resource statis.

3. Apa yang dimaksud dengan th:replace dan bagaimana bedanya dengan th:insert?
Hint: coba ganti th:replace jadi th:insert dan inspect element di browser
th:replace adalah perintah untuk mengganti elemen yang ada dengan isi fragment.
Contohnya:
<div th :replace="~{fragments/layout :: navbar}"></div>
Artinya, <div> tersebut akan diganti sepenuhnya oleh isi fragment navbar.
Sedangkan th:insert hanya menyisipkan isi fragment ke dalam elemen yang sudah ada.
Jika kita ubah menjadi:
<div th :insert="~{fragments/layout :: navbar}"></div>
Maka <div>-nya tetap ada, dan isi navbar akan berada di dalam <div> tersebut.
Perbedaannya bisa terlihat saat inspect element di browser:
th :replace → tag asli hilang, diganti fragment.
th :insert → tag asli tetap ada, fragment masuk ke dalamnya

4. Kenapa kita pakai @{} untuk URL di Thymeleaf, bukan langsung tulis path?
@{} digunakan agar Thymeleaf bisa menyesuaikan URL dengan context path aplikasi secara otomatis Jika kita menulis langsung: href="/products" itu hanya aman jika aplikasi berjalan di root, misalnya http://localhost:8080. Namun jika aplikasi dijalankan dengan context path tertentu, misalnya: http://localhost:8080/app maka path manual bisa menjadi salah. Dengan @{}: th:href="@{/products}" Thymeleaf akan menyesuaikan URL secara otomatis sesuai lokasi aplikasi dijalankan. Jadi lebih aman, fleksibel, dan tidak perlu diubah jika deployment berubah.

5. Perhatikan bahwa ProductController inject ProductService melalui Constructor Injection (konsep dari Week 3). Apa jadinya kalau Controller tidak pakai DI dan langsung new ProductService() di dalam Controller?
Jika Controller langsung membuat objek sendiri dengan new ProductService(), maka Controller menjadi terlalu bergantung pada implementasi tersebut. Ini disebut tight coupling.
Akibatnya:
•	Sulit melakukan pengujian (testing), karena kita tidak bisa dengan mudah mengganti ProductService dengan versi mock.
•	Tidak fleksibel jika ingin mengganti implementasi service.
•	Tidak mengikuti prinsip dasar Spring, yaitu IoC (Inversion of Control).
Dengan Dependency Injection melalui constructor, Spring yang akan mengatur pembuatan dan pengelolaan objek. Controller hanya menerima dependensi yang sudah disiapkan. Ini membuat kode lebih bersih, lebih mudah diuji, dan lebih sesuai dengan praktik pengembangan yang baik.

