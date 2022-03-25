# Fuzzing A.K.A The powerfull testing
 ---
 &nbsp;
 ---
# Apa itu Fuzzing
<p style="text-align: justify; letter-spacing: 0.002em;">
Fuzzing adalah sebuah teknik pengujian sebuah sistem dengan cara menginputkan random data, bahkan format data yang tak terduga yang tidak bisa di handle sama sebuah sistem yang akan di inputkan untuk menguji sebuah sistem. untuk lebih jelasnya bisa di perhatikan pada potongan code berikut.
</p>

```go
func add(a int, b int) int {
    return a + b
}
```
<p style="text-align: justify; letter-spacing: 0.002em;">
Pada function diatas terdapat inputan dengan format int, nah coba kita inputkan pada variable a dan b dengan type data yang berbeda semisal (string, binary, unicode dan semacamnya), bagaimana behavioral dari function tersebut ? , apa yang terjadi ketika kita meng inputkan type data yang berbeda ?. Nah dengan cara menginputkan random data pada sebuah function yang telah didefiniskan inputannya secara tidak langsung kita telah melakukan fuzzing. sampai disini mungkin sudah cukup jelas apa itu fuzzing.
</p>
&nbsp;
---

# Apa saja yang bisa di fuzzing ?
<p style="text-align: justify; letter-spacing: 0.002em;">
Pertanyaan ini sering sekali ditanyakan ketika kita baru akan melakukan fuzzing atau orang yang baru belajar fuzzing. Jawaban dari pertanyaan adalah, semua sistem / aplikasi yang menerima inputan itu bisa di fuzzing , namun tekniknya berbeda-beda, teknik fuzzing function akan berbeda dengan fuzzing REST API.
Dimana fuzzing function kita bisa melakukan full control pada function tersebut,biasanya fuzzing pada function ini bertujan untuk mencari bug / membuat 0day exploit dengan methode white box (kita bisa melihat behavioral dari internal log apps). 
</p>
<p style="text-align: justify; letter-spacing: 0.002em;">
Nah beda lagi dengan technik fuzzing REST API biasanya sering digunakan untuk melakukan debuging pada sebuah framework atau ORM yang ready to use, akan jauh lebih bagus ketika kita bisa melihat log dari sebuah framework yang difuzzing, apalagi ketika kita melakukan fuzzing ORM melalui inputan REST API akan sangat lebih mudah ketika kita bisa melihat raw Query yang di generate oleh ORM. Namun perlu diingat fuzzing ini terkadang pada REST API bisa membuat server crash A.K.A Internal Server Error untuk lebih jelasnya bisa di lihat pada link berikut teknik fuzzing pada REST API  <a href="https://www.youtube.com/watch?v=Gb3Lb4gRtXk">Youtube Fuzzing REST API</a>
</p>
&nbsp;
---


# Bug apa saja yang biasa ditemukan ketika melakukan debugging dengan fuzzing?
<p style="text-align: justify; letter-spacing: 0.002em;">
Bug apa saja yang ditemukan saat melakukan fuzzing ?? biasanya itu tergantung apa yang kita fuzzing. Jika kita melakukan fuzzing pada sebuah function akan sering di temukan bug :
</p>

* Memory Leaks
* Use After Free
* Error Handling
* Memory Corruption

<p style="text-align: justify; letter-spacing: 0.002em;"> Beda halnya ketika fuzzing ORM bug biasa yang ditemukan adalah : </p>

* SQL Injection
* Raw Query Injection (Ujungnya adalah SQL Injection)
* Leaking Raw Query (Bad Error Handling) 
* ORM Bad Logic
* Bad Raw Query
