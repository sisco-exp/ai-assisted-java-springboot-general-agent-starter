# AGENTS.md

## 1. Stack & Commands

- Java 21, Spring Boot 4.1.1 (`jakarta.*`), Maven.
- Testing: JUnit Jupiter, AssertJ, Mockito.
- Build dan test: `mvn -q verify`
- Test spesifik: `mvn -q test -Dtest=NamaTest`
- Run aplikasi: `mvn spring-boot:run`
- API Jakarta EE memakai `jakarta.*`. Jangan `javax.*` untuk persistence, validation, servlet, atau annotation.

## 2. Prioritas Keputusan

Urutan prioritas:

1. Correctness dan security.
2. Requirement dan scope.
3. Arsitektur dan layering.
4. Simplicity dan maintainability.
5. Konsistensi dengan codebase.

## 3. Arsitektur & Dependency Rules

Struktur package utama:
- `controller`
- `service`
- `repository`
- `model`
- `dto`
- `exception`
- `config`

Package `client` boleh ditambahkan hanya untuk integrasi eksternal.

### Dependency Direction

- Alur utama: `controller -> service -> repository -> model`.
- `controller` dan `service` boleh menggunakan `dto`.
- Client eksternal hanya dipanggil oleh `service`.
- `controller` tidak boleh mengakses `repository` langsung.
- `service` tidak boleh bergantung pada `controller` atau detail HTTP.
- `repository` tidak boleh memanggil `service` atau `controller`.
- `model` tidak boleh bergantung pada layer aplikasi atau DTO.

### Tanggung Jawab Layer

- **Controller:** menangani HTTP request, validasi input, pemanggilan service, mapping response DTO, dan status code. Tidak boleh berisi business logic, akses repository, atau transaction boundary.
- **Service:** menangani business logic, validasi bisnis, orchestration, dan transaksi.
- **Repository:** menangani akses data menggunakan Spring Data/JPA. Tidak boleh berisi business logic.
- **Model:** berisi entity dan enum domain.
- **DTO:** berisi request dan response API. Jangan mengekspos entity langsung.
- **Exception:** berisi exception bisnis dan `@RestControllerAdvice`.
- **Config:** berisi konfigurasi teknis dan deklarasi bean, bukan business logic.

## 4. Scope & Simplicity

- Implementasikan hanya requirement yang diminta.
- Pilih perubahan terkecil yang tetap memenuhi requirement dan aturan arsitektur.
- Gunakan fitur Java dan Spring yang sudah tersedia.
- Buat interface hanya untuk boundary nyata, seperti repository dan client eksternal.
- Jangan membuat interface untuk class internal dengan satu implementasi tanpa kebutuhan.
- Jangan menambahkan pattern, layer, module, helper, util, mapper, wrapper, flag, config, fallback, hook, atau extension point tanpa kebutuhan nyata.
- Jangan mengubah, memindahkan, atau me-rename kode di luar scope.
- Sebelum membuat class baru, cari pola serupa di codebase.
- Jika solusi membutuhkan abstraksi atau perubahan arsitektur yang belum jelas, tanyakan terlebih dahulu.
- Jangan membuat file Markdown baru tanpa diminta.
- Jangan mengubah migrasi database lama, file generated, `.env`, atau direktori `target/`.

## 5. Coding Standards & Documentation

- Gunakan constructor injection dengan field `final` untuk Spring component.
- Jangan gunakan field injection melalui `@Autowired`.
- Gunakan nama yang jelas dan sesuai domain.
- Hindari nama generik seperti `Helper`, `Util`, `Manager`, `Common`, dan `Processor`.
- Buat method dengan satu tanggung jawab utama dan tingkat abstraksi konsisten.
- Hindari nesting berlebihan; gunakan early return jika lebih jelas.
- Gunakan `record` untuk DTO/value carrier yang sesuai.
- Gunakan `Optional` hanya sebagai return value.
- Gunakan `var` hanya jika tipe tetap jelas.
- Hapus dead code, commented-out code, dan import tidak terpakai.
- Komentar menjelaskan alasan, bukan mengulang implementasi.
- Tambahkan Javadoc hanya untuk API publik baru yang tidak self-explanatory.

## 6. Error Handling, Logging & Security

- Gunakan exception bisnis yang spesifik.
- Status dan body error HTTP hanya ditetapkan di `@RestControllerAdvice`.
- Jangan gunakan empty catch, menelan exception, atau `printStackTrace()`.
- Tangkap exception hanya untuk menambah konteks, menerjemahkan exception, atau memulihkan alur dengan benar.
- Gunakan SLF4J untuk logging.
- Jangan hardcode atau mencatat secret, token, password, API key, atau data sensitif.
- Gunakan parameterized query untuk input query.

## 7. Testing

- Setiap perilaku baru dan bug fix wajib memiliki test relevan.
- Gunakan pola Arrange–Act–Assert.
- Format nama test: `namaMethod_kondisi_hasilYangDiharapkan`.
- Semua assertion menggunakan AssertJ.
- Uji satu perilaku utama per test.
- Jangan mock DTO, record, atau value object sederhana.

### Strategi Pengujian

- **Service:** unit test tanpa Spring context, gunakan `@ExtendWith(MockitoExtension.class)`, `@Mock`, dan `@InjectMocks` jika sesuai.
- **Controller:** gunakan `@WebMvcTest`.
- **Repository:** gunakan `@DataJpaTest`.
- **Integration:** gunakan `@SpringBootTest` hanya jika unit/slice test tidak cukup.
- **Mocking dependency:** gunakan `@MockBean` untuk dependency service dalam pengujian yang memerlukan Spring context.

## 8. Anti-Halusinasi

- Gunakan hanya dependency yang tersedia di `pom.xml`.
- Gunakan API yang valid untuk Java 21 dan Spring Boot 4.1.1.
- Jangan mengarang class, method, field, konfigurasi, endpoint, tabel, atau kemampuan framework.
- Periksa codebase dan dokumentasi resmi sebelum menggunakan API yang belum dikenal.
- Jika masih ragu setelah verifikasi, tanyakan sebelum melanjutkan.

## 9. Workflow & Definition of Done

### Workflow

1. Pahami requirement dan scope.
2. Cari pola serupa di codebase.
3. Implementasikan perubahan terkecil yang sesuai arsitektur.
4. Tambahkan atau perbarui test.
5. Jalankan verifikasi.
6. Hapus perubahan di luar scope dari diff.

### Definition of Done

- Build dan test relevan berhasil.
- Dependency direction sesuai aturan.
- Tidak ada business logic di controller/repository.
- Service tidak bergantung pada HTTP/controller.
- Tidak ada dead code, import tidak terpakai, atau perubahan di luar scope.
- Dokumentasi kode baru memadai jika diperlukan.

Jalankan `mvn -q verify`, kecuali perubahan dokumentasi saja. Jika verifikasi gagal, laporkan hasil sebenarnya.

### Wajib Meminta Persetujuan

- Menambahkan dependency.
- Mengubah skema database.
- Mengubah kontrak API publik.
- Menambahkan layer, package, atau abstraksi di luar struktur yang ditetapkan.
- Mengubah versi framework, struktur package/module, atau konfigurasi build.
- Requirement ambigu yang memengaruhi keputusan implementasi.

## 10. Boundaries

- Jangan mengubah migrasi database lama.
- Jangan mengubah file generated, `.env`, atau direktori `target/`.
- Jangan commit secret atau kredensial.
- Jangan mengubah kode di luar scope permintaan.

## 11. Output & Communication

### Laporan Akhir

Wajib mencantumkan:

- **File yang diubah**
- **Ringkasan perubahan**
- **Alasan**
- **Test/validasi dan hasil**
- **Risiko atau hal yang sengaja tidak diubah**

### Gaya Komunikasi

- Langsung, ringkas, dan berorientasi tindakan.
- Hindari basa-basi dan penjelasan yang tidak diperlukan.

### Format Plan

Gunakan format berikut tanpa teks tambahan:

1. **Goal:** satu kalimat tujuan.
2. **Steps:**
- [ ] Langkah 1
- [ ] Langkah 2
3. **Files to Modify:** daftar file