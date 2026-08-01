# Tantangan-Lakehouse-Data
Dokumen ini berisi panduan langkah demi langkah untuk menyelesaikan tugas pembuatan tabel Lakehouse, penerapan tag data sensitif (aspect/tag), dan pembersihan izin IAM.

---

## Tugas 1. Membuat Tabel Lakehouse Menggunakan Koneksi Cloud Resource

### Langkah 1: Buat Dataset BigQuery
1. Buka **BigQuery Studio** di Google Cloud Console.
2. Di panel penjelajah (Explorer), klik ikon tiga titik di sebelah ID Project Anda, lalu pilih **Create dataset**.
3. Atur konfigurasi berikut:
   * **Dataset ID:** `online_shop`
   * **Data location:** Pilih `US (multiple rgion in United States)`.
4. Klik **Create dataset**.

Pastikan sudah mendapat izin : apply the appropriate service account permissions to read Cloud Storage files in your project.


### Langkah 2: Buat Koneksi Cloud Resource (Cloud Resource Connection)

Run query berikut:

    ```
    CREATE CONNECTION IF NOT EXISTS `us.user_data_connection`
    OPTIONS (
      connection_type = "CLOUD_RESOURCE"
    );
    ```
    
Catat!:
    Service account id seperti: `bqcx-983519862653-viev@gcp-sa-bigquery-condel.iam.gserviceaccount.com`
    

### Langkah 3: Berikan Izin IAM ke Service Account Koneksi
1. Buka menu **IAM & Admin** > **IAM**.
2. Klik **Grant Access**.
3. Di kolom **New principals**, tempel (*paste*) `Service Account ID` yang sudah Anda salin sebelumnya.
4. Di kolom **Select a role**, cari dan pilih **Storage Object Viewer**.
5. Klik **Save**.

### Langkah 4: Buat Tabel Lakehouse (BigLake)
1. Kembali ke **BigQuery Studio**.
2. Di bawah dataset `online_shop`, klik tiga titik dan pilih **Create table**.
3. Atur konfigurasi pembuatan tabel:
   * **Create table from:** `Google Cloud Storage`
   * **Select file from GCS bucket:** pilih/browse file csv:
     ```text
     user-online-sessions.csv
     ```
   * **File format:** `CSV`
   * **Table type:** Pilih **External table**.
   * **Checklist/Centang:** `Create a BigLake table using a Cloud Resource connection`.
   * **Select connection:** Pilih koneksi `user_data_connection` yang sudah dibuat.
   * **Table name:** `user_online_sessions`
   * **Schema:** Centang **Auto detect**.
4. Klik **Create table**.

---

## Tugas 2. Membuat, Menerapkan, dan Memverifikasi Aspect pada Kolom Sensitif

### Langkah 1: Buat Tag Template (Sensitive Data Aspect)
1. //Buka menu **Dataplex** > **Manage** > **Tag Templates** (atau cari "Dataplex Tag Templates" di kolom pencarian).
2. //Klik **Create Tag Template**.
---
3.  **Knowledge Catalog/Aspect types** >> menu **Metadata types**
4. Pada Metadata types, kolom **Aspect types** klik **+Create**
5. Atur **Details** konfigurasi template:
   * **Display name:** `Sensitive Data Aspect`
   * **Aspect type ID:** `sensitive_data_aspect` (terisi otomatis)
   * **Location:** Pilih `us (multiple regions in united states)`.
5. Tambahkan *field* baru dengan klik **Add field**:
   * Field **Display name:** `Has Sensitive Data`
   * **Name:** `has_sensitive_data` (terisi otomatis)
   * **Type:** Pilih `Boolean`.
   * **Is Required** dicentang
6. Klik **Create**.

### Langkah 2: Terapkan Tag ke Kolom yang Ditentukan
1. Kembali ke **Knowledge Catalog** cari melalui pencarian utama Google Cloud paling atas tengah.
2. Setelah itu pada Search bar cari dengan mengetik: `user_online_sessions`.
3. Pilih/klik `user_online_sessions`, kemudian buka tab **Schema**.
4. Cari kolom pertama yang diminta: `zip`, kemudian centang.
5. Klik tombol **+ Add aspect** di atas.
6. Pilih template **Sensitive Data Aspect** yang baru saja Anda buat.
7. Di bagian `Add "Sensitive Data Aspect" `, pilih nilai **True**.
8. Klik **Save**.
9. Ulangi proses penambahan tag (Langkah 4-8) di atas untuk tiga kolom lainnya:
   * `latitude`
   * `ip_address`
   * `longitude`

---

## Tugas 3. Menghapus Izin IAM Cloud Storage untuk Pengguna Lain

### Langkah 1: Bersihkan Izin Akses Pengguna
1. Buka menu **IAM & Admin** > **IAM**.
2. Cari pengguna bernama **User 2** seperti: `student-01-01eb2d469292@qwiklabs.net` sebagaimana pada petunjuk pada task ini, di daftar *principals*. Bukan user aktif student Anda!.
3. Klik ikon pensil (**Edit principal**) di sebelah kanan baris nama User 2.
4. Di daftar peran (*roles*), temukan peran yang berkaitan dengan Cloud Storage (seperti `Storage Admin`, `Storage Object Admin`, atau `Storage Object Viewer`).

Sebelum menghapus, **perhatian perintah pada task ini**,
dalam contoh ini adalah hanya 'menghapus role Cloud Storage' dan membiarkan role project viewer

    ```
    - Leave the IAM role for project viewer.
    - Remove only the IAM role for Cloud Storage.
    ```
    
5. Klik ikon **Tempat Sampah (Delete)** disamping role yang akan dihapus.
6. Klik **Save**

