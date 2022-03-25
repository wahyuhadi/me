# Fuzzing
 ---
 &nbsp;
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
