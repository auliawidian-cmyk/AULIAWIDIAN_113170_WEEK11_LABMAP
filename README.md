SSAN APK : https://drive.google.com/drive/folders/1AiGaqcDsLF8_h8Pu4uG2EIOrhlkTu-IF?usp=sharing

LAB_11_A : https://github.com/auliawidian-cmyk/LAB_WEEK_11_A

LAB_11_B : https://github.com/auliawidian-cmyk/LAB_WEEK_11_B

ASSEMENT : ASSIGNMENT — AULIA WIDIAN PUTRI

1. **Bagian kode yang menangani penyimpanan foto berdasarkan URI**

Penyimpanan foto dilakukan saat fungsi `insertImageToStore()` memanggil `insertToStore()`. Bagian kode berikut adalah yang bertanggung jawab menyimpan file berdasarkan URI:

fun insertImageToStore(fileInfo: FileInfo?) {
    fileInfo?.let {
        insertToStore(
            fileInfo,
            mediaContentHelper.getImageContentUri(),
            mediaContentHelper.generateImageContentValues(it)
        )
    }
}

private fun insertToStore(fileInfo: FileInfo, contentUri: Uri, contentValues: ContentValues) {
    executor.execute {
        val insertedUri = contentResolver.insert(contentUri, contentValues)
        insertedUri?.let { destUri ->
            val inputStream = contentResolver.openInputStream(fileInfo.uri)
            val outputStream = contentResolver.openOutputStream(destUri)
            inputStream?.use { input ->
                outputStream?.use { output ->
                    IOUtils.copy(input, output)
                }
            }
        }
    }
}

2. **Arti attribute pertama dan keempat pada FileInfo.kt**

- `uri` → URI ini adalah URI sementara yang diberikan oleh FileProvider. Kamera akan menulis file pertama kali ke path ini sebelum file disalin ke MediaStore.
- `relativePath` → Ini adalah folder tujuan di dalam MediaStore tempat file akan disimpan secara permanen. Contohnya: "Pictures/" untuk foto, atau "Movies/" untuk video.

3. **[Bonus] Urutan proses dari mengambil foto hingga tersimpan di MediaStore**

a. Pengguna menekan tombol **Photo**.  
b. Fungsi `openImageCapture()` dipanggil dan `generatePhotoUri()` membuat FileInfo berisi:
   - File kosong sementara
   - URI FileProvider
   - Nama file
c. `takePictureLauncher.launch(uri)` membuka aplikasi kamera.  
d. Kamera menulis gambar ke file sementara sesuai URI.  
e. Setelah TakePicture() sukses, fungsi `insertImageToStore()` dipanggil.  
f. `insertToStore()` membuat entry baru di MediaStore dengan `contentResolver.insert()`.  
g. File sementara disalin ke MediaStore menggunakan InputStream → OutputStream.  
h. File kini muncul di aplikasi Gallery atau aplikasi yang membaca MediaStore.

