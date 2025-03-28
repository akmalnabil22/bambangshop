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
-   [x] Clone https://gitlab.com/ichlaffterlalu/bambangshop to a new repository.
-   **STAGE 1: Implement models and repositories**
    -   [x] Commit: `Create Subscriber model struct.`
    -   [x] Commit: `Create Notification model struct.`
    -   [x] Commit: `Create Subscriber database and Subscriber repository struct skeleton.`
    -   [x] Commit: `Implement add function in Subscriber repository.`
    -   [x] Commit: `Implement list_all function in Subscriber repository.`
    -   [x] Commit: `Implement delete function in Subscriber repository.`
    -   [x] Write answers of your learning module's "Reflection Publisher-1" questions in this README.
-   **STAGE 2: Implement services and controllers**
    -   [x] Commit: `Create Notification service struct skeleton.`
    -   [x] Commit: `Implement subscribe function in Notification service.`
    -   [x] Commit: `Implement subscribe function in Notification controller.`
    -   [x] Commit: `Implement unsubscribe function in Notification service.`
    -   [x] Commit: `Implement unsubscribe function in Notification controller.`
    -   [x] Write answers of your learning module's "Reflection Publisher-2" questions in this README.
-   **STAGE 3: Implement notification mechanism**
    -   [x] Commit: `Implement update method in Subscriber model to send notification HTTP requests.`
    -   [x] Commit: `Implement notify function in Notification service to notify each Subscriber.`
    -   [x] Commit: `Implement publish function in Program service and Program controller.`
    -   [ ] Commit: `Edit Product service methods to call notify after create/delete.`
    -   [ ] Write answers of your learning module's "Reflection Publisher-3" questions in this README.

## Your Reflections
This is the place for you to write reflections:

### Mandatory (Publisher) Reflections

#### Reflection Publisher-1
1. Dalam konteks BambangShop, penggunaan satu struktur Model `Subscriber` sudah cukup untuk menerapkan pola Observer, karena hanya ada satu model `Product` dengan atribut tipe yang dapat di-subscribe oleh `Subscriber`. Namun, jika ke depannya ada model lain yang juga bisa di-subscribe, maka penggunaan sebuah interface atau trait untuk `Subscriber` akan lebih efektif untuk mendukung fleksibilitas dan skalabilitas sistem.  

2. Penggunaan `DashMap` tetap lebih sesuai dibandingkan `Vec`, meskipun `id` dalam `Product` dan `url` dalam `Subscriber` bersifat unik. Hal ini karena `Subscriber` disimpan berdasarkan product_type dari suatu Product. Jika menggunakan `Vec`, kita harus membuat pemetaan tambahan untuk menghubungkan indeks dengan `product_type`, yang justru menambah kompleksitas. Selain itu, jika `url` disimpan dalam `Vec`, maka saat ada `Subscriber` yang berhenti berlangganan dari suatu product_type, diperlukan pencarian linear untuk menemukannya sebelum dapat dihapus. Dengan `DashMap`, operasi ini dapat dilakukan dalam waktu konstan, meningkatkan efisiensi.  

3. Meskipun pola Singleton dapat diterapkan dalam kasus ini, implementasinya cukup kompleks dalam Rust karena adanya batasan ketat terkait variabel statis dan mutability. Mengingat tujuan utama hanya untuk menyimpan daftar `Subscriber` berdasarkan `product_type`, penggunaan `DashMap` adalah pilihan yang lebih praktis dan efisien dibandingkan harus mengelola kompleksitas tambahan yang muncul dengan Singleton.  

#### Reflection Publisher-2
1. Memisahkan `Service` dan `Repository` dari `Model` penting untuk menerapkan prinsip Single Responsibility. Dengan pemisahan ini, `Model` hanya bertanggung jawab sebagai representasi data, sementara `Repository` menangani penyimpanan data, dan `Service` mengelola business logic. Hal ini membuat kode lebih terorganisir, lebih mudah dipelihara, dan memungkinkan perubahan pada salah satu bagian tanpa mempengaruhi yang lain secara signifikan.  

2. Jika hanya menggunakan `Model`, setiap `Model` akan menjadi lebih kompleks karena harus menangani berbagai tanggung jawab sekaligus, seperti penyimpanan data, business logic, dan interaksi antar objek. Misalnya, `Subscriber` harus menangani subscribe, unsubscribe, serta menerima notifikasi saat ada perubahan `product_type`. Jika semua fungsi ini dimasukkan ke dalam `Model`, kode akan sulit dipahami dan sulit dikelola karena batasan tanggung jawab tiap entity menjadi tidak jelas.  

3. Saya belum mengeksplorasi Postman secara mendalam, tetapi dari pengalaman sejauh ini, alat ini sangat membantu dalam pengujian API. Postman memungkinkan pengiriman berbagai tipe HTTP request seperti `GET`, `POST`, `PUT`, dan `DELETE`, mempermudah pengujian endpoint. Selain itu, fitur untuk menambahkan `Headers` dan `Authorization` sangat berguna, terutama untuk proyek kelompok atau sistem yang memerlukan otentikasi dan custom headers. Secara keseluruhan, Postman menghemat waktu dibandingkan harus membuat skrip pengujian sendiri atau mengandalkan browser untuk menguji API.

#### Reflection Publisher-3
