# MyValue-Partner

## Introduction

Kami memahami kebutuhan anda untuk melakukan integrasi lebih dalam dengan produk kami. Kami hadir untuk mengaktifkan dan mendukung sistem anda agar terintegrasi dengan produk MyValue.

## Getting Started

### Pre-Requisites

Hal yang wajib diketahui untuk menggunakan API di dokumentasi ini

- Mengetahui cara kerja REST-API. Memahami cara membuat request dan menerima response API
- Memiliki `client_id` dan `client_secret` yang sudah diberikan oleh Tim MyValue
- Mengajukan `redirect_url` ke Tim MyValue ketika ada perubahan atau penambahan alamat url
- Proses integrasi menggunakan sistem Host-to-Host (Backend to Backend) atau direct integration.

### Objective

Dengan menyelesaikan dokumentasi ini, anda dapat

- Membuat request dan menerima response API
- Memahami cara kerja API dan API mana yang dibutuhkan untuk produk anda.
- Memahami komponen yang perlu dibangun di sistem/produk anda agar sesuai dengan API MyValue

### Base URL

Sandbox Environment adalah tempat dimana kamu dapat melakukan internal end-to-end testing menggunakan kredensial _MyValue Staging_. Ketika testing sudah selesai, kamu dapat menggunakan _Production Environment_ menggunakan kredensial _MyValue Production_.

| Type         | Sandbox                       | Production                   |
| ------------ | ----------------------------- | ---------------------------- |
| External API | https://external.ovaltech.id/ | https://external.myvalue.id/ |
| SSO API      | https://auth.ovaltech.id/     | https://auth.ovaltech.id/    |

### Authentication

MyValue melakukan validasi client untuk proses integrasi ke semua partner kami. Data yang dibutuhkan untuk validasi ini adalah `client_id` dan `client_secret`. Proses autentikasi melakukan pengecekan payload sign dengan menggunakan hash sha256.

### Postman Collection
Postman merupakan tools dimana kamu dapat melakukan pengujian suatu endpoint API. MyValue menyediakan sekumpulan endpoint yang dapat diuji melalui postman melalui link dibawah ini.

https://www.getpostman.com/collections/57969755196a3c590781