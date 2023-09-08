import requests
from bs4 import BeautifulSoup
import time
import os

#Web sitesinin URL'si
url="https://www.ytukampus.com/ders-notlari/"

#Ders notlarının kaydedileceği yer
kayit_klasoru="C:\Dersnotlari"

#Yeni ders notlarını kontrol etme sıklığı
kontrol_araligi=20 #3 gün

while True:
    try:
        # Web sitesini ziyaret et
        response = requests.get(url)

        #HTML içeriğini çek
        html=response.text

        #Beautiful Soup ile HTML'i işle
        soup= BeautifulSoup(html, 'html.parser')


        linkler = soup.find_all('a',href=True) #<a> etiketlerini bul ve  içinde geçen kelimeyi içeren bağlantıları bul
        anahtar_kelime="Matematik 1 Ders Notları"
        for link in linkler:
            link_url=link['href'];


            if anahtar_kelime in link_url: #İstenen kelimeyi içeren bağlantıları indirir
                response=requests.get(link_url) #link_url toplanır
                dosya_adi =os.path.basename(link_url)
                dosya_yolu=os.path.join(kayit_klasoru,dosya_adi)

                with open(dosya_yolu,'wb') as dosya: # 'wb' içeriği ikili binary sayıya çevirir.
                    kayit_klasoru.write(response.content) # istenilen klasöre yazdırır
            else:
                continue

        print("dosyalar indirildi.")

        time.sleep(kontrol_araligi) # Yeni ders notlarını kontrol etmek için belirli bir süre bekle

    except Exception as e:  # Hata durumlarını yönetmek için burada gerekli işlemler yapılır
        print("Hata:", str(e))

