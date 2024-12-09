RIFQI ALRASID
H1D022062
SHIFT C (BARU)


**STRUKTUR FOLDER**
lib/bloc
Berisi logika untuk fitur login (logout_bloc.dart) dan produk (produk_bloc.dart).
Mengelola komunikasi antara UI dan API.

lib/model
Menyimpan model data seperti produk.dart untuk memetakan respons API ke dalam objek Dart.

lib/ui
Berisi file-file yang mengatur antarmuka pengguna, seperti produk_page.dart, login_page.dart, produk_form.dart, dan produk_detail.dart.

lib/helpers
Biasanya menyimpan fungsi utilitas atau konfigurasi, seperti helper untuk HTTP request, autentikasi, atau pengelolaan shared preferences.

lib/main.dart
Titik awal aplikasi, mengatur tema dan navigasi awal aplikasi.

LOGIN
logout_bloc.dart
```
import 'package:shared_preferences/shared_preferences.dart';

class LogoutBloc {
  static Future<void> logout() async {
    SharedPreferences prefs = await SharedPreferences.getInstance();
    await prefs.clear(); // Menghapus data pengguna yang tersimpan.
  }
}
```
•	logout():
o	Menggunakan SharedPreferences untuk menyimpan data sesi pengguna.
o	Saat logout, fungsi ini membersihkan semua data tersimpan, sehingga pengguna harus login ulang.

login_page.dart (potongan login UI)
```
ElevatedButton(
  onPressed: () async {
    final response = await LoginBloc.login(username, password);
    if (response) {
      Navigator.pushReplacement(
        context,
        MaterialPageRoute(builder: (context) => ProdukPage()),
      );
    } else {
      ScaffoldMessenger.of(context).showSnackBar(
        const SnackBar(content: Text('Login gagal')),
      );
    }
  },
  child: const Text('Login'),
),
```
•	Memanggil LoginBloc.login untuk autentikasi.
•	Jika berhasil, navigasi diarahkan ke ProdukPage.
•	Jika gagal, menampilkan pesan kesalahan dengan SnackBar.


**CRUD**
produk_bloc.dart
```
import 'dart:convert';
import 'package:http/http.dart' as http;
import 'package:tokokita/model/produk.dart';

class ProdukBloc {
  static Future<List<Produk>> getProduks() async {
    final response = await http.get(Uri.parse('http://localhost/api/produk'));
    if (response.statusCode == 200) {
      List<dynamic> data = json.decode(response.body);
      return data.map((item) => Produk.fromJson(item)).toList();
    } else {
      throw Exception('Gagal memuat data produk');
    }
  }

  static Future<void> addProduk(Produk produk) async {
    final response = await http.post(
      Uri.parse('http://localhost/api/produk'),
      headers: {'Content-Type': 'application/json'},
      body: json.encode(produk.toJson()),
    );
    if (response.statusCode != 201) {
      throw Exception('Gagal menambahkan produk');
    }
  }
}
```
•	getProduks():
o	Mengambil daftar produk dari API dengan metode GET.
o	Mengonversi respons JSON menjadi daftar objek Produk.
•	addProduk():
o	Mengirim data produk baru ke API dengan metode POST.
o	Memanfaatkan json.encode() untuk mengonversi data menjadi format JSON.

produk_page.dart (potongan daftar produk)
```
FutureBuilder<List>(
  future: ProdukBloc.getProduks(),
  builder: (context, snapshot) {
    if (snapshot.hasError) print(snapshot.error);
    return snapshot.hasData
        ? ListProduk(list: snapshot.data)
        : const Center(child: CircularProgressIndicator());
  },
),
```
•	Menggunakan FutureBuilder untuk memuat data produk secara asinkron.
•	Menampilkan CircularProgressIndicator saat data masih dimuat.
produk_form.dart
```
ElevatedButton(
  onPressed: () async {
    Produk produk = Produk(
      namaProduk: namaController.text,
      hargaProduk: int.parse(hargaController.text),
    );
    await ProdukBloc.addProduk(produk);
    Navigator.pop(context);
  },
  child: const Text('Simpan'),
),
```
•	Membuat objek Produk dari input pengguna.
•	Menggunakan ProdukBloc.addProduk() untuk menambahkan produk baru ke API.
•	Menutup halaman form setelah data berhasil disimpan.

**API**
•	API untuk CRUD diimplementasikan di backend PHP:
o	GET /api/produk: Mengambil daftar produk.
o	POST /api/produk: Menambahkan produk baru.
•	Contoh integrasi API:
```
final response = await http.post(
  Uri.parse('http://localhost/api/produk'),
  headers: {'Content-Type': 'application/json'},
  body: json.encode(produk.toJson()),
);
```




# Screenshot

![image](https://github.com/user-attachments/assets/b182a29a-2264-4c2e-abdd-e83843b7ca39)
![image](https://github.com/user-attachments/assets/de06b893-6f3d-428d-95cf-36a7a2900fa1)
![image](https://github.com/user-attachments/assets/7978d595-20af-4870-8816-3110ae7dc909)
![image](https://github.com/user-attachments/assets/df4ea328-6cbe-4b94-8159-9eb69272ce4f)
![image](https://github.com/user-attachments/assets/c604fc4c-f12f-4f3f-864b-2b9fcfb586ef)
