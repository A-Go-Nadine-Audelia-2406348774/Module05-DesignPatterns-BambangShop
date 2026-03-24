# BambangShop Publisher App
Tutorial and Example for Advanced Programming 2024 - Faculty of Computer Science, Universitas Indonesia

---

## About this Project
In this repository, we have provided you a REST (REpresentational State Transfer) API project using Rocket web framework.

This project consists of four modules:
1.  `controller`: this module contains handler functions used to receive request and send responses.
    In Model-View-Controller (MVC) pattern, this is the Controller part.
2.  `model`: this module contains structs that serve as data containers.
    In MVC pattern, this is the Model part.
3.  `service`: this module contains structs with business logic methods.
    In MVC pattern, this is also the Model part.
4.  `repository`: this module contains structs that serve as databases and methods to access the databases.
    You can use methods of the struct to get list of objects, or operating an object (create, read, update, delete).

This repository provides a basic functionality that makes BambangShop work: ability to create, read, and delete `Product`s.
This repository already contains a functioning `Product` model, repository, service, and controllers that you can try right away.

As this is an Observer Design Pattern tutorial repository, you need to implement another feature: `Notification`.
This feature will notify creation, promotion, and deletion of a product, to external subscribers that are interested of a certain product type.
The subscribers are another Rocket instances, so the notification will be sent using HTTP POST request to each subscriber's `receive notification` address.

## API Documentations

You can download the Postman Collection JSON here: https://ristek.link/AdvProgWeek7Postman

After you download the Postman Collection, you can try the endpoints inside "BambangShop Publisher" folder.
This Postman collection also contains endpoints that you need to implement later on (the `Notification` feature).

Postman is an installable client that you can use to test web endpoints using HTTP request.
You can also make automated functional testing scripts for REST API projects using this client.
You can install Postman via this website: https://www.postman.com/downloads/

## How to Run in Development Environment
1.  Set up environment variables first by creating `.env` file.
    Here is the example of `.env` file:
    ```bash
    APP_INSTANCE_ROOT_URL="http://localhost:8000"
    ```
    Here are the details of each environment variable:
    | variable              | type   | description                                                |
    |-----------------------|--------|------------------------------------------------------------|
    | APP_INSTANCE_ROOT_URL | string | URL address where this publisher instance can be accessed. |
2.  Use `cargo run` to run this app.
    (You might want to use `cargo check` if you only need to verify your work without running the app.)

## Mandatory Checklists (Publisher)
-   [ ] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [ ] Commit: `Create Subscriber model struct.`
    -   [ ] Commit: `Create Notification model struct.`
    -   [ ] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [ ] Commit: `Implement add function in Subscriber repository.`
    -   [ ] Commit: `Implement list_all function in Subscriber repository.`
    -   [ ] Commit: `Implement delete function in Subscriber repository.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [ ] Commit: `Create Notification service struct skeleton.`
    -   [ ] Commit: `Implement subscribe function in Notification service.`
    -   [ ] Commit: `Implement subscribe function in Notification controller.`
    -   [ ] Commit: `Implement unsubscribe function in Notification service.`
    -   [ ] Commit: `Implement unsubscribe function in Notification controller.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [ ] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [ ] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [ ] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. In the Observer pattern diagram explained by the Head First Design Pattern book, Subscriber is defined as an interface. Explain based on your understanding of Observer design patterns, do we still need an interface (or trait in Rust) in this BambangShop case, or a single Model struct is enough?
Menurut saya, satu struct Subscriber saja sudah cukup. Interface baru dibutuhkan ketika memiliki banyak jenis subscriber dengan cara kerja yang berbeda-beda. Saat ini semua subscriber diperlakukan sama, sehingga satu struct sudah mewakili semuanya.

2. id in Program and url in Subscriber is intended to be unique. Explain based on your understanding, is using Vec (list) sufficient or using DashMap (map/dictionary) like we currently use is necessary for this case?
Menurut saya, DashMap sangatlah diperlukan karena id dan url sifatnya unik sehingga DashMap otomatis mencegah adanya data ganda. Selain itu, pencarian atau penghapusan data menggunakan DashMap jauh lebih cepat dibandingkan Vec yang harus mengecek list dari awal sampai akhir.

3. When programming using Rust, we are enforced by rigorous compiler constraints to make a thread-safe program. In the case of the List of Subscribers (SUBSCRIBERS) static variable, we used the DashMap external library for thread safe HashMap. Explain based on your understanding of design patterns, do we still need DashMap or we can implement Singleton pattern instead?
Menurut saya, keduanya sebenarnya dibutuhkan. Pola Singleton yang diterapkan lewat lazy_static hanya berfungsi untuk memastikan hanya punya satu sumber data global. Namun, Singleton saja tidak aman dari tabrakan data sehingga tetap wajib menggunakan DashMap agar variabel Singleton tersebut tidak rusak saat diakses atau diubah oleh banyak request secara bersamaan.

#### Reflection Publisher-2
1. In the Model-View Controller (MVC) compound pattern, there is no “Service” and “Repository”. Model in MVC covers both data storage and business logic. Explain based on your understanding of design principles, why we need to separate “Service” and “Repository” from a Model?
Pemisahan ini bertujuan untuk menerapkan Single Responsibility Principle. Jika service dan repository digabungkan, maka model akan menjadi sangat besar dan sulit dikelola. Dengan melakukan pemisahan maka struktur kode akan menjadi jauh lebih teratur dan semuanya memiliki tanggung jawab yang jelas. Model murni hanya mendefinisikan struktur data, repository khusus menangani urusan database, dan service khusus menangani logika aplikasi. Pemisahan ini membuat kode lebih modular, mudah dibaca, dan jauh lebih mudah untuk dilakukan unit testing.

2. What happens if we only use the Model? Explain your imagination on how the interactions between each model (Program, Subscriber, Notification) affect the code complexity for each model?
Jika kita hanya menggunakan model, maka untuk interaksi akan tercipta ketergantungan yang tinggi dan kode yang berantakan. Misalnya, model Product harus tahu persis cara membuat Notification dan cara menyimpan Subscriber ke dalam database sehingga model akan dipenuhi oleh logika kompleks yang bukan tanggung jawabnya. Jika suatu saat ada perubahan kecil pada cara kerja notifikasi, maka terpaksa harus ikut mengedit kode di dalam model produk yang membuat sistem sangat rentan terhadap error.

3. Have you explored more about Postman? Tell us how this tool helps you to test your current work. You might want to also list which features in Postman you are interested in or feel like it is helpful to help your Group Project or any of your future software engineering projects.
Postman sangat membantu karena memungkinkan pengujian API backend secara langsung tanpa harus menunggu frontend selesai dibuat. Dapat dengan mudah melakukan simulasi pengiriman data  dan melihat langsung respons server.

#### Reflection Publisher-3
1. Observer Pattern has two variations: Push model (publisher pushes data to subscribers) and Pull model (subscribers pull data from publisher). In this tutorial case, which variation of Observer Pattern that we use?
Tutorial ini menggunakan Push Model. Hal ini terlihat dari cara kerja bambangshop yang secara aktif mengirimkan data lengkap dalam bentuk payload notifikasi langsung ke URL para Subscriber segera setelah terjadi perubahan status produk.

2. What are the advantages and disadvantages of using the other variation of Observer Pattern for this tutorial case? (example: if you answer Q1 with Push, then imagine if we used Pull)
Jika menggunakan Pull Model, maka subscriber harus secara aktif menanyakan atau menarik data dari publisher secara berkala. Keuntungannya adalah subscriber memiliki kendali penuh atas kapan mereka ingin memproses data sehingga tidak akan terbebani jika publisher melakukan pembaruan besar dalam waktu singkat. Kerugiannya adalah model ini tidak efisien dalam penggunaan sumber daya jaringan karena subscriber akan terus melakukan pemanggilan API meskipun tidak ada perubahan data. Selain itu, aspek real time akan hilang karena ada latency antara saat data berubah di publisher sampai saat subscriber melakukan penarikan data berikutnya.

3. Explain what will happen to the program if we decide to not use multi-threading in the notification process.
Jika tidak menggunakan multi-threading, maka proses pengiriman notifikasi akan berjalan secara sinkron. Akibatnya adalah setiap kali ada produk baru maka publisher akan hang dan tidak bisa merespons pengguna sampai seluruh proses pengiriman pesan ke semua subscriber selesai. Jika salah satu subscriber memiliki koneksi internet yang lambat atau servernya mati, hal ini akan menghambat seluruh performa aplikasi utama bambangshop.
