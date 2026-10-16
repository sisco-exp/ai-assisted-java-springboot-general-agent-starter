# AI-Assisted Java Spring Boot – General Agent Starter

Starter kit aturan kerja untuk **AI coding agent** pada proyek **Java 21 + Spring Boot 4.1.1 + Maven**.

Repositori ini berisi `AGENTS.md` sebagai sumber kebenaran, plus skeleton Maven Spring Boot. Package layer di bawah `com.sisco.springbootstarter` sudah ada dan masih kosong. Ini **bukan** aplikasi domain yang siap jalan: belum ada class di `controller`, `service`, `repository`, `model`, `dto`, `exception`, atau `config`.

> **Skala yang cocok:** proyek kecil sampai menengah dengan arsitektur layered sederhana.

## Target Project

- Backend REST API berbasis Java 21 dan Spring Boot 4.1.1 (`jakarta.*`).
- Maven sebagai build tool.
- JUnit Jupiter, AssertJ, dan Mockito untuk testing.
- Base package: `com.sisco.springbootstarter`.
- Package layer yang sudah dibuat: `controller`, `service`, `repository`, `model`, `dto`, `exception`, `config`.
- Package `client` belum dibuat. Tambahkan hanya jika ada integrasi eksternal.

## Skala Aplikasi yang Cocok

| Skala | Kecocokan | Catatan |
|-------|-----------|---------|
| Prototipe / MVP / proyek pribadi | Sangat cocok | Aturan YAGNI menjaga kode tetap ringan. |
| Aplikasi kecil–menengah | Paling cocok | Layer `controller -> service -> repository -> model` sudah memadai. |
| Aplikasi menengah–besar | Cocok dengan penyesuaian | Tambahkan aturan modul, auth, migrasi DB, dan operasional. |
| Skala besar / domain kompleks | Kurang cocok | Perlu aturan arsitektur khusus seperti modular monolith, DDD, atau event-driven. |

## Cara Pakai

1. `AGENTS.md` sudah berada di root, sejajar `pom.xml`. AI agent membaca file itu sebelum bekerja.
2. Tambahkan kode hanya di package layer yang sudah ada.
3. Package `client` hanya dibuat saat ada integrasi eksternal, dan hanya dipanggil dari `service`.
4. Perintah: `mvn -q verify`, `mvn -q test -Dtest=NamaTest`, `mvn spring-boot:run`.

## Ringkasan Isi `AGENTS.md`

| Bagian | Inti aturan |
|--------|-------------|
| Stack & Commands | Java 21, Spring Boot 4.1.1 (`jakarta.*`, bukan `javax.*` untuk persistence, validation, servlet, atau annotation), Maven. Build: `mvn -q verify`; test spesifik: `mvn -q test -Dtest=NamaTest`; run: `mvn spring-boot:run`. |
| Prioritas Keputusan | Urutan: (1) correctness dan security, (2) requirement dan scope, (3) arsitektur dan layering, (4) simplicity dan maintainability, (5) konsistensi dengan codebase. |
| Arsitektur | Package utama: `controller`, `service`, `repository`, `model`, `dto`, `exception`, `config`. Alur: `controller -> service -> repository -> model`. `controller` dan `service` boleh memakai `dto`. `controller` tidak mengakses `repository`. `service` tidak bergantung pada HTTP. `repository` tidak memanggil `service` atau `controller`. `model` tidak bergantung pada DTO atau layer aplikasi. |
| Tanggung Jawab Layer | Controller: HTTP, validasi input, panggil service, mapping DTO, status code. Service: business logic, validasi bisnis, orchestration, transaksi. Repository: akses data Spring Data/JPA. Model: entity dan enum. DTO: request/response; entity tidak diekspos langsung. Exception: exception bisnis dan `@RestControllerAdvice`. Config: konfigurasi teknis dan bean, bukan business logic. |
| Scope & Simplicity | Implementasikan hanya requirement yang diminta. Jangan menambah pattern, layer, helper, util, mapper, wrapper, config, fallback, atau extension point tanpa kebutuhan nyata. |
| Coding Standards | Constructor injection dengan field `final`. Jangan field `@Autowired`. Nama domain yang jelas, method satu tanggung jawab, `record` untuk DTO/value carrier, `Optional` hanya sebagai return value. |
| Documentation | Komentar menjelaskan alasan. Javadoc hanya untuk API publik baru yang tidak self-explanatory. |
| Error, Logging & Security | Error HTTP melalui `@RestControllerAdvice`. Logging memakai SLF4J. Jangan hardcode atau log secret. Gunakan parameterized query. |
| Testing | Perilaku baru dan bug fix wajib punya test. Pola AAA, nama `namaMethod_kondisi_hasilYangDiharapkan`, assertion AssertJ. Service: unit test tanpa Spring context. Controller: `@WebMvcTest`. Repository: `@DataJpaTest`. `@SpringBootTest` hanya jika unit/slice test tidak cukup. |
| Anti-Halusinasi | Gunakan hanya dependency di `pom.xml`. Jangan mengarang API, class, method, endpoint, tabel, atau kemampuan framework. |
| Workflow & DoD | Pahami scope, cari pola serupa, perubahan terkecil, update test, `mvn -q verify` (kecuali perubahan dokumentasi), hapus perubahan di luar scope. |
| Boundaries | Jangan ubah migrasi lama, file generated, `.env`, `target/`, dependency, versi framework, struktur package/module, atau kontrak API tanpa persetujuan. Jangan commit secret. |
| Output | Laporan akhir memuat file yang diubah, ringkasan, alasan, hasil validasi, dan risiko atau hal yang sengaja tidak diubah. Plan memakai format Goal, Steps, Files to Modify. |

Rincian lengkap ada di [`AGENTS.md`](./AGENTS.md), yang menjadi sumber kebenaran.

## Struktur Repositori

Package layer di bawah `com.sisco.springbootstarter` sudah dibuat dan masih kosong. `static/` dan `templates/` juga kosong. Package `client` tidak ada.

```text
.
├── AGENTS.md
├── README.md
├── pom.xml
├── mvnw
├── mvnw.cmd
└── src/
    ├── main/
    │   ├── java/com/sisco/springbootstarter/
    │   │   ├── MainApplication.java
    │   │   ├── ServletInitializer.java
    │   │   ├── config/
    │   │   ├── controller/
    │   │   ├── dto/
    │   │   ├── exception/
    │   │   ├── model/
    │   │   ├── repository/
    │   │   └── service/
    │   └── resources/
    │       ├── application.properties
    │       ├── static/
    │       └── templates/
    └── test/java/com/sisco/springbootstarter/
        └── MainApplicationTests.java
```
