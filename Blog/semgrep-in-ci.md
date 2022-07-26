# Semgrep In CI/CD


<p align="center">
		<a href="https://semgrep.dev">
			<img src="https://raw.githubusercontent.com/returntocorp/semgrep/develop/semgrep.svg" height="100" alt="Semgrep logo"/>
		</a>
</p>
<h3 align="center">
	  Lightweight static analysis for many languages.
	  Find bugs and enforce code standards.
</h3>

## Apa itu CI/CD


<p style="text-align: justify; letter-spacing: 0.002em;">
Continuous integration (CI) adalah pengintegrasian kode ke dalam repositori kode kemudian menjalankan pengujian secara otomatis, cepat, dan sering. Kamu dapat melakukan CI ini dengan menggunakan perintah  commit. </p>

<p style="text-align: justify; letter-spacing: 0.002em;">
Sementara continous delivery atau continuous deployment (CD) adalah praktik yang dilakukan setelah proses CI selesai dan seluruh kode berhasil terintegrasi, sehingga aplikasi bisa dibangun lalu dirilis secara otomatis.
</p>

## Jadi, apa itu CI/CD?

<p style="text-align: justify; letter-spacing: 0.002em;">
Jadi, CI/CD adalah salah satu praktik DevSecOps yang digunakan untuk pengembangan perangkat lunak menjadi lebih terorganisir. Penggunaan CI/CD pada pengembangan aplikasi memberikan banyak manfaat yang akan langsung terasa oleh tim pengembang. Selain itu, ada banyak pilihan tool atau alat yang dapat digunakan untuk mendukung berjalannya proses CI/CD ini secara otomatis dan aman.</p>

## Scan manual menggunakan semgrep

<p style="text-align: justify; letter-spacing: 0.002em;">
Cukup dengan cara simple , jalankan command berikut pada root project anda.
</p>

```sh
$ semgrep --config=http://sast.netzen.net.id/scan
```
<p style="text-align: justify; letter-spacing: 0.002em;">
untuk detailsnya kunjungi http://sast.netzen.net.id
</p>


## Bagaimana caranya agar terintegrasi dengan CI/CD

pada github taruh pada folder .github/workflows

```yml
name: Semgrep

on:
  pull_request:
    types: [opened, reopened]
    branches: ['master','main']
  push:
    branches: ['master','main']
jobs:

  semgrep:
    name: Scan
    runs-on: ubuntu-latest
    container:
      image: returntocorp/semgrep
    # Skip any PR created by dependabot to avoid permission issues

    steps:
      - uses: actions/checkout@v3
      - run: semgrep ci --config=http://sast.netzen.net.id/scan/go
```