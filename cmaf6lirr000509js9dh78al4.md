---
title: "Aplikasi Todo (CLI)"
datePublished: Thu May 08 2025 09:45:07 GMT+0000 (Coordinated Universal Time)
cuid: cmaf6lirr000509js9dh78al4
slug: aplikasi-todo-cli
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1746697489206/f61d6748-6271-435c-a4b7-37ecd05e9d4f.png
tags: go

---

## Tujuan

Membuat aplikasi **Command Line Interface (CLI)** untuk mengelola daftar tugas langsung dari terminal.

Contoh perintah dasar:

```plaintext
tasks
```

## Fitur Utama

Aplikasi harus mampu melakukan operasi CRUD pada data tugas yang disimpan dalam file. Operasi-operasi tersebut meliputi:

```plaintext
tasks add "Tugas baru saya"
tasks list
tasks complete <taskid>
tasks delete <taskid>
```

### Perintah `add`

Digunakan untuk menambahkan tugas baru ke penyimpanan data.

Contoh:

```plaintext
tasks add "Rapikan meja kerja"
```

Akan menambahkan tugas baru dengan deskripsi **"Rapikan meja kerja"**.

### Perintah `list`

Menampilkan daftar semua tugas **yang belum selesai**, dengan opsi untuk menampilkan semua tugas, baik yang selesai maupun belum.

Contoh (tugas belum selesai):

```plaintext
tasks list
ID    Tugas                                             Dibuat
1     Rapikan meja kerja                               1 menit yang lalu
3     Ubah pengaturan keyboard ke mode escape/control  beberapa detik yang lalu
```

Contoh (semua tugas):

```plaintext
tasks list -a
ID    Tugas                                             Dibuat                Selesai
1     Rapikan meja kerja                               2 menit yang lalu     1 menit yang lalu
2     Tulis dokumentasi fitur baru                     1 menit yang lalu     1 menit yang lalu
3     Ubah pengaturan keyboard ke mode escape/control  1 menit yang lalu     1 menit yang lalu
```

### Perintah `complete`

Menandai suatu tugas sebagai selesai.

Contoh:

```plaintext
tasks complete 3
```

Akan menandai tugas dengan ID 3 sebagai telah selesai.

### Perintah `delete`

Menghapus tugas dari data penyimpanan.

Contoh:

```plaintext
tasks delete 2
```

Akan menghapus tugas dengan ID 2.

---

## Package yang Digunakan

* `strconv` – Untuk konversi antar tipe data.
    
* `text/tabwriter` – Untuk menampilkan output terformat di terminal.
    
* `os` – Untuk akses file sistem.
    
* [`github.com/spf13/cobra`](http://github.com/spf13/cobra) – Untuk pembuatan CLI.
    
* [`github.com/mergestat/timediff`](http://github.com/mergestat/timediff) – Untuk menampilkan waktu dalam format yang mudah dibaca (misalnya: "1 jam yang lalu").
    

---

## Sumber Tambahan

---

## Pertimbangan Teknis

### Stdout vs Stderr

* **Output hasil** harus ditulis ke **stdout**.
    
* **Pesan error atau diagnosa** harus ditulis ke **stderr**.
    

### File Locking

Untuk mencegah konflik akses file dari proses lain secara bersamaan, gunakan `flock` untuk *exclusive lock*:

```go
func loadFile(filepath string) (*os.File, error) {
	f, err := os.OpenFile(filepath, os.O_RDWR|os.O_CREATE, os.ModePerm)
	if err != nil {
		return nil, fmt.Errorf("gagal membuka file")
	}

	if err := syscall.Flock(int(f.Fd()), syscall.LOCK_EX); err != nil {
		_ = f.Close()
		return nil, err
	}

	return f, nil
}
```

Untuk melepas lock dan menutup file:

```go
func closeFile(f *os.File) error {
	syscall.Flock(int(f.Fd()), syscall.LOCK_UN)
	return f.Close()
}
```

---

## Fitur Tambahan (Opsional)

* Ubah field `IsComplete` menjadi timestamp (waktu penyelesaian), bukan boolean.
    
* Tambahkan tanggal jatuh tempo (`due date`) opsional pada setiap tugas.