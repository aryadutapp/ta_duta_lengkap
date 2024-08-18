<!-- ABOUT THE PROJECT -->
# Implementasi

Jika anda sudah pada fase ini, diasumsikan telah selesai melakukan finetuning dan model LLM yang baru telah tersedia.

Sebelum melangkah lebih jauh, mari mencoba dulu model yang sudah di finetune.

Berikut notebook sederhana untuk mencoba inferrence (dengan menggunakan LoRA adapter yang telah diupload di Huggingface) [Notebook](./simple-inf.ipypnb)

Setelah memastikan model berhasil dijalankan, jika anda ingin melakukan evaluasi terhadap model (selain training dan eval loss), berikut notebook yang dapat digunakan (asumsi dataset testing sudah diupload di HF dengan format yang sama dengan dataset train) dengan menggunakan metode exact-match [Notebook](./simple-eval.ipypnb)

Setelah model LLM selesai di finetune, kita masih ada beberapa tugas sebelum bisa mengimplementasikannya ke robot nyata.

- [x] Finetuning gemma-2B untuk LLM
- [ ] Implementasi persepsi robot dengan webcam dan opencv (deteksi objek berdasarkan warna)
- [ ] Membuat web untuk antarmuka robot dan API inferrence
- [ ] Kalirasi robot dengan kamera dan lingkungan
- [ ] Menghubungkan semuanya

Note: Untuk kode inferrence LLM dan menjalankan robot dijalankan di dua komputer terpisah. Hal ini dikarenakan untuk inferrence LLM. Sederhananya Low Performance Computer (LPC) akan terhubung ke robot dan kamera dan juga hosting halaman web sederhana sebagai antarmuka kontrol untuk memasukkan text. Sedaangkan High Performance Computer (HPC) akan digunakan untuk inferrence LLM dan hosting API Inferrence agar LPC dapat membuat request inferrence ke HPC. Dalam implementasi nyata hanya terdapat dua file kode yang dijalankan (inferrence.ipypb dan app.py). Jika ingin dijalankan dalam satu komputer (1 HPC), bisa langsung menjalankan dua kode dalam satu komputer. 

Untuk penjelasan selanjutnya asumsikan saya hanya membahas untuk kode app.py kecuali saat membahas pembuatan endpoint untuk inferrence

## Implementasi persepsi robot dengan webcam dan opencv (deteksi objek berdasarkan warna)

Semua kode ini nantinya akan di app.py di fungsi detect_blocks():

Dalam kode ini hanya mendeteksi tepat satu balok biru, jingga, dan kuning.

Untuk deteksi objek dilakukan dengan webcam dan diproses dengan OpenCV untuk klasifikasi berdasarkan warna. Jika ingin menggunakan metode lain, dapat dilakukan. 

```
# Function to detect blocks
# Input = Image frame
# Output = frame of detected object (camera with bounding box ) and update detected_object global coordinate

def detect_blocks(frame, min_size=100, max_size=5000):
    hsv_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)
    ......
    ......
    detected_coordinates = detected_objects  # Update global coordinate
    return result_frame
```



