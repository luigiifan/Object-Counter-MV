# Praktikum Machine Vision: Menghitung Objek dan Segmentasi
**Nama Kelompok:**
1. Luigi Ifan Dwiutomo (0922040056)
2. Moch. Baharudin Yusuf Habibie (09220400)

| Library   | Description                                           | Command |
|-----------|-------------------------------------------------------|---------|
| OpenCV    | Used for image processing and computer vision tasks. | `pip install opencv-python` |
| Numpy     | Used for numerical computing and array manipulation. | `pip install numpy`  |
| Sys       | Provides access to system-specific parameters and functions. | (No installation needed) |
| PyQt5     | Used for creating graphical user interfaces (GUI) | `pip install pyqt5` |

## Preprocessing
**I. Scalling**
<br>
![image](https://github.com/user-attachments/assets/8e4ea3be-2a32-42ed-86e6-d570027aaac8)
<br>
**Insight:**
- Mengubah ukuran gambar ke dimensi yang lebih kecil atau standarisasi ukuran ke 800x600 pixel dengan menggunakan metode downsampling Interpolasi Linear.

**II. Grayscale, Gamma Correction and Histogram Equalization**
<br>
![image](https://github.com/user-attachments/assets/fd60260e-9b41-45c6-9da2-d1d60e5c85a1)
<br>
**Insight:**
- Mengonversi gambar menjadi grayscale dengan fungsi `cv.cvtColor`.
- Mengatur kontras menggunakan gamma correction dengan nilai **y = 1.2** yang mengubah intensitas piksel menggunakan rumus **y = 255 x (x/255) 1.2**, dimana **x** adalah intensitas piksel dan **y** adalah intensitas yang sudah dikoreksi. Hal ini membantu memperbaiki pencahayaan yg tidak merata, sehingga objek lebih mudah dideteksi.
- Menyesuaikan kontras dengan histogram equalization yg mendistribusikan ulang intensitas piksel secara merata sehingga meningkatkan kontras gambar. Teknik ini digunakan karena dapat memperjelas objek yg sebelumnya tersembunyi dalam area gelap atau terang.

## Processing
**I. Adaptive Thresholding**
<br>
![image](https://github.com/user-attachments/assets/bc086d8d-889f-4715-9f11-13802fcdea0c)
<br>
**Insight:**
- Melakukan Adaptive Thresholding untuk mengubah gambar grayscale menjadi gambar biner (hitam-putih) dengan ambang batas yg disesuaikan secara lokal berdasarkan piksel-piksel disekitarnya.
- Metode ini menggunakan rata-rata berbobot **Gaussian** untuk menentukan ambang batas lokal pada setiap piksel.
- Dilakukan inverting dari hasil thresholding dengan mengubah piksel dengan nilai 0 mejadi 255 dan nilai 255 menjadi 0 untuk meningkatkan visibilitas objek dalam citra.

**II. Morphological Operation**
<br>
![image](https://github.com/user-attachments/assets/dad5d312-a3aa-4736-b1de-fb30adfff45e)
<br>
**Insight:**
- Melakukan morfologi pada gambar yakni Dilatasi, Erosi dan Closing dengan membuat kernel berupa array 7x7 yang memberikan ruang cukup untuk mengubah objek dan berisi nilai 1 yang berarti semua piksel dalam kernel memiliki bobot yg sama.
- Dilatasi membantu memperbesar objek dan menghubungkan bagian objek yang terpisah.
- Erosi digunakan untuk mengurangi objek tetapi menjaga bentuk objek dari gambar yang telah diperbesar atau melalui dilatasi dan mencegah objek mengecil terlalu banyak.
- Membuat kernel baru untuk operasi closing dimana operasi morfologi untuk menutup lubang atau celah kecil di antara objek.
- Fungsi `cv.medianBlur` digunakan untuk mengurangi noise pada gambar dengan filter median yang bekerja dengan cara menggantikan setiap piksel dengan nilai median dari piksel-piksel sekitarnya dalam jendela kernel 7x7.
- Median Blur efektif untuk menghilangkan noise yang muncul setelah operasi dilatasi dan erosi, terutama pada piksel-piksel yang tidak diinginkan yg dapat menggangu deteksi objek.
- Melakukan labeling pada objek menggunakan fungsi `cv.connectedComponents` yang menandai objek terhubung dalam gambar biner. **ret** adalah jumlah total komponen yang terhubung dalam gambar, sedangkan **labels** adalah gambar yg disertai label untuk menunjukkan setiap objek terhubung yg terindentifikasi.
- Menyesuaikan nilai label untuk perwarnaan menggunakan nilai hue dalam ruang warna HSV dengan normalisasi nilai label dengan rentang 0-179.
- Membuat gambar kosong yg diisi dengan nilai 255 (putih), lalu menggabungkan channel hue untuk membentuk gambar berwarna dengan objek yg diberi warna berbeda berdasarkan labelnya. 
- Mengonversi gambar dari HSV ke BGR agar bisa ditampilkan dalam format warna standar.
- `labeled_img[label_hue == 0] = 0` digunakan untuk mengatur piksel yg memiliki nilai label 0 (background) menjadi hitam sehingga objek saja yg memiliki warna.

## Post Segmentation
![image](https://github.com/user-attachments/assets/dc3312f6-7eca-40a7-ba5b-547eb0e4a17d)
<br>
**Insight:**
- Menemukan kontur objek dengan fungsi <code>cv.findContours</code> yang telah diproses dan hanya kontur luar yang dicari atau objek-objek yang terpisah dan bukan bagian dalam objek.</li>
- Melakukan iterasi untuk setiap kontur pada objek yang ditemukan pada gambar. Selanjutnya, menyaring objek kecil berdasarkan luasnya dengan area lebih besar dari 500 piksel agar menghindari noise atau objek kecil yang tidak relevan.</li>
- Mendapatkan posisi dan ukuran bounding box yang mengelilingi objek berdasarkan kontur dengan fungsi <code>cv.boundingRect</code>.</li>
- Menggambar bounding box dan menambahkan teks untuk menunjukan nomor objek yang berada pada koordinat di atas bounding box.</li>

## Demo
<p><img src="https://raw.githubusercontent.com/luigiifan/object-counter/master/demo.gif")</p>

**Download RE (Release Executable)**: [lgiifn.engineer/object-counter](https://lgiifn.engineer/object-counter)








