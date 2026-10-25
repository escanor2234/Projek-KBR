# Rencana Proyek Expert System Evaluasi Kurikulum Akademik Berbasis Kompetensi

> **Status dokumen:** Rencana awal yang dapat diperbarui\
> **Fokus:** Menilai kesesuaian kurikulum akademik terhadap kebutuhan
> kompetensi berbagai pekerjaan di bidang IT.

## 1. Ringkasan Proyek

Proyek ini bertujuan membangun **expert system untuk mengevaluasi
keselarasan kurikulum akademik dengan kompetensi yang dibutuhkan dunia
kerja**, dengan fokus awal pada seluruh *occupation* di bidang
Information Technology (IT).

Sistem menghubungkan: 1. Kebutuhan kompetensi dari suatu pekerjaan. 2.
Bobot dan tingkat kompetensi yang dibutuhkan pekerjaan tersebut. 3.
Bukti pembelajaran dalam kurikulum, seperti mata kuliah, SKS, CPL, CPMK,
RPS, dan materi. 4. Aturan inferensi untuk menilai cakupan kompetensi.
5. Perhitungan *weighted coverage rate* untuk menghasilkan nilai
keselarasan kurikulum.

Sistem ini **mengevaluasi kurikulum**, bukan menilai kesiapan atau
kemampuan individu mahasiswa.

### Tujuan utama

-   Memetakan kompetensi yang dibutuhkan oleh pekerjaan IT.
-   Menilai kompetensi mana yang sudah, sebagian, atau belum tercakup
    kurikulum.
-   Menghasilkan skor keselarasan berbobot.
-   Menampilkan kesenjangan kompetensi (*competency gap*).
-   Memberikan rekomendasi perbaikan kurikulum berdasarkan bukti dan
    aturan yang dapat dijelaskan.

## 2. Ruang Lingkup

### Cakupan awal

-   Pekerjaan (*occupations*) di bidang IT.
-   Kompetensi, keterampilan (*skills*), dan pengetahuan (*knowledge*)
    yang dibutuhkan tiap pekerjaan.
-   Evaluasi satu kurikulum atau program studi terhadap satu atau
    beberapa pekerjaan target.
-   Analisis cakupan dan kesenjangan kompetensi.
-   Penjelasan hasil berdasarkan data sumber, bukti pembelajaran, dan
    aturan inferensi.

### Di luar cakupan awal

-   Menilai kemampuan aktual individu mahasiswa.
-   Menjamin bahwa lulusan pasti diterima bekerja.
-   Menggantikan keputusan akademik dosen atau pengelola program studi.
-   Menganggap kecocokan judul mata kuliah sebagai bukti bahwa
    kompetensi telah dikuasai.

## 3. Sumber Pengetahuan

### 3.1 ESCO

ESCO (*European Skills, Competences, Qualifications and Occupations*)
digunakan untuk: - Mengidentifikasi pekerjaan dan konsep
keterampilan/pengetahuan. - Mengambil relasi antara pekerjaan dengan
keterampilan. - Memanfaatkan hierarki, URI, dan relasi konsep untuk
membangun knowledge graph. - Menjadi sumber klasifikasi dan pengayaan
hubungan antarkonsep.

Sumber resmi: - Klasifikasi:
https://esco.ec.europa.eu/en/classification - Unduhan:
https://esco.ec.europa.eu/en/use-esco/download - API:
https://esco.ec.europa.eu/en/use-esco/use-esco-services-api/esco-web-service-api

**Pilihan unduhan yang disarankan untuk tahap awal:** - Dataset: ESCO
dataset v1.2.1 (atau versi resmi terbaru yang dipilih dan dicatat). -
Content: Classification. - File type: CSV. - Language: English, jika
tersedia dan sesuai kebutuhan.

Prioritaskan file yang memuat occupations, skills/knowledge, serta
relasi occupation--skill. Nama file persis dapat berbeda menurut
paket/versi yang diunduh; periksa isi paket sebelum membuat pipeline.

Bagian *ESCO datamodel* bersifat tambahan untuk tahap awal. Format RDF
dapat dipertimbangkan jika sistem memerlukan representasi
semantik/ontologi langsung.

### 3.2 O\*NET

O\*NET digunakan terutama untuk informasi pekerjaan dan ukuran kebutuhan
kompetensi, termasuk: - **Importance**: seberapa penting suatu
skill/knowledge untuk pekerjaan. - **Level**: tingkat atau kedalaman
yang dibutuhkan pada kompetensi tertentu, jika tersedia untuk elemen
terkait.

Sumber resmi: - Database: https://www.onetcenter.org/database.html -
Portal pekerjaan: https://www.onetonline.org/ - Crosswalk:
https://www.onetcenter.org/crosswalks.html

Importance dan Level **tidak memiliki arti yang sama**. Importance dapat
digunakan sebagai bobot utama, sedangkan Level dapat membantu menentukan
standar kedalaman kompetensi yang perlu dicapai.

### 3.3 Crosswalk ESCO--O\*NET

Crosswalk membantu menghubungkan pekerjaan pada dua klasifikasi.
Crosswalk occupation **tidak otomatis memetakan semua skill secara
satu-ke-satu**.

Strategi: 1. Gunakan crosswalk jika tersedia dan dapat diunduh. 2.
Simpan ID/URI asli ESCO dan kode O\*NET. 3. Verifikasi hasil pemetaan
pekerjaan. 4. Lakukan penyelarasan kompetensi secara terpisah. 5. Jangan
menggabungkan dua konsep hanya karena labelnya mirip.

Jika crosswalk tidak dapat diakses, pengembangan dapat dimulai dari ESCO
dan O\*NET secara terpisah. Crosswalk dapat ditambahkan kemudian sebagai
lapisan integrasi.

### 3.4 Data kurikulum

Data masukan ideal: - Daftar mata kuliah dan SKS. - CPL (Capaian
Pembelajaran Lulusan). - CPMK (Capaian Pembelajaran Mata Kuliah). -
RPS. - Materi atau topik pembelajaran. - Bentuk pembelajaran/asesmen,
bila tersedia.

Jika data hanya berisi nama mata kuliah dan SKS, hasilnya harus
diperlakukan sebagai **estimasi awal berkeyakinan rendah**. CPL, CPMK,
RPS, dan bukti asesmen memberikan dasar penilaian yang lebih kuat.

## 4. Konsep dan Model Pengetahuan

Gunakan lapisan konsep bersama (*canonical competency layer*) untuk
menghubungkan istilah dari ESCO, O\*NET, dan dokumen kurikulum tanpa
menghapus asal-usul data.

### Entitas utama

  -----------------------------------------------------------------------
  Entitas                             Fungsi
  ----------------------------------- -----------------------------------
  `Occupation`                        Pekerjaan IT yang dievaluasi

  `Competency`                        Konsep kompetensi umum yang telah
                                      diselaraskan

  `Skill`                             Keterampilan yang diperlukan

  `Knowledge`                         Pengetahuan yang diperlukan

  `CompetencyRequirement`             Kebutuhan kompetensi khusus untuk
                                      suatu pekerjaan, termasuk bobot dan
                                      level

  `Curriculum`                        Kurikulum atau program studi yang
                                      dievaluasi

  `Course`                            Mata kuliah beserta SKS

  `LearningOutcome`                   CPL/CPMK yang menjadi target
                                      pembelajaran

  `LearningEvidence`                  Bukti dari RPS, materi, tugas,
                                      praktikum, atau asesmen

  `CoverageAssessment`                Penilaian cakupan kompetensi
                                      beserta alasan dan sumber bukti

  `EvaluationResult`                  Skor, gap, dan rekomendasi akhir
  -----------------------------------------------------------------------

### Relasi yang disarankan

-   `Occupation` ---requires→ `CompetencyRequirement`
-   `CompetencyRequirement` ---refers_to→ `Competency`
-   `Competency` ---has_subskill/related_to→ `Competency`
-   `Course` ---has_outcome→ `LearningOutcome`
-   `LearningOutcome` ---supports→ `Competency`
-   `LearningEvidence` ---supports→ `CoverageAssessment`
-   `CoverageAssessment` ---assesses→ `CompetencyRequirement`
-   `EvaluationResult` ---evaluates→ `Curriculum` terhadap `Occupation`

Relasi perlu memiliki sumber, versi, dan status validasi. Jika
memungkinkan, simpan jenis relasi seperti `exact_match`, `broader_than`,
`narrower_than`, atau `related_to`.

### Prinsip integrasi data

-   Pertahankan URI ESCO dan kode O\*NET sebagai ID sumber.
-   Simpan label asli dan label hasil normalisasi.
-   Simpan provenance: sumber, versi dataset, tanggal ekstraksi, dan
    metode pemetaan.
-   Hindari *double counting* ketika dua sumber merepresentasikan
    kompetensi yang sama.
-   Bedakan kompetensi yang setara dari kompetensi yang hanya
    berhubungan.

## 5. Strategi Pembobotan

Pembobotan dilakukan pada **dua sisi yang berbeda**:

1.  **Bobot kebutuhan pekerjaan**: seberapa penting kompetensi bagi
    pekerjaan target.
2.  **Bukti cakupan kurikulum**: seberapa jauh kompetensi diajarkan dan
    didukung oleh bukti pembelajaran.

Bobot pekerjaan dan SKS adalah besaran yang berbeda. Jangan langsung
mengalikan Importance O\*NET dengan jumlah SKS sebagai satu ukuran tanpa
normalisasi dan definisi yang jelas.

### 5.1 Bobot kebutuhan kompetensi

Misalkan (I_i) adalah nilai Importance kompetensi ke-(i). Bobot
ternormalisasi:

\[ w_i = `\frac{I_i}{\sum_{j=1}^{n} I_j}`{=tex} \]

Dengan demikian, total bobot semua kompetensi dalam satu pekerjaan
adalah 1.

Jika Importance tidak tersedia, jangan otomatis menetapkan bobot 0.
Tandai sebagai data hilang dan tentukan kebijakan yang eksplisit,
misalnya melengkapi dari sumber lain atau meminta penilaian pakar. Catat
metode tersebut.

### 5.2 Menilai cakupan kurikulum

Untuk setiap kompetensi, tentukan nilai cakupan (C_i) dalam rentang
0--1. Rubrik awal yang dapat diuji:

  -----------------------------------------------------------------------
                                     Nilai Interpretasi awal
  ---------------------------------------- ------------------------------
                                      0.00 Tidak ditemukan bukti
                                           pembelajaran yang relevan

                                      0.25 Pengenalan sangat terbatas

                                      0.50 Dibahas sebagian, tetapi
                                           kedalaman/latihan belum
                                           memadai

                                      0.75 Cakupan cukup kuat, tetapi
                                           masih ada aspek penting yang
                                           kurang

                                      1.00 Cakupan dan kedalaman memenuhi
                                           kriteria yang ditetapkan
  -----------------------------------------------------------------------

Rubrik ini adalah **usulan desain**, bukan standar resmi ESCO atau
O\*NET. Definisi operasionalnya perlu disesuaikan dengan jenis
kompetensi dan divalidasi oleh dosen/pakar.

### 5.3 Peran SKS

SKS adalah indikator intensitas pembelajaran, bukan bukti kompetensi
secara langsung. SKS dapat menjadi salah satu faktor dalam menilai
cakupan, bersama: - Kesesuaian CPL/CPMK. - Proporsi materi yang
relevan. - Kedalaman materi. - Praktikum, proyek, dan tugas. - Bentuk
asesmen yang menguji kompetensi.

Contoh ilustratif: mata kuliah 3 SKS dengan perkiraan 50% konten SQL
memiliki sekitar 1,5 SKS ekuivalen yang relevan dengan SQL. Mata kuliah
lain 3 SKS dengan 20% konten SQL memiliki sekitar 0,6 SKS ekuivalen.
Total 2,1 SKS ekuivalen **tidak otomatis berarti cakupan SQL 100%**;
kedalaman, hasil pembelajaran, dan asesmennya tetap harus dinilai.

Hindari menghitung materi yang sama berulang kali pada beberapa mata
kuliah tanpa aturan deduplikasi.

## 6. Rumus Weighted Coverage Rate

Jika (w_i) adalah bobot kebutuhan pekerjaan dan (C_i) adalah skor
cakupan kurikulum:

\[ WCR =
`\frac{\sum_{i=1}^{n} w_i C_i}{\sum_{i=1}^{n} w_i}`{=tex}`\times 100`{=tex}%
\]

Jika bobot telah dinormalisasi sehingga (`\sum `{=tex}w_i=1), rumus
menjadi:

\[ WCR = `\left`{=tex}(`\sum`{=tex}\_{i=1}\^{n} w_i
C_i`\right`{=tex})`\times 100`{=tex}% \]

Interpretasi: WCR menunjukkan seberapa besar kebutuhan kompetensi
berbobot dari pekerjaan target yang telah dicakup kurikulum menurut
rubrik sistem. WCR **bukan probabilitas diterima kerja dan bukan bukti
bahwa lulusan pasti kompeten**.

### Skor pendamping

Selain WCR, laporkan: - **Coverage count/rate**: proporsi jumlah
kompetensi yang tercakup, tanpa bobot. - **Critical gaps**: kompetensi
penting yang belum atau baru sebagian tercakup. - **Evidence
completeness**: proporsi kompetensi yang memiliki bukti kurikulum
memadai. - **Confidence**: tingkat keyakinan penilaian berdasarkan
kualitas data dan validasi.

Hindari menetapkan label seperti "baik", "cukup", atau "buruk" sebelum
ambang batasnya divalidasi dengan pakar dan diuji pada kurikulum nyata.

## 7. Aturan Inferensi

Sistem menggabungkan aturan berbasis pengetahuan dengan pembobotan
numerik.

Contoh aturan awal:

-   **R1 --- Tidak tercakup:** Jika tidak ada CPL/CPMK, materi, atau
    bukti pembelajaran relevan yang ditemukan, tetapkan status
    `NOT_COVERED` dan (C_i=0).
-   **R2 --- Tercakup sebagian:** Jika bukti relevan ada tetapi hanya
    mencakup sebagian aspek atau kedalamannya belum memenuhi kriteria,
    tetapkan `PARTIAL` dan berikan skor sesuai rubrik.
-   **R3 --- Tercakup:** Jika CPL/CPMK, materi, dan asesmen memberikan
    bukti yang memenuhi kriteria kompetensi, tetapkan `COVERED` dengan
    skor sesuai rubrik.
-   **R4 --- Bukti tidak cukup:** Jika informasi kurikulum terlalu minim
    atau ambigu, tetapkan `INSUFFICIENT_EVIDENCE`; jangan otomatis
    menyimpulkan bahwa kompetensi tidak diajarkan.
-   **R5 --- Konflik bukti:** Jika dokumen saling bertentangan, tandai
    `NEEDS_REVIEW` untuk pemeriksaan ahli.
-   **R6 --- Validasi pemetaan:** Jika pemetaan occupation atau
    kompetensi belum tervalidasi, beri status `UNVERIFIED` dan jangan
    memperlakukannya sebagai fakta pasti.

Setiap hasil inferensi sebaiknya menyimpan: aturan yang dipicu, data
masukan, sumber bukti, skor, alasan, dan status validasi.

## 8. Alur Sistem

1.  **Ingest data pekerjaan:** impor dataset ESCO dan O\*NET.
2.  **Normalisasi:** bersihkan label, ID, bahasa, duplikasi, dan versi.
3.  **Pemetaan pekerjaan:** hubungkan occupation ESCO dan O\*NET bila
    memungkinkan.
4.  **Penyelarasan kompetensi:** bangun lapisan kompetensi bersama
    dengan provenance.
5.  **Input kurikulum:** impor mata kuliah, SKS, CPL, CPMK, RPS, materi,
    dan asesmen.
6.  **Ekstraksi dan pemetaan:** hubungkan hasil pembelajaran dengan
    kompetensi yang relevan.
7.  **Validasi ahli:** tinjau kandidat pemetaan dan bukti yang ambigu.
8.  **Inferensi:** tentukan status cakupan per kompetensi.
9.  **Perhitungan:** hitung WCR dan metrik pendamping.
10. **Gap analysis:** urutkan kekurangan berdasarkan bobot, skor
    cakupan, dan tingkat kepentingan.
11. **Pelaporan:** tampilkan hasil, bukti, sumber, ketidakpastian, dan
    rekomendasi.

## 9. Pemanfaatan NLP dan Embedding

NLP/embedding dapat membantu: - Mencocokkan istilah kompetensi dengan
CPL/CPMK dan materi. - Menemukan kandidat relasi dari dokumen teks. -
Mengelompokkan istilah yang mirip dan mendeteksi kemungkinan duplikasi.

Namun, kemiripan semantik **tidak cukup untuk menetapkan kompetensi
telah tercakup**. Contohnya, penyebutan "database" dalam deskripsi mata
kuliah tidak membuktikan bahwa mahasiswa mempelajari optimasi query atau
desain basis data.

Strategi yang disarankan: 1. Gunakan pencocokan kata kunci dan embedding
untuk menghasilkan kandidat. 2. Gunakan aturan dan konteks untuk
menyaring kandidat. 3. Minta validasi ahli pada pemetaan berisiko atau
berkeyakinan rendah. 4. Simpan keputusan validasi sebagai data yang
dapat diaudit.

## 10. Arsitektur Teknologi Awal

Arsitektur yang disarankan dan masih dapat diubah:

  -----------------------------------------------------------------------
  Komponen                Opsi awal               Peran
  ----------------------- ----------------------- -----------------------
  Pengolahan data         Python, pandas          Impor, pembersihan,
                                                  normalisasi,
                                                  perhitungan

  Penyimpanan knowledge   Neo4j                   Menyimpan occupation,
  graph                                           competency, course,
                                                  evidence, dan relasi

  API/backend             FastAPI                 Menyediakan endpoint
                                                  evaluasi dan
                                                  pengelolaan data

  Antarmuka               React                   Input kurikulum,
                                                  memilih occupation,
                                                  melihat hasil

  Penyimpanan             CSV saat prototipe;     Menyimpan data mentah,
  dokumen/data            database sesuai         dokumen, dan hasil
                          kebutuhan               evaluasi

  NLP/embedding           Model embedding yang    Mengusulkan pemetaan
                          sesuai                  teks

  Pengujian               Unit test, integration  Memastikan aturan dan
                          test, validasi pakar    skor dapat dipercaya
  -----------------------------------------------------------------------

Neo4j merupakan pilihan praktis untuk prototipe knowledge graph, bukan
satu-satunya pilihan. RDF/OWL, SHACL, dan SPARQL dapat dipertimbangkan
jika dibutuhkan ontologi formal dan validasi semantik. Pilih satu
pendekatan utama pada prototipe agar lingkup tidak melebar.

## 11. Modul Aplikasi yang Direncanakan

1.  **Data occupation:** cari dan lihat pekerjaan target.
2.  **Competency profile:** daftar kompetensi, bobot, level, dan sumber.
3.  **Curriculum management:** unggah/kelola mata kuliah dan dokumen
    pendukung.
4.  **Competency mapping:** tinjau hubungan kompetensi dengan
    CPL/CPMK/materi.
5.  **Inference engine:** terapkan aturan cakupan.
6.  **Coverage calculator:** hitung WCR dan metrik pendamping.
7.  **Gap analysis:** tampilkan kompetensi yang paling membutuhkan
    perbaikan.
8.  **Recommendation module:** berikan saran berbasis gap yang
    ditemukan.
9.  **Audit/provenance:** tampilkan alasan, sumber, versi data, dan
    validasi.
10. **Dashboard/report:** hasil per occupation, per kompetensi, dan
    lintas occupation.

## 12. Bentuk Keluaran

Untuk setiap occupation yang dievaluasi, laporan sebaiknya memuat: -
Nama dan ID pekerjaan serta sumbernya. - Daftar kompetensi dan bobot. -
Status cakupan dan skor per kompetensi. - Bukti kurikulum yang mendasari
skor. - WCR dan coverage rate tanpa bobot. - Daftar critical gaps. -
Rekomendasi perbaikan yang terhubung dengan gap. - Kompetensi yang belum
dapat dinilai karena data tidak memadai. - Sumber, versi dataset, dan
catatan validasi.

## 13. Strategi Validasi dan Evaluasi

Validasi sistem perlu dilakukan pada dua level.

### Validasi pengetahuan dan pemetaan

-   Pakar/dosen menilai apakah occupation--competency mapping masuk
    akal.
-   Pakar meninjau kecocokan CPL/CPMK/materi terhadap kompetensi.
-   Ukur kesepakatan antarpenilai bila beberapa pakar terlibat.
-   Catat kasus ambigu dan revisi rubrik berdasarkan hasil evaluasi.

### Validasi hasil numerik

-   Uji perhitungan WCR dengan contoh yang dihitung manual.
-   Pastikan bobot dinormalisasi per occupation.
-   Uji data kosong, kompetensi duplikat, dan bobot yang hilang.
-   Pastikan kompetensi tanpa bukti tidak disamakan dengan kompetensi
    yang belum dapat dinilai.
-   Lakukan analisis sensitivitas terhadap bobot dan rubrik cakupan.
-   Bandingkan hasil sistem dengan evaluasi ahli, lalu dokumentasikan
    perbedaan.

## 14. Risiko dan Mitigasi

  -----------------------------------------------------------------------
  Risiko                              Mitigasi
  ----------------------------------- -----------------------------------
  Istilah ESCO dan O\*NET tidak       Gunakan lapisan kompetensi bersama
  sepenuhnya sama                     dan tipe relasi pemetaan

  Crosswalk pekerjaan tidak memetakan Lakukan penyelarasan kompetensi
  semua skill                         terpisah dan validasi ahli

  SKS dianggap bukti kompetensi       Gunakan SKS hanya sebagai salah
                                      satu indikator intensitas

  Nama mata kuliah terlalu umum       Evaluasi CPL, CPMK, RPS, materi,
                                      proyek, dan asesmen

  Kompetensi terhitung dua kali       Deduplikasi berdasarkan identitas
                                      konsep dan aturan agregasi

  Bobot tidak tersedia                Tandai missing; dokumentasikan
                                      kebijakan penanganannya

  NLP salah mencocokkan istilah       Gunakan sebagai kandidat, bukan
                                      keputusan final

  Skor dipahami sebagai jaminan       Jelaskan batas interpretasi WCR dan
  kesiapan kerja                      tampilkan evidence/confidence

  Data berubah antarversi             Catat versi, tanggal unduh, dan
                                      provenance

  Rekomendasi terlalu umum            Kaitkan rekomendasi langsung ke gap
                                      dan bukti yang ditemukan
  -----------------------------------------------------------------------

## 15. Tahapan Pengembangan

### Fase 1 --- Eksplorasi data

-   Unduh ESCO Classification dalam CSV.
-   Ambil data O\*NET yang relevan.
-   Periksa skema, ID, relasi, lisensi, dan ketersediaan nilai
    Importance/Level.
-   Pilih beberapa occupation IT untuk prototipe.

**Output:** data dictionary dan dataset awal yang terdokumentasi.

### Fase 2 --- Model pengetahuan

-   Definisikan entitas, relasi, dan canonical competency layer.
-   Rancang aturan penyelarasan occupation/competency.
-   Tentukan provenance dan kebijakan duplikasi.

**Output:** skema knowledge graph dan contoh graph.

### Fase 3 --- Data kurikulum dan rubrik

-   Tentukan format input kurikulum.
-   Susun rubrik coverage dan aturan inferensi.
-   Validasi rubrik dengan dosen/pakar.

**Output:** template kurikulum, rubrik, dan aturan awal.

### Fase 4 --- Prototipe evaluasi

-   Implementasikan pemetaan kompetensi.
-   Implementasikan rule engine dan WCR.
-   Uji dengan data kecil dan perhitungan manual.

**Output:** pipeline evaluasi yang dapat direproduksi.

### Fase 5 --- Aplikasi dan visualisasi

-   Buat API, antarmuka, dashboard, dan laporan.
-   Tambahkan pencarian occupation serta drill-down bukti kompetensi.

**Output:** aplikasi prototipe yang dapat digunakan.

### Fase 6 --- Validasi dan perluasan

-   Bandingkan hasil dengan pakar.
-   Perbaiki aturan, rubrik, dan mapping.
-   Perluas ke lebih banyak occupation IT setelah kualitas prototipe
    memadai.

**Output:** evaluasi validitas, dokumentasi keterbatasan, dan rencana
pengembangan.

## 16. Keputusan yang Masih Perlu Ditetapkan

Sebelum implementasi penuh, tetapkan: 1. **Sumber utama occupation:**
ESCO, O*NET, atau kombinasi dengan aturan integrasi yang jelas. 2.
**Definisi "occupation IT":** gunakan kategori resmi yang tersedia dan
dokumentasikan kriteria inklusinya. 3. **Kelengkapan input kurikulum:**
apakah tersedia hanya nama mata kuliah/SKS atau juga CPL, CPMK, RPS,
materi, dan asesmen. 4. **Bobot:** apakah Importance O*NET menjadi bobot
utama dan bagaimana menangani kompetensi tanpa nilai. 5. **Rubrik
coverage:** definisi operasional setiap nilai 0--1 dan siapa yang
memvalidasi. 6. **Agregasi SKS:** bagaimana menghitung proporsi materi
relevan dan mencegah penghitungan ganda. 7. **Teknologi graph:** Neo4j
atau pendekatan RDF/OWL. 8. **Ambang interpretasi WCR:** ditetapkan
hanya setelah validasi, bukan secara arbitrer. 9. **Bahasa data:**
bahasa Inggris untuk sumber internasional dan strategi pemetaan dokumen
kurikulum berbahasa Indonesia. 10. **Lisensi dan atribusi:** periksa
ketentuan penggunaan serta distribusi dataset dari setiap sumber.

## 17. Prinsip Desain yang Harus Dipertahankan

-   **Explainability:** setiap skor harus memiliki alasan dan bukti.
-   **Traceability:** setiap konsep dan relasi dapat ditelusuri ke
    sumber.
-   **Separation of concerns:** bobot kebutuhan pekerjaan berbeda dari
    intensitas pembelajaran.
-   **Human-in-the-loop:** pakar memvalidasi pemetaan dan kasus ambigu.
-   **No false certainty:** data yang tidak tersedia harus dibedakan
    dari kompetensi yang tidak tercakup.
-   **Reproducibility:** versi dataset, parameter, aturan, dan hasil
    dicatat.
-   **No double counting:** konsep atau bukti yang sama tidak boleh
    meningkatkan skor secara tidak wajar.
-   **Iterative scope:** mulai dari prototipe beberapa occupation IT,
    kemudian perluas ke seluruh cakupan IT setelah pipeline tervalidasi.

## 18. Referensi Resmi

-   ESCO Classification: https://esco.ec.europa.eu/en/classification
-   ESCO Downloads: https://esco.ec.europa.eu/en/use-esco/download
-   ESCO API:
    https://esco.ec.europa.eu/en/use-esco/use-esco-services-api/esco-web-service-api
-   O\*NET Database: https://www.onetcenter.org/database.html
-   O\*NET Online: https://www.onetonline.org/
-   O\*NET Crosswalks: https://www.onetcenter.org/crosswalks.html

------------------------------------------------------------------------

**Ringkasan satu kalimat:** sistem ini menggabungkan data occupation dan
kebutuhan kompetensi dari ESCO/O\*NET, bukti pembelajaran dari
kurikulum, aturan inferensi yang dapat dijelaskan, dan weighted coverage
rate untuk mengevaluasi keselarasan kurikulum akademik dengan kebutuhan
pekerjaan IT.
