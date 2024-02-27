# Promo Integration

## 1. Introduction
Dokumentasi ini memberikan panduan lengkap terkait Integrasi Promo MyValue, memudahkan Anda untuk mengintegrasikan produk kami dengan sistem Anda.

## 2. Getting Started

### Pre-Requisites

Hal yang wajib diketahui untuk menggunakan API di dokumentasi ini

- Mengetahui cara kerja REST-API. Memahami cara membuat request dan menerima response API
- Memiliki `client_id` dan `client_secret` yang sudah diberikan oleh Tim MyValue
- Memiliki value id yang terdaftar
- Proses integrasi menggunakan sistem Host-to-Host (Backend to Backend) atau direct integration.

## 3. Base URL

Sandbox Environment adalah tempat dimana kamu dapat melakukan internal end-to-end testing menggunakan kredensial _MyValue Staging_.

| Type         | Sandbox                       |
| ------------ | ----------------------------- |
| External API | https://external.ovaltech.id/ |

## 4. Integrasi Promo
Section ini menentukan cara untuk mendapatkan informasi promo dan melakukan transaksi pembelian promo MyValue.

| HTTP Method | Base URL Type | Endpoint                   | Description                                |
| ----------- | ------------- | -------------------------- | ------------------------------------------ |
| GET         | External API  | `/v1/service/promos/:ID`   | mendapatkan detail promo berdasarkan ID    |
| GET         | External API  | `/v1/service/promos`       | mendapatkan list promo                     |
| GET         | External API  | `/v1/service/vouchers/:ID` | mendapatkan detail voucher berdasarkan ID  |
| GET         | External API  | `/v1/service/vouchers`     | mendapatkan list voucher                   |
| POST        | External API  | `/v1/service/promos/buy`   | melakukan redeem untuk mendapatkan voucher |

### A. Detail Promo
Dengan menggunakan method ini, anda dapat melihat detail promo. Method ini mengirimkan informasi seperti id promo dan value_id.

`GET /v1/service/promos/:ID`

#### Path Parametes
| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| ID    | Int  | Required | Id Promo    |

#### Query Parametes
| Field    | Type   | Required | Description                   |
| -------- | ------ | -------- | ----------------------------- |
| valueID  | String | Required | Value id                      |
| clientID | String | Required | Id client                     |
| sign     | String | Required | Sign, hash menggunakan SHA256 |

> Pembuatan sign menggunakan kalkulasi: **sha256(clientID + clientSecret + ID + valueID)**

#### Examples

request
```sh
curl --location --globoff 'https://external.ovaltech.id/v1/service/promos/{{PROMO_ID}}?valueID=VR8EPN&clientID=KompasCom&sign={{SIGN}}'
```

#### Responses Detail Promo - Success
> HTTP Status Code: `200`

```json
{
    "id": 13063,
    "status": 1,
    "merchant": {
        "id": 6,
        "image": "https://staging-myvalue.ap-south-1.linodeobjects.com/media/merchant/1666671034688-Gramed 2",
        "name": "Gramedia"
    },
    "isExpired": false,
    "paymentRequired": false,
    "paymentMethod": null,
    "categories": [
        {
            "id": 2,
            "url": "http://external.ovaltech.id/categories/2/",
            "title": "Books"
        }
    ],
    "categoryIds": [
        "2"
    ],
    "title": "Vocer Buku",
    "image": "https://staging-myvalue.ap-south-1.linodeobjects.com/media/no-img.jpg",
    "thumbnail": "https://staging-myvalue.ap-south-1.linodeobjects.com/media/no-img.jpg",
    "validFrom": "2024-02-01T13:18:21+07:00",
    "validTo": "2025-02-01T13:18:28+07:00",
    "content": "<p>konten</p>",
    "tnc": "<p>snk</p>",
    "howToUse": "<p>cara pakai</p>",
    "price": 0,
    "priority": 999,
    "isQuotaAvailable": true,
    "quotaType": 0,
    "promoType": 1,
    "isActive": true,
    "deepLink": "https://myvalue.test-app.link/promo/13063/vocer-buku",
    "isPaymentAvailable": false,
    "isAlreadyPurchased": false
}
```

**Response Body Parameters**

| JSON Attribute     | Type           | Description                                             |
| ------------------ | -------------- | ------------------------------------------------------- |
| id                 | int32          | Unique identifier for the promo                         |
| status             | int32          | Status of the promo (e.g., "Active", "Expired")         |
| merchant           | *Merchants     | Information about the associated merchant               |
| isExpired          | bool           | Indicates if the promo has expired                      |
| paymentRequired    | bool           | Indicates if payment is required for the promo          |
| paymentMethod      | *PaymentMethod | Payment methods accepted for the promo                  |
| categories         | []*Categories  | Categories associated with the promo                    |
| categoryIds        | []string       | IDs of categories associated with the promo             |
| title              | string         | Title of the promo                                      |
| image              | string         | URL of the promo image                                  |
| thumbnail          | string         | URL of the promo thumbnail                              |
| validFrom          | string         | Start date for promo validity                           |
| validTo            | string         | End date for promo validity                             |
| shortContent       | string         | Short description of the promo                          |
| content            | string         | Detailed content of the promo                           |
| tnc                | string         | Terms and conditions of the promo, di list ga muncul    |
| howToUse           | string         | Instructions on how to use the promo, di list ga muncul |
| price              | int32          | Price associated with the promo                         |
| priority           | int32          | Priority level of the promo                             |
| isQuotaAvailable   | bool           | Indicates if there is available quota                   |
| quotaType          | int32          | Type of quota for the promo                             |
| promoType          | int32          | Type of promo                                           |
| isActive           | bool           | Indicates if the promo is active                        |
| deepLink           | string         | Deep link associated with the promo                     |
| isPaymentAvailable | bool           | Indicates if payment is available                       |
| isAlreadyPurchased | bool           | Indicates if the promo has already been purchased       |

#### Responses - Failure

> HTTP Status Code: `400`

Proses validasi gagal atau format request yang dimasukkan salah.

```json
{
    "id": "InvalidClient",
    "detail": "Client id or client secret is invalid",
    "errorCode": 400
}
```

> HTTP Status Code: `401`

Gagal melakukan proses autentikasi data.

```json
{
    "id": "ErrAuthorizationInvalidSign",
    "detail": "Invalid Sign Parameter",
    "errorCode": 401
}
```

### B. List Promo
Dengan menggunakan method ini, anda dapat melihat list promo. Method ini mengirimkan informasi seperti value_id, page, pageSize dan filter lainnya.

`GET /v1/service/promos`

#### Query Parametes
| Field    | Type    | Required | Description                                             |
| -------- | ------- | -------- | ------------------------------------------------------- |
| valueID  | String  | Required | Value id                                                |
| page     | Integer | Optional | Halaman saat ini, default 1                             |
| pageSize | Integer | Optional | Jumlah data yang ditampilkan dalam 1 halaman, default 8 |
| category | Integer | Optional | Filter category promo, bisa multiple                    |
| clientID | String  | Required | Id client                                               |
| sign     | String  | Required | Sign, hash menggunakan SHA256                           |

> Pembuatan sign menggunakan kalkulasi: **sha256(clientID + clientSecret + valueID + page + pageSize)**

#### Examples

request
```sh
curl --location 'https://external.ovaltech.id/v1/service/promos?valueID=VR8EPN&page=1&pageSize=8&clientID=KompasCom&sign={{SIGN}}&category=2&category=5035'
```

#### Responses - Success

> HTTP Status Code: `200`

```json
{
    "count": 1,
    "next": null,
    "previous": null,
    "perPage": 8,
    "results": [
        {
            "id": 13063,
            "status": 1,
            "merchant": {
                "id": 6,
                "image": "https://staging-myvalue.ap-south-1.linodeobjects.com/media/merchant/1666671034688-Gramed 2",
                "name": "Gramedia"
            },
            "isExpired": false,
            "paymentRequired": false,
            "paymentMethod": null,
            "categories": [
                {
                    "id": 2,
                    "url": "http://external.ovaltech.id/categories/2/",
                    "title": "Books"
                }
            ],
            "categoryIds": [
                "2"
            ],
            "title": "Vocer Buku",
            "image": "https://staging-myvalue.ap-south-1.linodeobjects.com/media/no-img.jpg",
            "thumbnail": "https://staging-myvalue.ap-south-1.linodeobjects.com/media/no-img.jpg",
            "validFrom": "2024-02-01T13:18:21+07:00",
            "validTo": "2025-02-01T13:18:28+07:00",
            "content": "<p>konten</p>",
            "tnc": "",
            "howToUse": "",
            "price": 0,
            "priority": 999,
            "isQuotaAvailable": true,
            "quotaType": 0,
            "promoType": 1,
            "isActive": true,
            "deepLink": "https://myvalue.test-app.link/promo/13063/vocer-buku",
            "isPaymentAvailable": false,
            "isAlreadyPurchased": false
        }
    ]
}
```

**Response Body Parameters**

| JSON Attribute | Type                                                | Description                                                             |
| -------------- | --------------------------------------------------- | ----------------------------------------------------------------------- |
| count          | integer                                             | The total count of events in the response.                              |
| next           | string                                              | The URL to retrieve the next page of promo (if available).              |
| previous       | string                                              | The URL to retrieve the previous page of promo (if available).          |
| perPage        | integer                                             | The number of promo per page in the response.                           |
| results        | array of [Promo](#responses-detail-promo---success) | An array containing [promo](#responses-detail-promo---success) objects. |

#### Responses - Failure

> HTTP Status Code: `400`

Proses validasi gagal atau format request yang dimasukkan salah.

```json
{
    "id": "InvalidClient",
    "detail": "Client id or client secret is invalid",
    "errorCode": 400
}
```

> HTTP Status Code: `401`

Gagal melakukan proses autentikasi data.

```json
{
    "id": "ErrAuthorizationInvalidSign",
    "detail": "Invalid Sign Parameter",
    "errorCode": 401
}
```

### C. Detail Voucher
Dengan menggunakan method ini, anda dapat melihat detail voucher. Method ini mengirimkan informasi seperti id voucher dan value_id.


`GET /v1/service/vouchers/:ID`

#### Path Parametes
| Field | Type | Required | Description |
| ----- | ---- | -------- | ----------- |
| ID    | Int  | Required | Id Voucher  |

#### Query Parametes
| Field    | Type   | Required | Description                   |
| -------- | ------ | -------- | ----------------------------- |
| valueID  | String | Required | Value id                      |
| clientID | String | Required | Id client                     |
| sign     | String | Required | Sign, hash menggunakan SHA256 |

> Pembuatan sign menggunakan kalkulasi: **sha256(clientID + clientSecret + ID + valueID)**

#### Examples

request
```sh
curl --location --globoff 'https://external.ovaltech.id/v1/service/vouchers/{{VOUCHER_ID}}?valueID=VR8EPN&clientID=KompasCom&sign={{SIGN}}'
```

#### Responses Detail Voucher - Success
> HTTP Status Code: `200`

| JSON Attribute   | Type                                        | Description                                     |
| ---------------- | ------------------------------------------- | ----------------------------------------------- |
| id               | int32                                       | Unique identifier for the voucher               |
| code             | string                                      | Voucher code                                    |
| promo            | *[Promo](#responses-detail-promo---success) | Associated promotional information              |
| qrCode           | string                                      | QR code associated with the voucher             |
| isPaymentSuccess | bool                                        | Indicates if the voucher payment was successful |
| transaction      | *Transactions                               | Associated transaction information              |
| used             | bool                                        | Indicates if the voucher has been used          |
| purchasedDate    | string                                      | Date when the voucher was purchased             |
| pointUsed        | int32                                       | Points used for the voucher redemption          |
| usedDate         | null.String                                 | Date when the voucher was used                  |
| redeemFrom       | string                                      | Start date for voucher redemption               |
| redeemTo         | string                                      | End date for voucher redemption                 |
| isCanRedeem      | bool                                        | Indicates if the voucher can be redeemed        |

#### Responses - Failure

> HTTP Status Code: `400`

Proses validasi gagal atau format request yang dimasukkan salah.

```json
{
    "id": "InvalidClient",
    "detail": "Client id or client secret is invalid",
    "errorCode": 400
}
```

> HTTP Status Code: `401`

Gagal melakukan proses autentikasi data.

```json
{
    "id": "ErrAuthorizationInvalidSign",
    "detail": "Invalid Sign Parameter",
    "errorCode": 401
}
```

### D. List Voucher
Dengan menggunakan method ini, anda dapat melihat list voucher. Method ini mengirimkan informasi seperti value_id, page dan pageSize.

`GET /v1/service/vouchers`

#### Query Parametes
| Field    | Type    | Required | Description                                             |
| -------- | ------- | -------- | ------------------------------------------------------- |
| valueID  | String  | Required | Value id                                                |
| page     | Integer | Optional | Halaman saat ini, default 1                             |
| pageSize | Integer | Optional | Jumlah data yang ditampilkan dalam 1 halaman, default 8 |
| clientID | String  | Required | Id client                                               |
| sign     | String  | Required | Sign, hash menggunakan SHA256                           |

> Pembuatan sign menggunakan kalkulasi: **sha256(clientID + clientSecret + valueID + page + pageSize)**

#### Examples

request
```sh
curl --location 'https://external.ovaltech.id/v1/service/vouchers?valueID=VR8EPN&page=1&pageSize=8&clientID=KompasCom&sign={{SIGN}}'
```

#### Responses List Voucher - Success
> HTTP Status Code: `200`

| JSON Attribute | Type                                                    | Description                                                                 |
| -------------- | ------------------------------------------------------- | --------------------------------------------------------------------------- |
| count          | integer                                                 | The total count of events in the response.                                  |
| next           | string                                                  | The URL to retrieve the next page of promo (if available).                  |
| previous       | string                                                  | The URL to retrieve the previous page of promo (if available).              |
| perPage        | integer                                                 | The number of promo per page in the response.                               |
| results        | array of [Voucher](#responses-detail-voucher---success) | An array containing [voucher](#responses-detail-voucher---success) objects. |

#### Responses - Failure

> HTTP Status Code: `400`

Proses validasi gagal atau format request yang dimasukkan salah.

```json
{
    "id": "InvalidClient",
    "detail": "Client id or client secret is invalid",
    "errorCode": 400
}
```

> HTTP Status Code: `401`

Gagal melakukan proses autentikasi data.

```json
{
    "id": "ErrAuthorizationInvalidSign",
    "detail": "Invalid Sign Parameter",
    "errorCode": 401
}
```

### E. Redeem Promo
Dengan menggunakan method ini, anda dapat melakukan redeem untuk mendapatkan voucher. Method ini mengirimkan informasi seperti id promo dan value_id.

`POST /v1/service/promos/buy`

#### Body Request
| Field    | Type   | Required | Description                   |
| -------- | ------ | -------- | ----------------------------- |
| id       | Int    | Required | Id Promo                      |
| valueID  | String | Required | Value id                      |
| clientID | String | Required | Id client                     |
| sign     | String | Required | Sign, hash menggunakan SHA256 |

> Pembuatan sign menggunakan kalkulasi: **sha256(clientID + clientSecret + id + valueID)**

#### Examples

request
```sh
curl --location 'https://external.ovaltech.id/v1/service/promos/buy' \
--header 'Content-Type: application/json' \
--data '{
    "id":1,
    "valueID":"VR8EPN",
    "clientID":"KompasCom",
    "sign":{{SIGN}}
}'
```

#### Responses List Voucher - Success
> HTTP Status Code: `200`

[Detail Voucher Response](#responses-detail-voucher---success)

#### Responses - Failure

> HTTP Status Code: `400`

Proses validasi gagal atau format request yang dimasukkan salah.

```json
{
    "id": "InvalidClient",
    "detail": "Client id or client secret is invalid",
    "errorCode": 400
}
```

> HTTP Status Code: `401`

Gagal melakukan proses autentikasi data.

```json
{
    "id": "ErrAuthorizationInvalidSign",
    "detail": "Invalid Sign Parameter",
    "errorCode": 401
}
```

