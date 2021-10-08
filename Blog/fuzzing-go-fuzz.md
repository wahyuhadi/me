# Fuzzing golang with go-fuzz
 ---
 &nbsp;
[![GoDoc](https://godoc.org/github.com/google/gofuzz?status.svg)](https://godoc.org/github.com/google/gofuzz)
 

 
 <p style="text-align: justify; letter-spacing: 0.002em;">
Fuzzing is a testing technique that loads our program with random data. It is <b>complementary to the usual testing</b> developers to find bugs that would be hard to find with manual generated input. Fuzzing is quite easy to set up in Go programs and can be adapted to almost all kinds of code.
</p>

&nbsp;

##### How to install go-fuzz
---

```sh
$ go get -u github.com/dvyukov/go-fuzz/go-fuzz@latest github.com/dvyukov/go-fuzz/go-fuzz-build@latest
```


for details [go-fuzz](https://github.com/dvyukov/go-fuzz)
&nbsp;
&nbsp;

##### Example how to test 
---
 <p style="text-align: justify; letter-spacing: 0.002em;">
For example i have a package call add, in the same package folder i have a fuzz.go file 
</p>

```
.
├── add 
│   ├── add.go
│   └── fuzz.go
├── apps.go
├── go.mod
└── go.sum

1 directory, 6 files
```

add.go
```go
package add
func Add(a, b int) int {
	return a + b
}
```

 <p style="text-align: justify; letter-spacing: 0.002em;">
Now we create a new file inside the package named Fuzz.go and write our fuzz function:
</p>

fuzz.go

```go
package add
import fuzz "github.com/google/gofuzz"
func Fuzz(data []byte) int {
	var i int
	fuzz.NewFromGoFuzz(data).Fuzz(&i)
	Add(i, i) // fuzzing Add func
	return 0
}
```

<p style="text-align: justify; letter-spacing: 0.002em;">
After that run go-fuzz-build  projectname/package to make the magic blob</p>

<b>Example </b>: `go-fuzz-build fuzz/add` in this case i will fuzz the package add
	
```sh
$ go-fuzz-build fuzz/add
$ tree
.
├── add
│   ├── add.go
│   └── fuzz.go
├── add-fuzz.zip // generated fuzzing payload
├── apps.go
├── go.mod
└── go.sum

1 directory, 6 files
```
&nbsp;
##### Fuzzing time
----
Now we can run go-fuzz.

```sh
$ go-fuzz -bin=add-fuzz.zip -workdir=.
2021/10/08 11:30:28 workers: 8, corpus: 5 (1s ago), crashers: 0, restarts: 1/0, execs: 0 (0/sec), cover: 0, uptime: 3s
2021/10/08 11:30:31 workers: 8, corpus: 5 (4s ago), crashers: 0, restarts: 1/0, execs: 0 (0/sec), cover: 141, uptime: 6s
2021/10/08 11:30:34 workers: 8, corpus: 5 (7s ago), crashers: 0, restarts: 1/4001, execs: 72020 (8001/sec), cover: 141, uptime: 9s
2021/10/08 11:30:37 workers: 8, corpus: 5 (10s ago), crashers: 0, restarts: 1/5690, execs: 170725 (14225/sec), cover: 141, uptime: 12s
2021/10/08 11:30:40 workers: 8, corpus: 5 (13s ago), crashers: 0, restarts: 1/6748, execs: 263194 (17545/sec), cover: 141, uptime: 15s
2021/10/08 11:30:43 workers: 8, corpus: 5 (16s ago), crashers: 0, restarts: 1/7546, execs: 362210 (20122/sec), cover: 141, uptime: 18s
2021/10/08 11:30:46 workers: 8, corpus: 5 (19s ago), crashers: 0, restarts: 1/8196, execs: 458981 (21855/sec), cover: 141, uptime: 21s
2021/10/08 11:30:49 workers: 8, corpus: 5 (22s ago), crashers: 0, restarts: 1/8267, execs: 553911 (23079/sec), cover: 141, uptime: 24s
2021/10/08 11:30:52 workers: 8, corpus: 5 (25s ago), crashers: 0, restarts: 1/8480, execs: 652979 (24183/sec), cover: 141, uptime: 27s
2021/10/08 11:30:55 workers: 8, corpus: 5 (28s ago), crashers: 0, restarts: 1/8644, execs: 752059 (25068/sec), cover: 141, uptime: 30s
2021/10/08 11:30:58 workers: 8, corpus: 5 (31s ago), crashers: 0, restarts: 1/8685, execs: 851210 (25793/sec), cover: 141, uptime: 33s
2021/10/08 11:31:01 workers: 8, corpus: 5 (34s ago), crashers: 0, restarts: 1/8877, execs: 949880 (26385/sec), cover: 141, uptime: 36s
2021/10/08 11:31:04 workers: 8, corpus: 5 (37s ago), crashers: 0, restarts: 1/9041, execs: 1048803 (26892/sec), cover: 141, uptime: 39s
2021/10/08 11:31:07 workers: 8, corpus: 5 (40s ago), crashers: 0, restarts: 1/9092, execs: 1145632 (27276/sec), cover: 141, uptime: 42s
2021/10/08 11:31:10 workers: 8, corpus: 5 (43s ago), crashers: 0, restarts: 1/9193, execs: 1241160 (27581/sec), cover: 141, uptime: 45s
2021/10/08 11:31:13 workers: 8, corpus: 5 (46s ago), crashers: 0, restarts: 1/9279, execs: 1336216 (27837/sec), cover: 141, uptime: 48s
2021/10/08 11:31:16 workers: 8, corpus: 5 (49s ago), crashers: 0, restarts: 1/9209, execs: 1436755 (28171/sec), cover: 141, uptime: 51s
```

and the result is:

```sh
.
├── add
│   ├── add.go
│   └── fuzz.go
├── add-fuzz.zip
├── apps.go
├── corpus
│   ├── 5ba93c9db0cff93f52b521d7420e43f6eda2784f-1
│   ├── a135dd7a6aedaf92f3483eeb75cdcd7a1328a2f1-1
│   ├── d32ab8c7993a19d5c6bc39fb140c831f086f592c-1
│   ├── da39a3ee5e6b4b0d3255bfef95601890afd80709
│   └── f0e54a36ca10b48c0981984e895337de5de3fa8e-1
├── crashers
├── go.mod
├── go.sum
└── suppressions

4 directories, 11 files
```

if you got the crash you can see the result in folder crash.	