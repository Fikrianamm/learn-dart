# Catatan Belajar Dart

> Tanggal mulai: 26 Agustus 2026
> Sumber belajar: [dart.dev](https://dart.dev/learn/tutorial)

---

## 1. Project Baru di Dart

**Cara Membuat Project Baru di Dart:**

```dart
 dart create cli
 cd cli
 dart run
```

Direktori `bin/` berisi titik masuk Dart untuk sebuah program. Saat Anda menjalankan `dart run`, Dart akan mencari titik masuk paket Anda di direktori ini.

Fungsi `main` diperlukan dalam program Dart yang dapat dijalankan. Tanpanya, Dart tidak akan tahu dari mana harus mulai menjalankan kode Anda.

```dart
void main(List<String> arguments) {
  print('Hello, Dart!'); // Change this line
}
```

---

## 2. Tipe Data

**Apa yang saya pahami:**

### a. Tipe Data Bawaan (Built-in Types)

Dart merupakan bahasa statically typed, artinya setiap variabel memiliki tipe data yang pasti.

| Tipe Data | Deskripsi                           | Contoh                                          |
| --------- | ----------------------------------- | ----------------------------------------------- |
| int       | Bilangan Bulat                      | int umur = 25;                                  |
| double    | Bilangan desimal                    | double tinggi = 175.5;                          |
| num       | Tipe induk int dan double           | num angka = 10; atau 10.5                       |
| String    | Teks / karakter                     | "String nama = ""Fikri"";"                      |
| bool      | Nilai kebenaran                     | bool isActive = true;                           |
| List      | Kumpulan data terurut (Array)       | "List<int> angka = [1, 2, 3];"                  |
| Set       | Kumpulan data unik (tanpa duplikat) | "Set<String> tag = {'dart', 'flutter'};"        |
| Map       | Kumpulan pasangan key-value         | "Map<String, String> user = {'name': 'Fikri'};" |
| Symbol    | Penanda identifier internal         | Symbol s = #myMethod;                           |
| Null      | Mewakili nilai kosong               | Null emptyValue = null;                         |

### b. Kata Kunci Deklarasi (Keywords)

`var`, Tipe data dideteksi secara otomatis (type inference) berdasarkan nilai pertamanya. Tipe data tersebut terkunci setelah diinisialisasi.

**Contoh kode:**

```dart
var nama = "Fikri"; // Otomatis dianggap String
// nama = 123; // ERROR: Tipe data tidak bisa diubah ke int
```

`dynamic`, Digunakan jika variabel bisa berganti-ganti tipe data saat aplikasi berjalan (runtime). Mengabaikan pemeriksaan type-checking.

**Contoh kode:**

```dart
dynamic data = "Hallo";
data = 100; // BERHASIL: Bisa berganti tipe data
```

`Object/Object?`, Tipe data dasar tertinggi dari semua objek di Dart (kecuali Null pada non-nullable). Lebih aman daripada dynamic karena tetap melalui pemeriksaan tipe data.

**Contoh kode:**

```dart
Object nilai = "Teks";
```

### c. Pengontrol Mutabilitas & Immutability

`final`, Nilai hanya bisa diisi satu kali (ditentukan saat runtime).

```dart
final waktu = DateTime.now();
```

`const`, Nilai bersifat konstan mutlak dan harus sudah diketahui saat kompilasi (compile-time).

```dart
const double pi = 3.14;

```

`late`, Menandai variabel non-nullable yang inisialisasi nilainya ditunda sampai variabel tersebut pertama kali diakses.

```dart
late String deskripsi;
// ... kode lain ...
deskripsi = "Inisialisasi nanti";
```

### d. Null Safety Variable

Secara default, variabel di Dart tidak boleh bernilai null (non-nullable). Untuk mengizinkan null, tambahkan tanda tanya (?) di akhir tipe data.

```dart
String? email; // Boleh bernilai null
email = null;  // BERHASIL
```

---

## 3. Function

**Apa yang saya pahami:**

```dart
const version = '0.0.1'; // Add this line

void main(List<String> arguments) {
  if (arguments.isEmpty || arguments.first == 'help') {
    printUsage(); // Change this from 'Hello, Dart!'
  } else if (arguments.first == 'version') {
    print('Dartpedia CLI version $version');
  } else {
    printUsage(); // Catch-all for any unrecognized command.
  }
}

void printUsage() { // new function
  print(
    "The following commands are valid: 'help', 'version', 'search <ARTICLE-TITLE>'"
  );
}
```

- `arguments.isEmpty` memeriksa apakah tidak ada argumen baris perintah yang diberikan.
- `arguments.first` mengakses argumen paling awal, yang Anda gunakan sebagai perintah.
- `version` dideklarasikan sebagai `const`. Ini berarti nilainya diketahui pada saat kompilasi, dan Anda tidak dapat mengubahnya saat program berjalan (_runtime_).
- `arguments` adalah variabel biasa (bukan konstanta) karena isinya dapat berubah saat program berjalan, bergantung pada input pengguna.

```dart
void main(List<String> arguments) {
  if (arguments.isEmpty || arguments.first == 'help') {
    printUsage();
  } else if (arguments.first == 'version') {
    print('Dartpedia CLI version $version');
  } else if (arguments.first == 'search') {
    // Add this new block:
    final inputArgs = arguments.length > 1 ? arguments.sublist(1) : null;
    searchWikipedia(inputArgs);
  } else {
    printUsage();
  }
}
```

- Variabel `final` hanya dapat ditetapkan nilainya satu kali dan digunakan ketika Anda tidak berniat mengubah variabel tersebut lagi di dalam kode.
- `arguments.sublist(1)` membuat daftar baru yang berisi semua elemen dari daftar `arguments` setelah elemen pertama (yaitu `search`).
- `arguments.length > 1 ? ... : null;` adalah operator kondisional (ternary). Operator ini memastikan bahwa jika tidak ada argumen yang diberikan setelah perintah `search`, `inputArgs` akan bernilai `null`, sesuai dengan perilaku kode contoh untuk parameter `arguments` pada `searchWikipedia` yang bertipe `List<String>?`.

`stdin.readLineSync()` adalah fungsi dalam Dart yang digunakan untuk membaca teks yang diketik oleh pengguna secara sinkron.

---

## 4. Async/Await & Future

**Apa yang saya pahami:**

```dart
Future<String> getWikipediaArticle(String articleTitle) async {
  final url = Uri.https(
    'en.wikipedia.org',
    '/api/rest_v1/page/summary/$articleTitle',
  );
  final response = await http.get(url); // Make the HTTP request

  if (response.statusCode == 200) {
    return response.body; // Return the response body if successful
  }

  // Return an error message if the request failed
  return 'Error: Failed to fetch article "$articleTitle". Status code: ${response.statusCode}';
}

```

- Tipe kembalian `Future<String>` menunjukkan bahwa fungsi ini pada akhirnya akan menghasilkan hasil String, tetapi tidak segera, karena ini adalah operasi asinkron.
- Kata kunci `async` menandai fungsi sebagai asinkron, memungkinkan Anda untuk menggunakan await di dalamnya.

---

## 5. Class & Package

**Apa yang saya pahami:**

command membuat package:

```dart
dart create -t package command_runner
```

- `-t package`, menginstruksikan Dart untuk menggunakan template Package/Library. pembuatan package/library berguna untuk diimport oleh proyek Dart/Flutter lain.

```dart
library;

export 'src/command_runner_base.dart';
```

- `library;`, mendeklarasikan bahwa file ini adalah library, yang mendefinisikan batasan dan antarmuka publik dari unit kode Dart yang dapat digunakan kembali.
- `export`, merupakan baris krusial yang membuat deklarasi dari `command_runner_base.dart` tersedia bagi paket-paket lain yang mengimpor paket `command_runner`. Tanpa pernyataan `export` ini, kelas dan fungsi di dalam `command_runner_base.dart` akan bersifat privat bagi paket `command_runner`, sehingga Anda tidak dapat menggunakannya dalam aplikasi Anda.

buat kelas pada folder `lib/src/..`
contoh:

```dart
class CommandRunner {
  /// Runs the command-line application logic with the given arguments.
  Future<void> run(List<String> input) async {
    print('CommandRunner received arguments: $input');
  }
}
```

jika ingin menggunakan library tersebut pada project lain tambahkan pada dependency `pubspec.yaml`:

```dart
dependencies:
  http: ^1.3.0 # Keep your existing http dependency
  command_runner:
    path: ../command_runner # Points to your local command_runner package
```

kemudian `dart pub get` dan gunakan dengan mengimport `import 'package:command_runner/command_runner.dart';`

```dart
import 'dart:io';
import 'package:http/http.dart' as http;
import 'package:command_runner/command_runner.dart';

void main(List<String> arguments) async { // main is now async and awaits the runner
  var runner = CommandRunner(); // Create an instance of your new CommandRunner
  await runner.run(arguments); // Call its run method, awaiting its Future<void>
}
```

---

## 6. Object-Oriented Programming (OOP) in Dart

### a. Abstract Class

`abstract class` di Dart mirip seperti sketsa/cetak biru yang belum jadi atau sebuah kontrak. Kelas ini tidak bisa dipakai langsung (tidak bisa di-instansiasi dengan new atau Arguments()), melainkan hanya dibuat sebagai standar dasar untuk diturunkan (extended) oleh kelas-kelas anak lainnya (seperti Option atau Command).

```dart
abstract class Arguments {
  String get name;
  String? get help;

  Object? get defaultValue;
  String? get valueHelp;

  String get usage;
}
```

- enum OptionType { flag, option }: Pilihan pasti yang terbatas (hanya bisa bertipe flag atau option).
- String get name;: Getter tanpa isi { ... }. Ini adalah janji/kontrak bahwa semua kelas anak wajib punya properti name bertipe String.
- String? get help;: Tanda ? artinya nilai kembaliannya boleh null (opsional).
- Object? get defaultValue;: Boleh mengembalikan tipe data apa saja (int, bool, String, dll) atau null.

### b. Inheritance Class

`Class` turunan dari abstract class yang variabel/getter-nya di-override menyesuaikan kebutuhan class tersebut.

```dart
class Option extends Argument {
  Option(   //Contsructor
    this.name,
    { // named parameters (parameter yang opsional)
    required this.type,
    this.help,
    this.abbr,
    this.defaultValue,
    this.valueHelp,}
  );

  @override
  final String name;

  final OptionType type; // fitur/properti tambahan khusus yang hanya dimiliki oleh Option (bukan warisan)

  @override
  final String? help;

  final String? abbr;

  @override
  final Object? defaultValue;

  @override
  final String? valueHelp;

  @override
  String get usage {
    if (abbr != null) {
      return '-$abbr,--$name: $help';
    }

    return '--$name: $help';
  }
}
```

### c. Enkapsulasi, UnmodifiableSetView, & FutureOr

Mengombinasikan variabel privat, perlindungan akses data, dan tipe eksekusi fleksibel pada kelas Command.

```dart
import 'dart:async';
import 'dart:collection';

abstract class Command extends Arguments {
  String get description;

  // Private field (diawali '_'), tidak bisa diakses langsung dari luar file/library
  final List<Option> _options = [];

  // Read-only getter yang membungkus _options menjadi Set tak ubah
  UnmodifiableSetView<Option> get options => UnmodifiableSetView(_options.toSet());

  void addFlag(String name, {String? help}) {
    _options.add(Option(name, type: OptionType.flag, help: help));
  }

  // FutureOr<T> mengizinkan run() ditulis secara sync (return Object?) atau async (return Future<Object?>)
  FutureOr<Object?> run(ArgResults args);
}
```

- `_options`: Awalan \_ menandakan variabel bersifat privat.

- `UnmodifiableSetView`: Mencegah pihak luar menambah/menghapus isi list secara langsung (\_options.add()). .toSet() mengonversi List menjadi Set tanpa duplikat.

- `FutureOr<Object?>`: Membebaskan implementasi perintah apakah ingin mengembalikan nilai secara langsung (sync) atau via Future (async).

---

### d. Record Types (ArgResults)

Dart 3+ mendukung Record Types ({TypeA a, TypeB b}) untuk mengembalikan beberapa nilai sekaligus tanpa perlu membuat kelas pembungkus baru.

```dart
class ArgResults {
  Map<Option, Object?> options = {};

  // Mengembalikan Record berupa pasangan Option dan nilainya
  ({Option option, Object? input}) getOption(String name) {
    var mapEntry = options.entries.firstWhere(
      (entry) => entry.key.name == name || entry.key.abbr == name,
    );

    return (option: mapEntry.key, input: mapEntry.value);
  }
}
```

### e. Contoh Penggunaan Lengkap (Method Cascade ..)

Penerapan seluruh konsep OOP Dart dalam eksekusi CLI:

```dart
class SearchCommand extends Command {
  SearchCommand() {
    addFlag('verbose', help: 'Menampilkan log detail');
  }

  @override
  String get name => 'search';

  @override
  String get description => 'Mencari artikel Wikipedia';

  @override
  // Implementasi sync/async sesuai janji FutureOr
  Future<String> run(ArgResults args) async {
    return 'Hasil pencarian...';
  }
}

void main() async {
  // Method Cascade (..) memanggil method berturut-turut pada objek yang baru diinstansiasi
  var searchCmd = SearchCommand()
    ..addFlag('exact', help: 'Pencarian persis');

  print('Command Usage: ${searchCmd.usage}');
}
```

## 7. Error Handling

**Apa yang saya pahami:**

Error Handling digunakan untuk menangani kesalahan (_exception_) saat program berjalan (_runtime_) agar aplikasi tidak _crash_ secara tiba-tiba.

### a. `try-on-catch-finally` Block

Struktur utama untuk menangkap dan mengelola exception:

```dart
void main() {
  try {
    int result = 12 ~/ 0; // Membagi dengan nol (IntegerDivisionByZeroException)
    print(result);
  } on UnsupportedError {
    // 'on' digunakan untuk menangkap tipe Exception spesifik
    print('Gagal: Tidak bisa membagi angka dengan nol!');
  } catch (e, s) {
    // 'catch' menangkap semua jenis error umum
    // e = objek error, s = stack trace (lacak baris kode error)
    print('Terjadi error: $e');
    print('Stack trace: $s');
  } finally {
    // 'finally' SELALU dieksekusi, baik terjadi error maupun tidak
    print('Proses selesai (pembersihan resource).');
  }
}
```

- `on <ExceptionType>`: Digunakan jika kamu sudah tahu jenis error spesifik yang mungkin terjadi dan ingin memberikan penanganan khusus.
- `catch (e, stackTrace)`: Menangkap objek error (e) beserta riwayat baris kode penyebab error (stackTrace).

- `finally`: Cocok untuk pembersihan resource (seperti menutup koneksi database, file, atau koneksi HTTP).

### b. Throw Error

```dart
void validateAge(int age) {
  if (age < 0) {
    // throw melempar exception baru
    throw ArgumentError('Umur tidak boleh negatif!');
  }
}

void processUser() {
  try {
    validateAge(-5);
  } catch (e) {
    print('Log internal: $e');
    rethrow; // Melempar kembali error yang sama ke fungsi pemanggil di atasnya
  }
}
```

- `throw`: Menghentikan eksekusi normal dan melempar objek Exception atau kustom error.

- `rethrow`: Meneruskan error yang ditangkap ke blok try-catch tingkat di atasnya setelah melakukan logging lokal.

### c. Handling Error pada Asynchronous(`Future` & `async/await`)

Menangani error pada proses async (seperti HTTP Request/Database) menggunakan `try-catch` standar dengan `await`.

```dart
Future<void> fetchData() async {
  try {
    var response = await http.get(Uri.parse('[https://invalid-url.com](https://invalid-url.com)'));
    print(response.body);
  } catch (e) {
    print('Gagal mengambil data dari API: $e');
  }
}
```

## 8. Advanced OOP

**Apa yang saya pahami:**

Fitur-fitur pemrograman berorientasi objek tingkat lanjut di Dart untuk meningkatkan reusabilitas kode, ekstensi fungsi, dan fleksibilitas tipe data.

---

### a. Mixins (`with`)

Mixins memungkinkan kita menambahkan kemampuan/fungsi dari suatu kelas ke kelas lain **tanpa hubungan hierarki pewarisan (inheritance)**.

```dart
mixin Logger {
  void log(String message) {
    print('[LOG ${DateTime.now()}]:$message');
  }
}

// Menggunakan kata kunci 'with'
class UserService with Logger {
  void saveUser(String name) {
    log('User $name berhasil disimpan'); // Memakai fungsi dari mixin
  }
}
```

- Kapan dipakai: Saat butuh berbagi kode/fitur yang sama ke banyak kelas yang tidak saling berhubungan (multiple inheritance di Dart diwakili oleh Mixin).

### b. Extension Methods (extension on)

Fitur untuk menambahkan method baru ke class yang sudah ada (termasuk class bawaan seperti `String`, `List`, `int`) tanpa mengubah kode sumber aslinya.

```dart
// Menambahkan method khusus ke tipe data String bawaan Dart
extension StringCapitalize on String {
  String capitalize() {
    if (isEmpty) return this;
    return '${this[0].toUpperCase()}${substring(1)}';
  }
}

void main() {
  String nama = 'fikri';
  print(nama.capitalize()); // Output: "Fikri"
}
```

### c. Enhanced Enums (Enum dengan Properti & Method)

Di Dart 2.17+, `enum` tidak hanya sekadar daftar konstanta, tetapi juga bisa memiliki properti, constructor, dan method.

```dart
enum StatusKoneksi {
  connected(200, 'Terhubung'),
  notFound(404, 'Halaman Tidak Ditemukan'),
  serverError(500, 'Kesalahan Server');

  // Properti & Constructor
  final int code;
  final String description;
  const StatusKoneksi(this.code, this.description);

  // Method di dalam enum
  bool get isSuccess => code == 200;
}

void main() {
  var status = StatusKoneksi.notFound;
  print(status.description); // Output: "Halaman Tidak Ditemukan"
  print(status.isSuccess);    // Output: false
}
```

### d. Generics (`<T>`)

Generics membuat kode kita fleksibel terhadap berbagai tipe data sambil tetap mempertahankan pemeriksaan keamanan tipe data (type safety).

```dart
// Class umum yang bisa menampung data tipe apa saja
class DataHolder<T> {
  T data;
  DataHolder(this.data);

  T getData() => data;
}

void main() {
  var intHolder = DataHolder<int>(100);       // Menampung int
  var stringHolder = DataHolder<String>('A'); // Menampung String

  print(intHolder.getData()); // 100
}
```

- `<T>`: Type Parameter (konvensi penamaan: T untuk Type, E untuk Element, K/V untuk Key/Value).

## 9. Working with Data & JSON

**Apa yang saya pahami:**

Di Dart, data JSON dari API biasanya masuk dalam bentuk `String`. Untuk mengolahnya, data tersebut di-parse (_decode_) menjadi `Map<String, dynamic>` atau `List<dynamic>`, lalu diubah menjadi objek kelas (_Model_) agar _type-safe_.

---

### a. Melakukan Parsing JSON (`dart:convert`)

Sistem bawaan Dart menyediakan library `dart:convert` dengan fungsi `jsonDecode()` dan `jsonEncode()`.

```dart
import 'dart:convert';

void main() {
  // 1. JSON String (biasanya diterima dari HTTP Response)
  String jsonString = '{"name": "Fikri", "age": 22, "is_active": true}';

  // 2. Decode: String JSON -> Map<String, dynamic>
  Map<String, dynamic> userMap = jsonDecode(jsonString);
  print(userMap['name']); // Output: Fikri

  // 3. Encode: Map -> String JSON
  String rawJson = jsonEncode(userMap);
  print(rawJson);
}
```

### b. Manual Serialization (Factory Constructor fromJson)

Untuk keamanan tipe data (type safety), ubah Map hasil dekode JSON menjadi objek Model Class menggunakan `factory constructor`.

```dart
class UserModel {
  final String name;
  final int age;
  final bool isActive;

  UserModel({
    required this.name,
    required this.age,
    required this.isActive,
  });

  // Factory Constructor untuk mengonversi Map dari JSON ke Objek UserModel
  factory UserModel.fromJson(Map<String, dynamic> json) {
    return UserModel(
      name: json['name'] as String,
      age: json['age'] as int,
      isActive: json['is_active'] as bool,
    );
  }

  // Method untuk mengonversi Objek kembali ke Map (JSON)
  Map<String, dynamic> toJson() {
    return {
      'name': name,
      'age': age,
      'is_active': isActive,
    };
  }
}

void main() {
  String jsonInput = '{"name": "Fikri", "age": 22, "is_active": true}';

  // Mengonversi string JSON langsung ke Objek Model
  var user = UserModel.fromJson(jsonDecode(jsonInput));

  print(user.name); // Output: Fikri (Terproteksi autocomplete & type check)
}
```

### c. Mengolah List of JSON Objects

Jika API mengembalikan array/list of JSON, gunakan .map() untuk mengonversi setiap item menjadi objek model.

```dart
void main() {
  String jsonListString = '''
  [
    {"name": "Fikri", "age": 22, "is_active": true},
    {"name": "Anam", "age": 24, "is_active": false}
  ]
  ''';

  List<dynamic> rawList = jsonDecode(jsonListString);

  // Konversi List<dynamic> -> List<UserModel>
  List<UserModel> users = rawList
      .map((item) => UserModel.fromJson(item as Map<String, dynamic>))
      .toList();

  print(users.first.name); // Output: Fikri
}
```

- `jsonDecode()`: Mengubah teks JSON menjadi struktur data Dart (Map/List).

- `UserModel.fromJson()`: Pola standar (pattern) di Flutter/Dart untuk memetakan field JSON ke variabel class secara eksplisit.

## 10. Testing in Dart

**Apa yang saya pahami:**

Testing digunakan untuk memastikan kode berjalan sesuai harapan secara otomatis dan mencegah _regression_ (fitur lama rusak saat ada kode baru). Dart menyediakan paket resmi `test` untuk kebutuhan ini.

---

### a. Setup Package Testing

Tambahkan dependency `test` ke bagian `dev_dependencies` di `pubspec.yaml`:

```yaml
dev_dependencies:
  test: ^1.24.0
```

Jalankan perintah pengujian lewat terminal: `dart test`

- Catatan: Dart akan secara otomatis mencari dan menjalankan seluruh file di dalam folder test/ yang berakhiran \_test.dart (contoh: test/calculator_test.dart).

### b. Menulis Unit Test Sederhana (test & expect)

Gunakan fungsi test() untuk mendefinisikan skenario dan expect() untuk membandingkan nilai hasil eksekusi dengan nilai ekspektasi.

```dart
import 'package:test/test.dart';

// Fungsi sederhana yang ingin diuji
int add(int a, int b) => a + b;

void main() {
  test('Fungsi add harus mengembalikan hasil penjumlahan dua angka', () {
    int result = add(2, 3);

    // expect(nilai_aktual, nilai_yang_diharapkan)
    expect(result, equals(5));
  });
}
```

### c. Pengelompokan Test Suite (group)

Gunakan group() untuk mengorganisasi beberapa pengujian yang saling berhubungan agar laporan hasil test lebih rapi.

```dart
import 'package:test/test.dart';

void main() {
  group('Pengujian Operasi Matematika', () {
    test('Penjumlahan', () {
      expect(1 + 1, equals(2));
    });

    test('Pengurangan', () {
      expect(5 - 2, equals(3));
    });
  });
}
```

### d. Testing Asynchronous Code & Exception

Gunakan await untuk fungsi async atau matcher throwsA untuk menguji error yang dilempar.

```dart
import 'package:test/test.dart';

Future<String> fetchUsername() async {
  return Future.delayed(Duration(milliseconds: 100), () => 'Fikri');
}

void main() {
  test('Memastikan fetchUsername mengembalikan nama pengguna', () async {
    String username = await fetchUsername();
    expect(username, equals('Fikri'));
  });

  test('Memastikan pembagian dengan nol melempar exception', () {
    expect(() => 12 ~/ 0, throwsA(isA<UnsupportedError>()));
  });
}
```

- `test()`: Menandai satu unit pengujian individual.

- `expect(actual, matcher)`: Memvalidasi apakah keluaran kode sesuai harapan.

- `group()`: Wadah untuk mengelompokkan skenario test terkait.

- `dart test`: Perintah CLI untuk mengeksekusi pengujian secara otomatis.

## 11. Fetch Data Over the Network

**Apa yang saya pahami:**

Untuk mengambil data dari internet (REST API), Dart menggunakan paket resmi `http`. Proses pengambilan data bersifat _asynchronous_ (`Future`), dan hasilnya perlu diproses menggunakan perkondisian kode status HTTP (`statusCode`).

---

### a. Setup Package `http`

Tambahkan dependency `http` ke `pubspec.yaml`:

```yaml
dependencies:
  http: ^1.3.0
```

Lalu impor paket pada file Dart menggunakan alias (as http):

```dart
import 'package:http/http.dart' as http;
```

### b. Mengambil Data via HTTP GET Request

Gunakan http.get() dengan konversi URL menggunakan Uri.parse().

```dart
import 'dart:convert';
import 'package:http/http.dart' as http;

// Model data sederhananya
class Post {
  final int id;
  final String title;

  Post({required this.id, required this.title});

  factory Post.fromJson(Map<String, dynamic> json) {
    return Post(
      id: json['id'] as int,
      title: json['title'] as String,
    );
  }
}

// Fungsi Fetch Data
Future<Post> fetchPost() async {
  final response = await http.get(
    Uri.parse('[https://jsonplaceholder.typicode.com/posts/1](https://jsonplaceholder.typicode.com/posts/1)'),
  );

  // Kode status 200 OK berarti permintaan berhasil
  if (response.statusCode == 200) {
    // 1. Decode String body ke Map
    Map<String, dynamic> jsonMap = jsonDecode(response.body);

    // 2. Ubah ke Objek Model
    return Post.fromJson(jsonMap);
  } else {
    // Melempar error jika gagal (misal: 404, 500)
    throw Exception('Gagal memuat post. Status code: ${response.statusCode}');
  }
}

void main() async {
  try {
    Post post = await fetchPost();
    print('Judul Post: ${post.title}');
  } catch (e) {
    print('Error: $e');
  }
}
```

### c. Mengirim Data via HTTP POST Request

Gunakan http.post() dengan menyertakan headers dan body yang di-jsonEncode.

```dart
Future<Post> createPost(String title) async {
  final response = await http.post(
    Uri.parse('[https://jsonplaceholder.typicode.com/posts](https://jsonplaceholder.typicode.com/posts)'),
    headers: <String, String>{
      'Content-Type': 'application/json; charset=UTF-8',
    },
    body: jsonEncode(<String, String>{
      'title': title,
    }),
  );

  // Kode status 201 Created berarti data baru berhasil dibuat
  if (response.statusCode == 201) {
    return Post.fromJson(jsonDecode(response.body));
  } else {
    throw Exception('Gagal membuat post.');
  }
}
```

- `Uri.parse('https://...')`: Memvalidasi dan mengonversi alamat String menjadi objek Uri yang dibutuhkan oleh paket http.

- `response.statusCode`: Memeriksa respons server (200 = Berhasil, 201 = Berhasil Dibuat, 404 = Not Found, 500 = Server Error).

- `response.body`: Mengambil teks respon mentah dari server sebelum di-decode oleh jsonDecode().

## 12. Logging in Dart

**Apa yang saya pahami:**

Menggunakan `print()` untuk debugging sangat terbatas karena tidak bisa mengatur level prioritas pesan, tidak ada info timestamp otomatis, dan sulit dimatikan saat aplikasi rilis. Paket resmi `logging` menyediakan sistem pencatatan log yang terstruktur dan fleksibel.

---

### a. Setup Package `logging`

Tambahkan dependency `logging` ke `pubspec.yaml`:

```yaml
dependencies:
  logging: ^1.2.0
```

Lalu import di file Dart:

```dart
import 'package:logging/logging.dart';
```

### b. Konfigurasi Hierarki Log & Listener

Sebelum mencatat log, kita perlu mengatur tingkat hierarki (level) pesan yang ingin ditampilkan dan mendengarkan (listen) pesan log yang keluar.

```dart
void setupLogging() {
  // 1. Atur level minimum log yang ingin ditangkap (misal: INFO ke atas)
  Logger.root.level = Level.ALL;

  // 2. Tambahkan listener untuk menangani output log (misal: print ke konsol)
  Logger.root.onRecord.listen((record) {
    print('[${record.level.name}]${record.time}: ${record.loggerName} -${record.message}');
  });
}
```

### c. Menggunakan Logger di dalam Aplikasi

Buat instance Logger khusus untuk setiap modul/class agar asal-usul pesan log mudah dilacak.

```dart
final _log = Logger('UserService');

class UserService {
  void login(String username) {
    _log.info('Mencoba login untuk user: $username');

    try {
      // Simulasi proses
      if (username.isEmpty) {
        throw ArgumentError('Username tidak boleh kosong!');
      }
      _log.fine('Login berhasil untuk user: $username');
    } catch (e, stackTrace) {
      // Menulis log level SEVERE beserta objek error dan stack trace-nya
      _log.severe('Gagal melakukan login', e, stackTrace);
    }
  }
}

void main() {
  setupLogging(); // Inisialisasi listener log terlebih dahulu

  var service = UserService();
  service.login('Fikri');
  service.login(''); // Akan memicu log SEVERE
}
```

### d. Tingkatan Level Log Utama

Paket logging menyediakan beberapa tingkatan bawaan berdasarkan urgensinya:

| Level              | Penggunaan                                                   |
| ------------------ | ------------------------------------------------------------ |
| Level.FINE / FINER | Log detail/tracing internal untuk proses debugging mendalam. |
| Level.INFO         | Informasi umum terkait jalannya alur normal aplikasi.        |
| Level.WARNING      | Peringatan potensi masalah (aplikasi masih bisa berjalan).   |
| Level.SEVERE       | Error serius/fatal yang membutuhkan tindakan perbaikan.      |

- `Logger('NamaLogger')`: Membuat pemicu log teridentifikasi berdasarkan nama komponennya.

- `Logger.root.level`: Mengontrol batas minimum log yang akan diproses (sangat berguna untuk menyaring log di lingkungan production vs development).

- `Logger.root.onRecord.listen()`: Pintu keluar utama untuk mengarahkan pesan log ke konsol, file penyimpan, atau layanan pemantauan pihak ketiga.