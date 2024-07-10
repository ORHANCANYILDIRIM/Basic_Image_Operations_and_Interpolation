Giriş

   Bu ödevde, Dijital Görüntü İşleme dersi kapsamında seçilen bir görsel üzerinde büyütme, küçültme, döndürme ve zoom in-zoom out işlemlerini belirli interpolasyon teknikleriyle destekleyerek, görsel üzerinde uygulanan interpolasyon tekniklerinin etkisini görmek amaçlanmıştır.

                                Kullanılan Teknolojiler
•	    Python programlama dili
•	PyQt5 (Python için bir GUI toolkit)
•	PIL (Python Imaging Library)
•	Matematik kütüphaneleri (math)


 				  Uygulama Arayüzü
    Uygulama, ana pencere üzerinde görsel bir arayüz sağlar. Kullanıcı, görüntü işleme işlevlerini kullanmak için butonlar ve girdi alanları aracılığıyla etkileşime geçebilir. Ayrıca, orijinal görüntü ve işlem sonucunu karşılaştırmak için iki ayrı çerçeve bulunmaktadır.Ayrıca proje ilk açıldığında bir görsel seçme imkanı sunar.

 
![image](https://github.com/ORHANCANYILDIRIM/Scurve_Houg_deblur_count/assets/99863519/f341d792-0221-4b45-b594-4a3209eedff5)

![image](https://github.com/ORHANCANYILDIRIM/Scurve_Houg_deblur_count/assets/99863519/27094a3d-72cd-455a-9d76-36f0b195c7ad)


 


                          Uygulamanın İşlevselliği
   Uygulama, aşağıdaki temel işlevselliklere sahiptir:
•	Görüntü seçme: Kullanıcı, bir dosya seçme diyaloğu aracılığıyla bir görüntü dosyası seçebilir.
•	Büyütme: Seçilen görüntüyü istenilen oranda büyütebilir.
•	def buyutme(self):
    if self.label_gorsel.pixmap() is not None:
        try:
            oran = 1 + (float(self.buyutme_orani_giris.text()) / 100)
            pixmap = self.label_gorsel.pixmap()
            genislik = int(pixmap.width() * oran)
            yukseklik = int(pixmap.height() * oran)

            # Yeni boyutta pixmap oluştur
            buyuk_pixmap = QPixmap(genislik, yukseklik)

            # Yeni boyutta pixmap oluştur (interpolasyon yapılmamış)
            buyuk_pixmap_interpolationsuz = QPixmap(genislik, yukseklik)

            # Büyütme işlemini gerçekleştir
            painter = QPainter(buyuk_pixmap)
            painter_interpolationsuz = QPainter(buyuk_pixmap_interpolationsuz)
            for x in range(genislik):
                for y in range(yukseklik):
                    # Yeni koordinatları oranla hesapla ve tam sayıya dönüştür
                    new_x = int(x / oran)
                    new_y = int(y / oran)
                    # Yeni piksel değerini hesapla (ortalama değeri alarak)
                    average_color = average_interpolation(pixmap.toImage(), new_x, new_y)
                    # Pikseli büyük pixmap'e çiz
                    painter.setPen(average_color)
                    painter.drawPoint(x, y)

                    # Interpolasyon yapılmamış halini hesapla
                    old_x = int(new_x / oran)
                    old_y = int(new_y / oran)
                    color_interpolationsuz = pixmap.toImage().pixelColor(old_x, old_y)
                    painter_interpolationsuz.setPen(color_interpolationsuz)
                    painter_interpolationsuz.drawPoint(x, y)
            painter.end()
            painter_interpolationsuz.end()

            # Yeni pixmap'leri göster
            self.label_gorsel.setPixmap(buyuk_pixmap)
            self.label_gorsel_2.setPixmap(buyuk_pixmap_interpolationsuz)
        except Exception as e:
            self.show_alert("Büyütme işlemi sırasında bir hata oluştu: {}".format(str(e)))
    else:
        self.show_alert("Lütfen bir görsel seçin.")

•	Küçültme: Seçilen görüntüyü istenilen oranda küçültebilir.

def kucultme(self):
    if self.label_gorsel.pixmap() is not None:
        try:
            oran = 1 - (float(self.kucultme_orani_giris.text()) / 100)
            pixmap = self.label_gorsel.pixmap()
            genislik = int(pixmap.width() * oran)
            yukseklik = int(pixmap.height() * oran)

            # Küçültülmüş resmi oluştur
            kucultulmus_pixmap = QPixmap(genislik, yukseklik)

            kucultulmus_pixmap_interpolationsuz = QPixmap(genislik, yukseklik)

            # Küçültme işlemini gerçekleştir
            painter = QPainter(kucultulmus_pixmap)
            painter_interpolationsuz = QPainter(kucultulmus_pixmap)
            for x in range(genislik):
                for y in range(yukseklik):
                    # Yeni koordinatları oranla hesapla ve tam sayıya dönüştür
                    new_x = int(x / oran)
                    new_y = int(y / oran)
                    # Bilinear interpolasyon kullanarak pikseli al
                    interpolated_color = bilinear_interpolation(pixmap.toImage(), new_x, new_y)
                    # Pikseli çiz
                    painter.setPen(interpolated_color)
                    painter.drawPoint(x, y)

                    # Interpolasyon yapılmamış halini hesapla
                    old_x = int(new_x * oran)
                    old_y = int(new_y * oran)
                    color_interpolationsuz = pixmap.toImage().pixelColor(old_x, old_y)
                    painter_interpolationsuz.setPen(color_interpolationsuz)
                    painter_interpolationsuz.drawPoint(x, y)
            painter.end()
            painter_interpolationsuz.end()

            # Yeni pixmap'i göster
            self.label_gorsel.setPixmap(kucultulmus_pixmap)
            self.label_gorsel_2.setPixmap(kucultulmus_pixmap_interpolationsuz)
        except Exception as e:
            self.show_alert("Küçültme işlemi sırasında bir hata oluştu: {}".format(str(e)))
    else:
        self.show_alert("Lütfen bir görsel seçin.")

•	Döndürme: Seçilen görüntüyü belirli bir dereceye kadar döndürebilir.
•	def dondurme(self):
    if self.label_gorsel.pixmap() is not None:
        try:
            pixmap = self.label_gorsel.pixmap()
            genislik = pixmap.width()
            yukseklik = pixmap.height()
            dondurulmus_image = QImage(genislik, yukseklik, QImage.Format_ARGB32)
            dondurulmus_image_interpolationsuz = QImage(genislik, yukseklik, QImage.Format_ARGB32)

            # Derece cinsinden verilen açıyı radyan cinsine dönüştür
            radian_aci = math.radians(int(self.donme_orani_giris.text()))

            # Orjinal resmin merkezini hesapla
            merkez_x = genislik / 2
            merkez_y = yukseklik / 2

            for x in range(genislik):
                for y in range(yukseklik):
                    # Pikselin orijinal koordinatlarına göre merkeze göre olan vektörü hesapla
                    dx = x - merkez_x
                    dy = y - merkez_y

                    # Döndürme matrisini uygula
                    yeni_x = int(dx * math.cos(radian_aci) - dy * math.sin(radian_aci) + merkez_x)
                    yeni_y = int(dx * math.sin(radian_aci) + dy * math.cos(radian_aci) + merkez_y)

                    # Yeni piksel değerini al
                    if 0 <= yeni_x < genislik and 0 <= yeni_y < yukseklik:
                        # Interpolasyon yapılmış resmi elde et
                        piksel = bicubic_interpolation(pixmap.toImage(), yeni_x, yeni_y)
                        dondurulmus_image.setPixelColor(x, y, piksel)

                        # Interpolasyon yapılmamış (orijinal) resmi elde et
                        piksel_interpolationsuz = pixmap.toImage().pixelColor(yeni_x, yeni_y)
                        dondurulmus_image_interpolationsuz.setPixelColor(x, y, piksel_interpolationsuz)

            # Döndürülmüş QImage'leri QPixmap'e dönüştür
            dondurulmus_pixmap = QPixmap.fromImage(dondurulmus_image)
            dondurulmus_pixmap_interpolationsuz = QPixmap.fromImage(dondurulmus_image_interpolationsuz)

            # İlk çerçeveye interpolasyon yapılmış resmi, ikinci çerçeveye interpolasyon yapılmamış (orijinal) resmi göster
            self.label_gorsel.setPixmap(dondurulmus_pixmap)
            self.label_gorsel_2.setPixmap(dondurulmus_pixmap_interpolationsuz)
        except Exception as e:
            print("Hata:", e)
            self.show_alert("Döndürme işlemi sırasında bir hata oluştu.")
    else:
        self.show_alert("Lütfen bir görsel seçin.")



•	Zoom in-Zoom out: Görüntünün üzerinde scroll tuşu ile büyütme ve küçültme işlemi yapar.
def wheelEvent(self, event):
    if self.label_gorsel.pixmap() is not None:
        angle = event.angleDelta().y() / 120  # 120 birim scroll'da bir artış/azalış
        faktor = 1.1 if angle > 0 else 0.9  # Zoom in veya zoom out faktörüne karar verme
        pixmap = self.label_gorsel.pixmap()
        genislik = int(pixmap.width() * faktor)
        yukseklik = int(pixmap.height() * faktor)
        zoomed_pixmap = pixmap.scaled(genislik, yukseklik, Qt.KeepAspectRatio)
        self.label_gorsel.setPixmap(zoomed_pixmap)
    else:
        self.show_alert("Lütfen bir görsel seçin.")

•	Farklı interpolasyon yöntemleri: Görüntü işlemlerinde farklı interpolasyon tekniklerini uygular.
                                       
                                 

                                     
                                  
                                  İşlevlerin Açıklaması
•	Bicubic Interpolasyon: Piksel değerlerini bir polinom kullanarak yaklaşıklayan bir yöntemdir. Bu yöntem için yazdığım kodun açıklaması şu şekildedir;
Parametreleri Ayarlama: Fonksiyon, interpolasyon yapılacak görüntüyü (img) ve interpolasyon noktasının koordinatlarını (x ve y) alır.

Koordinatları Ayırma: İlk olarak, interpolasyon yapılacak noktanın koordinatları tamsayı ve kesirli kısımlar olarak ayrılır. Bu, interpolasyon için kullanılacak komşu piksellerin belirlenmesine yardımcı olur.

Ağırlık Matrisi Oluşturma: Bicubic interpolasyon, piksel değerlerini hesaplarken kullanılacak bir ağırlık matrisi kullanır. Bu matris, her bir komşu pikselin interpolasyon işlemindeki katkısını belirler. Örneğin, pikselin tam merkezindeki pikselin katkısı daha fazladır, kenarlardaki piksellerin katkısı ise daha azdır.

Komşu Pikselleri Almak: Interpolasyon için kullanılacak piksel değerleri, interpolasyon noktasının çevresindeki komşu piksellerden alınır. Bu, piksel değerlerini belirli bir alanda yoğunlaştırarak interpolasyonun daha doğru yapılmasını sağlar.

Interpolasyon İşlemi: Her bir renk kanalı (RGBA) için ayrı ayrı bir interpolasyon işlemi yapılır. Her bir piksel değeri, komşu piksellerin değerleri ve ağırlık matrisi kullanılarak hesaplanır. Bu hesaplama, her bir komşu pikselin değeri ile o piksele atanmış ağırlık değeri arasındaki çarpımın toplamı şeklinde gerçekleştirilir.

Sonuçları Sınırlandırma ve Dönüştürme: Elde edilen interpolasyon sonuçları, sınırlandırılarak renk değerlerinin 0 ile 255 arasında olmasına ve tam sayı olmasına dikkat edilir. Bu adım, renk değerlerinin uygun bir şekilde temsil edilmesini sağlar.

Sonuçları Döndürme: Interpolasyon sonuçları, yeni bir renk nesnesi olarak döndürülür. Bu nesne, interpolasyon yapılan pikselin yeni renk değerlerini içerir.
def bicubic_interpolation(img, x, y):
    # Interpolasyon noktasının koordinatlarını tamsayı ve kesirli kısımlar olarak ayırın
    x_int = int(x)
    y_int = int(y)
    dx = x - x_int
    dy = y - y_int

    # Interpolasyon için ağırlık matrisi
    weights = [[1, 0, 0, 0, 0, 0],
               [0, 0, 1, 0, 0, 0],
               [-1 / 3, 1, -1 / 3, -1 / 3, 1 / 3, 0],
               [1 / 6, -1 / 2, 0, 1 / 2, -1 / 6, 0],
               [-1 / 6, 2 / 3, 0, -2 / 3, 1 / 6, 0],
               [1 / 24, -1 / 6, 1 / 4, 1 / 6, -1 / 24, 0]
               ]

    # Hata ayıklama modu
    try:
        # Piksel değerlerini çevresindeki komşu piksellerden alın
        pixels = []
        for i in range(x_int - 1, x_int + 3):
            row = []
            for j in range(y_int - 1, y_int + 3):
                # Piksel değerlerini alırken sınırları kontrol etmeliyiz
                px = max(0, min(i, img.width() - 1))
                py = max(0, min(j, img.height() - 1))
                row.append(img.pixelColor(px, py).getRgb())  # Renk kanallarını al ve listeye ekle
            pixels.append(row)

        # Her bir kanal için ayrı ayrı interpolasyon yap
        interpolated_channels = []
        for c in range(4):  # 4 kanal (RGBA)
            # Her bir kanal için değerler al
            interpolated_pixel = 0
            for i in range(4):
                for j in range(4):
                    interpolated_pixel += pixels[i][j][c] * weights[i][j] * ((dx ** i) * (dy ** j))

            # Interpolasyon sonucunu ekle
            interpolated_channels.append(int(max(0, min(interpolated_pixel, 255))))  # Renk değerlerini sınırlandır ve integer'a dönüştür

        # Interpolasyon sonucunu dön
        return QColor(*interpolated_channels)
    except Exception as e:
        print("Bicubic Interpolasyon Hatası:", e)
        return QColor(0, 0, 0, 255)  # Hata durumunda siyah bir piksel dön


•	Bilinear Interpolasyon: Bilinen piksel değerlerini kullanarak, bir görüntüdeki belirli bir noktanın renk değerini hesaplayan bir yöntemdir. Bu yöntem için yazdığım kodun açıklaması şu şekildedir;
Parametreleri Ayarlama: Fonksiyon, interpolasyon yapılacak görüntüyü (img) ve interpolasyon noktasının koordinatlarını (x ve y) alır.

Koordinatları Ayırma: İlk olarak, interpolasyon yapılacak noktanın koordinatları tamsayı ve kesirli kısımlar olarak ayrılır. Bu, interpolasyon için kullanılacak komşu piksellerin belirlenmesine yardımcı olur.

Komşu Pikselleri Belirleme: Interpolasyon için kullanılacak dört komşu pikselin koordinatları belirlenir. Bu dört piksel, interpolasyon işleminde kullanılacak temel noktaları sağlar.

Piksel Değerlerini Alma: Belirlenen komşu piksellerden renk değerleri alınır. Bu, interpolasyon işleminin temelini oluşturur.

Interpolasyon İşlemi: Her bir renk kanalı (RGB) için ayrı ayrı bir interpolasyon işlemi yapılır. Bu işlem, komşu piksellerin renk değerleri ve interpolasyon noktasının konumu kullanılarak gerçekleştirilir.

Sonuçları Dönme: Yeni pikselin interpolasyon sonucu hesaplanan renk değerleri, yeni bir renk nesnesi olarak döndürülür.

def bilinear_interpolation(img, x, y):
    x_int = int(x)
    y_int = int(y)
    dx = x - x_int
    dy = y - y_int

    # Komşu dört pikselin koordinatlarını belirle
    x1 = max(0, min(x_int, img.width() - 1))
    x2 = max(0, min(x1 + 1, img.width() - 1))
    y1 = max(0, min(y_int, img.height() - 1))
    y2 = max(0, min(y1 + 1, img.height() - 1))

    # Komşu dört pikselin renk değerlerini al
    color1 = img.pixelColor(x1, y1).getRgb()
    color2 = img.pixelColor(x2, y1).getRgb()
    color3 = img.pixelColor(x1, y2).getRgb()
    color4 = img.pixelColor(x2, y2).getRgb()

    # Pikselin yeni değerini hesapla
    new_color = []
    for i in range(3):  # RGB kanalları için
        # İnterpolasyon hesapla
        interpolated_channel = (1 - dx) * (1 - dy) * color1[i] + dx * (1 - dy) * color2[i] + (1 - dx) * dy * color3[i] + dx * dy * color4[i]
        new_color.append(int(interpolated_channel))

    # Yeni pikselin rengini döndür
    return QColor(*new_color)



•	Average Interpolasyon: bir pikselin değerini, çevresindeki komşu piksellerin renklerinin ortalaması alınarak hesaplayan bir yöntemdir. Bu yöntem için yazdığım kodun açıklaması şu şekildedir;

Parametreleri Ayarlama: Fonksiyon, interpolasyon yapılacak görüntüyü (img) ve interpolasyon noktasının koordinatlarını (x ve y) alır.

Koordinatları Ayırma: İlk olarak, interpolasyon yapılacak noktanın koordinatları tamsayı ve kesirli kısımlar olarak ayrılır. Bu, interpolasyon için kullanılacak komşu piksellerin belirlenmesine yardımcı olur.

Komşu Piksellerin Renklerini Toplama: Noktanın çevresindeki 2x2 alandaki piksellerin renk değerleri toplanır. Bu adım, interpolasyon için temel renkleri sağlar.

Ortalama Alma: Toplanan renk değerleri, komşu piksellerin sayısına bölünerek ortalama alınır. Bu, interpolasyonun sonucunu oluşturur.

Renk Değerlerini Sınırlandırma: Elde edilen ortalama renk değerleri, 0 ile 255 arasında olacak şekilde sınırlandırılır. Bu adım, renk değerlerinin uygun bir şekilde temsil edilmesini sağlar.

Sonuçları Dönme: Yeni pikselin interpolasyon sonucu hesaplanan renk değerleri, yeni bir renk nesnesi olarak döndürülür.
def average_interpolation(img, x, y):
    # Interpolasyon noktasının koordinatlarını tamsayı ve kesirli kısımlar olarak ayırın
    x_int = int(x)
    y_int = int(y)
    dx = x - x_int
    dy = y - y_int

    # Komşu piksellerin renklerini topla
    total_color = [0, 0, 0, 0]
    for i in range(x_int, x_int + 2):
        for j in range(y_int, y_int + 2):
            # Piksel değerlerini alırken sınırları kontrol etmeliyiz
            px = max(0, min(i, img.width() - 1))
            py = max(0, min(j, img.height() - 1))
            color = img.pixelColor(px, py).getRgb()  # Renk kanallarını al
            # Renk kanallarını topla
            total_color[0] += color[0]
            total_color[1] += color[1]
            total_color[2] += color[2]
            total_color[3] += color[3]

    # Komşu piksellerin ortalamasını al
    num_pixels = 4  # 2x2 piksel olduğu için
    average_color = [c / num_pixels for c in total_color]

    # Renk değerlerini sınırlandır ve QColor nesnesi oluşturarak dön
    color = QColor(*[int(max(0, min(c, 255))) for c in average_color])

    return color


  Sonuç ve Değerlendirme
    Bu proje geliştirilebilir ve farklı teknilerin uygulanmasına açık bir projedir. Ayrıca İnterpolasyon tekniklerinin Görsel işleme konusunda çok önemli bir adım olduğunu anlamamı sağlayan yararlı bir proje ödevidir.
										Orhancan Yıldırım

		                                                                                                     
