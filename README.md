# Otomatik-Veri-Cekici
import os
import json
from datetime import datetime
import requests
from bs4 import BeautifulSoup

def veri_cek():
    # Örnek: TCMB veya başka bir API/Web sayfasından basit dolar kuru çekimi
    # (İstediğiniz herhangi bir siteyi veya oyun haberini buraya uyarlayabilirsiniz)
    url = "https://exchangerate-api.com"
    
    try:
        response = requests.get(url)
        data = response.json()
        try:
            try_rate = data["rates"]["TRY"]
        except KeyError:
            try_rate = "Veri alınamadı"
            
        guncel_veri = {
            "tarih": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
            "usd_try": try_rate,
            "durum": "Başarılı"
        }
    except Exception as e:
        guncel_veri = {
            "tarih": datetime.now().strftime("%Y-%m-%d %H:%M:%S"),
            "hata": str(e),
            "durum": "Hata oluştu"
        }

    # Veriyi JSON dosyasına yaz/güncelle
    dosya_adi = "veriler.json"
    
    # Eğer dosya varsa eski verileri oku, yoksa boş liste oluştur
    if os.path.exists(dosya_adi):
        with open(dosya_adi, "r", encoding="utf-8") as f:
            try:
                veri_havuzu = json.load(f)
                if not isinstance(veri_havuzu, list):
                    veri_havuzu = []
            except json.JSONDecodeError:
                veri_havuzu = []
    else:
        veri_havuzu = []

    # Yeni veriyi ekle (Son 30 günü tutacak şekilde sınırlayabilirsiniz)
    veri_havuzu.append(guncel_veri)
    if len(veri_havuzu) > 30:
        veri_havuzu.pop(0)

    with open(dosya_adi, "w", encoding="utf-8") as f:
        json.dump(veri_havuzu, f, ensure_ascii=False, indent=4)
        
    print(f"Veri başarıyla kaydedildi: {guncel_veri}")

if __name__ == "__main__":
    veri_cek()
