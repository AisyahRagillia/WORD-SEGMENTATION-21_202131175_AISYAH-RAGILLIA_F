
# TEORI WORD SEGMENTATION
Metode yang digunakan dalam codingan word segmentation di atas adalah pendekatan berbasis kontur untuk mendeteksi huruf-huruf dalam sebuah gambar. Berikut ini teori yang mendukung metode tersebut:

1. Konversi ke Skala Keabuan (Grayscale):
   Pertama, gambar asli dikonversi ke skala keabuan menggunakan fungsi `color.rgb2gray()`. Konversi ini mengubah setiap piksel gambar menjadi tingkat keabuan yang berkisar antara 0 (hitam) hingga 1 (putih). Hal ini dilakukan agar informasi yang relevan dalam gambar dapat ditemukan lebih mudah, terutama untuk mendeteksi kontur dan bentuk huruf-huruf.

2. Thresholding:
   Setelah konversi ke skala keabuan, dilakukan proses thresholding untuk mengubah gambar menjadi binary, yaitu hanya memiliki dua nilai piksel: hitam dan putih. Dalam kasus ini, thresholding dilakukan dengan menggunakan operasi `gray_img < 0.5`, yang menghasilkan nilai piksel hitam (0) jika tingkat keabuan lebih kecil dari 0.5, dan nilai piksel putih (1) jika tingkat keabuan lebih besar atau sama dengan 0.5. Dengan demikian, bagian gambar yang mewakili huruf-huruf akan terlihat lebih jelas dan memudahkan proses deteksi.

3. Operasi Morfologi:
   Setelah thresholding, dilakukan operasi morfologi pada gambar binary untuk membersihkan noise dan memisahkan huruf-huruf yang saling berdekatan. Dalam codingan ini, digunakan fungsi `measure.label()` untuk memberikan label pada komponen terhubung dalam gambar binary. Proses ini menghasilkan gambar yang terdiri dari objek-objek terisolasi yang merepresentasikan huruf-huruf.

4. Deteksi Kontur:
   Setelah proses operasi morfologi, dilakukan deteksi kontur menggunakan fungsi `measure.find_contours()` pada gambar dengan label komponen terhubung. Kontur-kontur ini merepresentasikan batas-batas visual antara huruf-huruf. Kontur tersebut ditemukan dengan menelusuri piksel-piksel yang membentuk batas objek dalam gambar.

5. Deteksi Huruf:
   Pada langkah ini, dilakukan deteksi huruf berdasarkan area dan aspek rasio kontur. Setiap kontur yang ditemukan diukur luasnya dan dihitung aspek rasio antara lebar dan tinggi kontur. Dalam codingan ini, hanya kontur-kontur dengan luas lebih besar dari 100 dan aspek rasio lebih besar dari 0.2 yang dianggap sebagai huruf yang terdeteksi. Koordinat dan ukuran persegi panjang yang mengelilingi huruf yang terdeteksi disimpan dalam list `detected_letters`.

6. Visualisasi Hasil:
   Setelah proses deteksi huruf, dilakukan visualisasi hasilnya dengan menampilkan gambar asli dan gambar yang telah dideteksi hurufnya. Pada gambar hasil deteksi huruf, persegi panjang yang mengelilingi huruf yang terdeteksi ditambahkan menggunakan `plt.Rectangle()` untuk menunjukkan lokasi dan ukuran

 huruf yang terdeteksi.

Dengan menggunakan metode word segmentation seperti ini, kita dapat mendeteksi dan memisahkan huruf-huruf dalam sebuah gambar dengan memanfaatkan informasi kontur dan aspek rasio.

# CODINGAN DAN PENJELASAN

        import matplotlib.pyplot as plt
        from skimage import io, color, measure

        # Fungsi untuk mendeteksi huruf
        def detect_letters(image_path):
        # Baca gambar menggunakan scikit-image
        img = io.imread(image_path)
        
        # Ubah gambar ke skala keabuan
        gray_img = color.rgb2gray(img)
        
        # Lakukan thresholding untuk mengubah gambar ke binary
        threshold = gray_img < 0.5
        
        # Terapkan operasi morfologi untuk membersihkan gambar
        cleaned_img = measure.label(threshold)
        
        # Temukan kontur huruf
        contours = measure.find_contours(cleaned_img, 0.8)
        
        # Deteksi huruf berdasarkan area dan aspek rasio
        detected_letters = []
        for contour in contours:
            y, x = contour[:, 0], contour[:, 1]
            ymin, ymax = int(min(x)), int(max(x))
            xmin, xmax = int(min(y)), int(max(y))
            width = xmax - xmin
            height = ymax - ymin
            area = width * height
            
            # Periksa apakah height tidak sama dengan nol sebelum melakukan perhitungan aspek rasio
            if height != 0:
                aspect_ratio = width / height
                if area > 100 and aspect_ratio > 0.2:
                    detected_letters.append((xmin, ymin, width, height))
        
        return detected_letters

    # Path file gambar
    image_path = 'AISYAH.jpg'

    # Buka gambar menggunakan scikit-image
    image = io.imread(image_path)

    # Tampilkan gambar awal
    plt.subplot(1, 2, 1)
    plt.imshow(image)
    plt.axis('off')
    plt.title('GAMBAR ASLI')

    # Panggil fungsi deteksi huruf
    detected_letters = detect_letters(image_path)

    # Tampilkan gambar dengan persegi panjang yang mengelilingi huruf yang terdeteksi
    plt.subplot(1, 2, 2)
    plt.imshow(image)
    plt.axis('off')
    plt.title('GAMBAR SETELAH DITEKSI TEXT')

    # Tampilkan persegi panjang yang mengelilingi huruf yang terdeteksi
    for (x, y, width, height) in detected_letters:
        rect = plt.Rectangle((y, x), height, width, edgecolor='g', linewidth=2, fill=False)
        plt.gca().add_patch(rect)

    # Tampilkan gambar yang telah dideteksi hurufnya
    plt.show()


Berikut adalah langkah-langkah penyelesaian codingan di atas:

1. Pertama, kita mengimpor library yang dibutuhkan, yaitu `matplotlib.pyplot` untuk menampilkan gambar, `skimage.io` untuk membaca gambar, `skimage.color` untuk mengubah gambar ke skala keabuan, dan `skimage.measure` untuk melakukan operasi pengukuran pada gambar.

2. Kemudian, kita mendefinisikan fungsi `detect_letters(image_path)` yang akan digunakan untuk mendeteksi huruf pada gambar. Pada fungsi ini, langkah-langkahnya adalah:
   - Membaca gambar menggunakan `io.imread()` dan menyimpannya dalam variabel `img`.
   - Mengubah gambar ke skala keabuan menggunakan `color.rgb2gray()` dan menyimpannya dalam variabel `gray_img`.
   - Melakukan thresholding pada gambar dengan mengubahnya menjadi binary menggunakan `gray_img < 0.5` dan menyimpannya dalam variabel `threshold`.
   - Membersihkan gambar dengan menggunakan operasi morfologi `measure.label(threshold)` dan menyimpannya dalam variabel `cleaned_img`.
   - Mencari kontur huruf menggunakan `measure.find_contours(cleaned_img, 0.8)` dan menyimpannya dalam variabel `contours`.
   - Melakukan deteksi huruf berdasarkan area dan aspek rasio kontur. Untuk setiap kontur, dihitung area dan aspek rasio kontur. Jika area lebih besar dari 100 dan aspek rasio lebih besar dari 0.2, maka kontur tersebut dianggap sebagai huruf yang terdeteksi. Koordinat dan ukuran persegi panjang yang mengelilingi huruf tersebut disimpan dalam list `detected_letters`.
   - Mengembalikan list `detected_letters` yang berisi koordinat dan ukuran persegi panjang yang mengelilingi huruf yang terdeteksi.

3. Setelah itu, kita mendefinisikan path file gambar yang akan digunakan, dalam hal ini `image_path` diatur menjadi 'AISYAH.jpg'.

4. Kemudian, kita membuka gambar menggunakan `io.imread(image_path)` dan menyimpannya dalam variabel `image`.

5. Tampilkan gambar asli menggunakan `plt.subplot(1, 2, 1)` untuk membuat subplot pertama, `plt.imshow(image)` untuk menampilkan gambar, `plt.axis('off')` untuk menghilangkan sumbu, dan `plt.title('GAMBAR ASLI')` untuk memberikan judul pada subplot.

6. Panggil fungsi `detect_letters(image_path)` untuk mendeteksi huruf pada gambar. Hasilnya disimpan dalam variabel `detected_letters`.

7. Tampilkan gambar dengan persegi panjang yang mengelilingi huruf yang terdeteksi menggunakan `plt.subplot(1, 2, 2)` untuk membuat subplot kedua, `plt.imshow(image)` untuk menampilkan gambar, `plt.axis('off')` untuk menghilangkan sumbu, dan `plt.title('GAMBAR SETELAH DITEKSI TEXT')` untuk memberikan judul pada subplot.

8. Tampilkan persegi panjang yang mengelilingi huruf yang terdeteksi menggunakan loop `for (x, y, width, height) in detected_letters`. Pada setiap iterasi, buat objek `rect` dengan menggunakan `plt.Rectangle()` yang berisi koordinat dan ukuran perse

gi panjang. Kemudian tambahkan objek `rect` ke plot menggunakan `plt.gca().add_patch(rect)`.

9. Tampilkan gambar yang telah dideteksi hurufnya dengan menggunakan `plt.show()`.

Dengan langkah-langkah tersebut, kita dapat mendeteksi huruf pada gambar dan menampilkan persegi panjang yang mengelilingi huruf yang terdeteksi.