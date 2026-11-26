# Modul Panduan Kerja Kelompok — Praktikum 7 Rekayasa API

**Repository:** `https://github.com/mohakbarnusantara/api-wisata-sulteng-4`  
**Status File Dasar:** `openapi.yaml` (bagian C.1–C.4) sudah valid dan siap dilanjutkan ke 4 Eksperimen.

---

## Pembagian Tugas 5 Anggota Kelompok

| Urutan | Nama Lengkap | Stambuk | Tugas Utama |
| :---: | :--- | :--- | :--- |
| **Tahap 0 & 5** | **Moh Akbar Nusantara** | `F5512520037` | Siapkan branch Git, gabungkan (*merge*) hasil akhir, buat tag `v2.2.0`, Tabel Kesesuaian, Analisis & Lampiran |
| **Tahap 1** | **Humaira Islami Risya Adam** | `F5512510006` | **Eksperimen 1:** Lengkapi dokumentasi `v2` (`PUT /v2/destinations/{id}`, `/v2/regions`, `/v2/regions/{id}`) & schema `Region` |
| **Tahap 2** | **Bagus Aldy Putra** | `F5512510009` | **Eksperimen 2:** Lengkapi dokumentasi `v1` (`/v1/destinations` & `/v1/destinations/{id}`) bertanda `deprecated` + schema `v1` |
| **Tahap 3** | **Andhin Anggraini Bantulu** | `F5512510010` | **Eksperimen 3:** Tambah endpoint publik `GET /openapi.yaml` di `server.js` (tanpa API key) |
| **Tahap 4** | **Cut Safira** | `F5512510022` | **Eksperimen 4:** Tambah 5 Test Case baru (`TC9`–`TC13`) di Postman & jalankan *Collection Runner* |

> **Aturan Kerja Tim (Wajib Baca Sebelum Mulai):**
> 1. Kerjakan **berurutan dari Tahap 0 → 1 → 2 → 3 → 4 → 5** agar tidak bentrok (*conflict*) di Git.
> 2. Sebelum mulai mengedit, selalu jalankan `git pull`. Setelah selesai, wajib `git commit` dan `git push`.
> 3. Di file `openapi.yaml`, **DILARANG memakai tombol `Tab`**. Gunakan **spasi** dan pastikan rata kiri-kanan persis seperti contoh di bawah.

---

## Panduan Singkat Menggunakan Swagger Editor (Untuk Humaira & Bagus)

Setiap kali selesai mengedit `openapi.yaml`, cek hasilnya di Swagger Editor:
1. Buka browser dan kunjungi **`https://editor.swagger.io`**.
2. Buka file `openapi.yaml` di VS Code → tekan `Ctrl + A` (pilih semua) → `Ctrl + C` (salin).
3. Klik panel sebelah **kiri** Swagger Editor → tekan `Ctrl + A` → `Backspace` (hapus contoh bawaan) → `Ctrl + V` (tempel kode kalian).
4. Lihat panel sebelah **kanan**:
   * Jika muncul kotak merah bertuliskan **Errors**, periksa nomor barisnya (biasanya kelebihan atau kekurangan 1 spasi). Perbaiki di VS Code lalu tempel ulang.
   * Jika **tidak ada kotak merah**, klik endpoint yang baru kalian tambahkan untuk memastikan detailnya muncul, lalu **Screenshot** untuk laporan.

---

## TAHAP 0: Persiapan Branch (Akbar)

**Penanggung Jawab:** **Moh Akbar Nusantara (`F5512520037`)**

Pastikan `openapi.yaml` sudah masuk ke `main` terlebih dahulu agar tidak hilang saat berpindah branch. Jalankan perintah berikut di terminal VS Code:

```bash
# 1. Pastikan berada di main dan gabungkan branch dokumentasi
git switch main
git merge dokumentasi
git push origin main

# 2. Buat branch baru khusus untuk 4 eksperimen lalu kirim ke GitHub
git switch -c eksperimen-docs
git push -u origin eksperimen-docs
```

*(Pastikan file `openapi.yaml` terlihat di sidebar VS Code. Jika sudah, kabari **Humaira** untuk mulai Tahap 1).*

---

## TAHAP 1: Eksperimen 1 — Melengkapi Dokumentasi `v2` (Humaira)

**Penanggung Jawab:** **Humaira Islami Risya Adam (`F5512510006`)**

### Langkah 1: Ambil Kode Terbaru
Buka terminal VS Code di folder project, ketik:
```bash
git fetch origin
git switch eksperimen-docs
git pull
```

### Langkah 2: Edit File `openapi.yaml`
Buka `openapi.yaml` di VS Code, lakukan **3 penambahan** berikut:

**A. Cari baris `tags:` (sekitar baris 12), tambahkan `- name: Regions`:**
```yaml
tags:
  - name: Login
  - name: Destinations
  - name: Regions
```

**B. Cari bagian `/v2/destinations/{id}:` lalu gulir ke bawah sampai akhir blok `delete:` (sebelum `/v1/destinations:`). Tambahkan `put:` sejajar dengan `delete:`, lalu tambahkan `/v2/regions` dan `/v2/regions/{id}` sejajar dengan `/v2/destinations/{id}:`:**
```yaml
    put:
      tags: [Destinations]
      summary: Memperbarui data destinasi
      security:
        - ApiKeyAuth: []
          BearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/DestinationInput'
      responses:
        '200':
          description: Destinasi berhasil diperbarui
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Destination'
        '400':
          $ref: '#/components/responses/TidakValid'
        '401':
          $ref: '#/components/responses/TidakDikenali'
        '404':
          $ref: '#/components/responses/TidakDitemukan'

  /v2/regions:
    get:
      tags: [Regions]
      summary: Membaca daftar wilayah (region)
      responses:
        '200':
          description: Daftar wilayah
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/Region'
        '401':
          $ref: '#/components/responses/TidakDikenali'

  /v2/regions/{id}:
    parameters:
      - name: id
        in: path
        required: true
        description: Nomor id wilayah
        schema:
          type: integer
          example: 1
    get:
      tags: [Regions]
      summary: Membaca satu wilayah
      responses:
        '200':
          description: Data wilayah
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/Region'
        '401':
          $ref: '#/components/responses/TidakDikenali'
        '404':
          $ref: '#/components/responses/TidakDitemukan'
```

**C. Cari bagian `components:` → `schemas:`, lalu tambahkan schema `Region:` tepat di bawah `Error:` (sejajar 4 spasi dengan `Error:`):**
```yaml
    Region:
      type: object
      properties:
        id:
          type: integer
          example: 1
        name:
          type: string
          example: Kota Palu
        latitude:
          type: number
          example: -0.8917
        longitude:
          type: number
          example: 119.8707
```

### Langkah 3: Cek di Swagger Editor & Screenshot
1. Salin seluruh `openapi.yaml` ke `editor.swagger.io`.
2. Pastikan bebas error dan `PUT /v2/destinations/{id}`, `GET /v2/regions`, serta `GET /v2/regions/{id}` tampil rapi.
3. **Ambil Screenshot** tampilan panel kanan untuk laporan (Bagian 4.c).

### Langkah 4: Commit & Push ke GitHub
```bash
git add openapi.yaml
git commit -m "Eksperimen 1: Lengkapi dokumentasi v2 dan schema Region"
git push
```

*(Kabari **Bagus** bahwa Tahap 1 sudah selesai).*

---

## TAHAP 2: Eksperimen 2 — Dokumentasi Versi Lama `v1` (Bagus)

**Penanggung Jawab:** **Bagus Aldy Putra (`F5512510009`)**

> **Info dari `server.js`:** Pada versi `v1`, server melayani `GET` dan `POST` di `/v1/destinations`, serta `GET`, `PUT`, dan `DELETE` di `/v1/destinations/{id}`. Selain itu, data `v1` menggunakan field `region` (nama daerah berupa teks, misal `"Poso"`), bukan `regionId`.

### Langkah 1: Ambil Update dari Humaira
```bash
git fetch origin
git switch eksperimen-docs
git pull
```

### Langkah 2: Edit File `openapi.yaml`
Buka `openapi.yaml`, lakukan **2 perubahan** berikut:

**A. Cari bagian `/v1/destinations:` yang sudah ada, lalu GANTI seluruh blok `/v1/destinations:` tersebut dengan kode lengkap berikut (termasuk `/v1/destinations/{id}`):**
```yaml
  /v1/destinations:
    get:
      tags: [Destinations]
      summary: Membaca daftar destinasi (versi lama)
      description: Dilayani sampai 30 Juni 2027. Gunakan /v2/destinations.
      deprecated: true
      responses:
        '200':
          description: Daftar destinasi dengan field region berisi nama daerah
          content:
            application/json:
              schema:
                type: array
                items:
                  $ref: '#/components/schemas/DestinationV1'
        '401':
          $ref: '#/components/responses/TidakDikenali'
    post:
      tags: [Destinations]
      summary: Menambah destinasi baru (versi lama)
      description: Dilayani sampai 30 Juni 2027. Gunakan /v2/destinations.
      deprecated: true
      security:
        - ApiKeyAuth: []
          BearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/DestinationV1Input'
      responses:
        '201':
          description: Destinasi berhasil ditambahkan
          headers:
            Location:
              description: Alamat destinasi yang baru dibuat
              schema:
                type: string
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/DestinationV1'
        '400':
          $ref: '#/components/responses/TidakValid'
        '401':
          $ref: '#/components/responses/TidakDikenali'

  /v1/destinations/{id}:
    parameters:
      - name: id
        in: path
        required: true
        description: Nomor id destinasi
        schema:
          type: integer
          example: 1
    get:
      tags: [Destinations]
      summary: Membaca satu destinasi (versi lama)
      description: Dilayani sampai 30 Juni 2027. Gunakan /v2/destinations/{id}.
      deprecated: true
      responses:
        '200':
          description: Data destinasi versi lama
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/DestinationV1'
        '401':
          $ref: '#/components/responses/TidakDikenali'
        '404':
          $ref: '#/components/responses/TidakDitemukan'
    put:
      tags: [Destinations]
      summary: Memperbarui destinasi (versi lama)
      description: Dilayani sampai 30 Juni 2027. Gunakan /v2/destinations/{id}.
      deprecated: true
      security:
        - ApiKeyAuth: []
          BearerAuth: []
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/DestinationV1Input'
      responses:
        '200':
          description: Destinasi berhasil diperbarui
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/DestinationV1'
        '400':
          $ref: '#/components/responses/TidakValid'
        '401':
          $ref: '#/components/responses/TidakDikenali'
        '404':
          $ref: '#/components/responses/TidakDitemukan'
    delete:
      tags: [Destinations]
      summary: Menghapus destinasi (versi lama)
      description: Dilayani sampai 30 Juni 2027. Gunakan /v2/destinations/{id}.
      deprecated: true
      security:
        - ApiKeyAuth: []
          BearerAuth: []
      responses:
        '204':
          description: Destinasi berhasil dihapus, tanpa body
        '401':
          $ref: '#/components/responses/TidakDikenali'
        '403':
          $ref: '#/components/responses/TidakBerhak'
        '404':
          $ref: '#/components/responses/TidakDitemukan'
```

**B. Pada bagian `components:` → `schemas:`, tambahkan 2 schema baru (`DestinationV1` dan `DestinationV1Input`) tepat di bawah `Region:` (sejajar 4 spasi):**
```yaml
    DestinationV1:
      type: object
      properties:
        id:
          type: integer
          example: 1
        name:
          type: string
          example: Danau Poso
        region:
          type: string
          example: Poso
        category:
          type: string
          example: danau
    DestinationV1Input:
      type: object
      required: [name, category, region]
      properties:
        name:
          type: string
          example: Air Terjun Wera
        category:
          type: string
          example: alam
        region:
          type: string
          example: Sigi
```

### Langkah 3: Cek di Swagger Editor & Screenshot
1. Salin seluruh `openapi.yaml` ke `editor.swagger.io`.
2. Pastikan semua endpoint `/v1/...` tampil **pudar/tercoret** (*deprecated*) dan tidak ada error.
3. **Ambil Screenshot** tampilan panel kanan untuk laporan (Bagian 4.c).

### Langkah 4: Commit & Push ke GitHub
```bash
git add openapi.yaml
git commit -m "Eksperimen 2: Lengkapi dokumentasi v1 dengan status deprecated"
git push
```

*(Kabari **Andhin** bahwa Tahap 2 sudah selesai).*

---

## TAHAP 3: Eksperimen 3 — Endpoint Publik `GET /openapi.yaml` (Andhin)

**Penanggung Jawab:** **Andhin Anggraini Bantulu (`F5512510010`)**

### Langkah 1: Ambil Update dari Bagus
```bash
git fetch origin
git switch eksperimen-docs
git pull
```

### Langkah 2: Edit File `server.js`
Buka file `server.js`, lalu cari bagian fungsi `router` (sekitar baris 460):

```javascript
async function router (req, res) {
  const url = new URL(req.url, 'http://localhost');
  const segmen = url.pathname.split('/').filter(Boolean);
  const aplikasi = await periksaApiKey (req);
```

Sisipkan kode `GET /openapi.yaml` **TEPAT SEBELUM** baris `const aplikasi = await periksaApiKey (req);` (agar bisa diakses tanpa API key). Hasilnya menjadi seperti ini:

```javascript
async function router (req, res) {
  const url = new URL(req.url, 'http://localhost');
  const segmen = url.pathname.split('/').filter(Boolean);

  // Eksperimen 3: Endpoint publik dokumentasi OpenAPI (tanpa API key)
  if (req.method === 'GET' && url.pathname === '/openapi.yaml') {
    const isiYaml = await fs.readFile(path.join(__dirname, 'openapi.yaml'), 'utf8');
    res.writeHead(200, {
      'Content-Type': 'text/yaml; charset=utf-8',
      'Content-Length': Buffer.byteLength(isiYaml)
    });
    return res.end(isiYaml);
  }

  const aplikasi = await periksaApiKey (req);
```

### Langkah 3: Uji Coba & Salin Kode untuk Laporan
1. Jalankan server di terminal:
   ```bash
   node --watch --env-file=.env server.js
   ```
2. Buka browser dan kunjungi **`http://localhost:3000/openapi.yaml`**.
3. Pastikan isi file `openapi.yaml` langsung tampil di browser tanpa error 401.
4. **Salin potongan kode** `if (req.method === 'GET' && url.pathname === '/openapi.yaml') { ... }` sebagai teks untuk laporan (Bagian 4.c).

### Langkah 4: Commit & Push ke GitHub
```bash
git add server.js
git commit -m "Eksperimen 3: Tambah endpoint publik GET /openapi.yaml"
git push
```

*(Kabari **Cut Safira** bahwa Tahap 3 sudah selesai).*

---

## TAHAP 4: Eksperimen 4 — Tambah 5 Test Case Postman (Cut Safira)

**Penanggung Jawab:** **Cut Safira (`F5512510022`)**

### Langkah 1: Ambil Update & Jalankan Server
```bash
git fetch origin
git switch eksperimen-docs
git pull
node --watch --env-file=.env server.js
```
*(Biarkan terminal server tetap menyala selama pengujian di Postman).*

### Langkah 2: Buat 5 Request Baru di Folder `Praktikum 7`
Buka Postman → Collection **Praktikum Rekayasa API** → Folder **Praktikum 7** (yang sudah berisi `TC1`–`TC8`). Tambahkan 5 request berikut di urutan paling bawah:

#### 1. `TC9 Tambah data tidak valid`
* **Method & URL:** `POST {{local_url}}/v2/destinations`
* **Authorization:** Bearer Token → `{{token_editor}}`
* **Body (`raw` → `JSON`):**
  ```json
  { "name": "" }
  ```
* **Scripts (`Post-response`):**
  ```javascript
  pm.test('Status 400 karena body tidak valid', function () {
    pm.response.to.have.status(400);
  });
  pm.test('Body memuat field error', function () {
    pm.expect(pm.response.json()).to.have.property('error');
  });
  ```

#### 2. `TC10 Update destinasi berhasil`
* **Method & URL:** `PUT {{local_url}}/v2/destinations/1`
* **Authorization:** Bearer Token → `{{token_editor}}`
* **Body (`raw` → `JSON`):**
  ```json
  { "name": "Danau Poso", "category": "danau", "regionId": 2 }
  ```
* **Scripts (`Post-response`):**
  ```javascript
  pm.test('Status 200 berhasil memperbarui destinasi', function () {
    pm.response.to.have.status(200);
  });
  pm.test('Data yang diperbarui sesuai kiriman', function () {
    const data = pm.response.json();
    pm.expect(data.name).to.eql('Danau Poso');
    pm.expect(data.regionId).to.eql(2);
  });
  ```

#### 3. `TC11 Cek header Sunset v1`
* **Method & URL:** `GET {{local_url}}/v1/destinations`
* **Authorization:** No Auth *(API key sudah otomatis dikirim oleh script folder)*
* **Scripts (`Post-response`):**
  ```javascript
  pm.test('Status 200 dan memiliki header Sunset', function () {
    pm.response.to.have.status(200);
    pm.response.to.have.header('Sunset');
  });
  ```

#### 4. `TC12 Daftar wilayah v2`
* **Method & URL:** `GET {{local_url}}/v2/regions`
* **Authorization:** No Auth
* **Scripts (`Post-response`):**
  ```javascript
  pm.test('Status 200 dan body berupa array region', function () {
    pm.response.to.have.status(200);
    const data = pm.response.json();
    pm.expect(data).to.be.an('array');
    pm.expect(data[0]).to.include.all.keys('id', 'name');
  });
  ```

#### 5. `TC13 Ambil dokumentasi openapi.yaml`
* **Method & URL:** `GET {{local_url}}/openapi.yaml`
* **Authorization:** No Auth
* **Scripts (`Post-response`):**
  ```javascript
  pm.test('Status 200 dengan Content-Type text/yaml', function () {
    pm.response.to.have.status(200);
    pm.expect(pm.response.headers.get('Content-Type')).to.include('text/yaml');
    pm.expect(pm.response.text()).to.include('openapi: 3.0.3');
  });
  ```

### Langkah 3: Jalankan Collection Runner & Screenshot
1. Klik folder **Praktikum 7** → klik tombol **Run**.
2. Pastikan seluruh `TC1` sampai `TC13` tercentang berurutan, lalu klik **Run**.
3. Pastikan **0 Failed** (semua hijau/lolos). Klik **Run Again** sekali lagi untuk memastikan pengujian tetap lolos saat dijalankan berulang.
4. **Ambil Screenshot** hasil Collection Runner `TC1`–`TC13` untuk laporan (Bagian 4.c), dan salin seluruh test script `TC9`–`TC13` sebagai teks.

*(Kabari **Akbar** bahwa Tahap 4 sudah selesai).*

---

## TAHAP 5: Finalisasi Git, Tagging & Laporan (Akbar)

**Penanggung Jawab:** **Moh Akbar Nusantara (`F5512520037`)**

### Langkah 1: Merge ke `main`, Buat Tag `v2.2.0`, dan Push
```bash
git fetch origin
git switch eksperimen-docs
git pull

# Kembalikan file data jika sempat berubah saat pengujian Postman
git restore data/destinations.json

# Pindah ke main dan gabungkan seluruh eksperimen
git switch main
git merge eksperimen-docs

# Buat tag rilis v2.2.0 sesuai modul
git tag v2.2.0

# Kirim branch main dan tag ke GitHub
git push origin main --tags
```

### Langkah 2: Screenshot Riwayat Git
Jalankan perintah berikut lalu ambil screenshot terminalnya untuk lampiran laporan (Bagian 4.g):
```bash
git log --oneline --decorate -n 8
```

### Langkah 3: Bahan Tabel Kesesuaian (Bagian 4.d Laporan)
Untuk poin **dua perilaku server yang ditemukan tetapi belum tercatat di dokumentasi**, kamu bisa mencantumkan temuan nyata berikut langsung dari `server.js` kalian:
1. **ID bukan bilangan bulat positif** (misal `GET /v2/destinations/abc` atau `/v2/destinations/0`) → server menjawab **`400 Bad Request`** (`"id harus berupa bilangan bulat positif"`).
2. **Method HTTP tidak didukung** (misal `PATCH /v2/destinations/1`) → server menjawab **`405 Method Not Allowed`** beserta header `Allow: GET, PUT, DELETE`.
3. *(Bonus)* **Login gagal 3 kali berturut-turut** pada `POST /login` → server menjawab **`429 Too Many Requests`** (akun dikunci sementara 60 detik).
