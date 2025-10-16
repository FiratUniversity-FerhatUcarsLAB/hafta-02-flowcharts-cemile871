BASLA SISTEM

    // 1. KULLANICI GİRİŞİ
    BASLA KULLANICI_GIRISI
        KULLANICI_GIRDI ← GİRİŞ_BİLGİSİ_AL()
        
        EGER KULLANICI_GIRDI = YENI_KAYIT_ISTEK
            ISE KAYIT_OL()
        DEGILSE
            DOGRU_MU ← KULLANICI_DOGRULAMA(KULLANICI_GIRDI)
            
            EGER DOGRU_MU = HAYIR
                ISE MESAJ_GÖSTER("Geçersiz kullanıcı bilgisi.")
                CIK()
            DEGILSE
                OTURUM_AC(KULLANICI_GIRDI)
        BITIR EGER
    BITIR KULLANICI_GIRISI


    // 2. ÜRÜN EKLEME ve SEPET YÖNETİMİ
    BASLA SEPET_ISLEMLERI
        SEPET ← BOS_SEPET_OLUSTUR()
        
        DONGU KULLANICI_URUN_EKLEMEK_ISTEDIKCE
            URUN_ID ← URUN_SEC()
            ADET ← MIKTAR_SEC()
            
            STOK ← STOK_SORGULA(URUN_ID)
            
            EGER STOK >= ADET
                ISE SEPETE_EKLE(SEPET, URUN_ID, ADET)
            DEGILSE
                MESAJ_GÖSTER("Yetersiz stok!")
            BITIR EGER
        BITIR DONGU
        
        SEPET_GÖSTER(SEPET)
    BITIR SEPET_ISLEMLERI


    // 3. STOK KONTROLÜ (ÖDEME ÖNCESİ)
    BASLA STOK_KONTROL
        HER_URUN ← SEPET_URUNLERI(SEPET)
        
        DONGU HER_URUN ICIN
            STOK ← STOK_SORGULA(HER_URUN.ID)
            EGER STOK < HER_URUN.ADET
                ISE
                    MESAJ_GÖSTER("Bazı ürünler stokta yok.")
                    SIPARIS_ENGELLE()
                    CIK()
            BITIR EGER
        BITIR DONGU
    BITIR STOK_KONTROL


    // 4. INDIRIM KODU KONTROLÜ
    BASLA INDIRIM_KONTROL
        KOD ← KULLANICIDAN_AL("İndirim kodu giriniz (boş bırakabilirsiniz):")
        
        EGER KOD ≠ BOS
            ISE
                GECERLI_MI ← KOD_DOGRULA(KOD)
                EGER GECERLI_MI = EVET
                    ISE SEPETE_INDIRIM_UYGULA(SEPET, KOD)
                DEGILSE
                    MESAJ_GÖSTER("Geçersiz veya süresi dolmuş indirim kodu.")
                BITIR EGER
        BITIR EGER
    BITIR INDIRIM_KONTROL


    // 5. KARGO HESAPLAMA
    BASLA KARGO_HESAPLAMA
        ADRES ← KULLANICIDAN_AL("Teslimat adresini giriniz:")
        AGIRLIK ← SEPET_AGIRLIGI(SEPET)
        KARGO_LISTESI ← KARGO_SECENEKLERI_GETIR(ADRES, AGIRLIK)
        
        KARGO_SEC ← KULLANICI_SEC(KARGO_LISTESI)
        KARGO_UCRETI ← KARGO_HESAPLA(KARGO_SEC, AGIRLIK, ADRES)
        
        TOPLAM_TUTAR ← SEPET_TOPLAMI(SEPET) + KARGO_UCRETI
        MESAJ_GÖSTER("Toplam tutar: " + TOPLAM_TUTAR)
    BITIR KARGO_HESAPLAMA


    // 6. ÖDEME AŞAMASI
    BASLA ODEME_ISLEMI
        ODEME_YONTEMI ← KULLANICIDAN_AL("Ödeme yöntemi seçiniz (Kredi kartı / Havale / Dijital Cüzdan):")
        
        EGER ODEME_YONTEMI = "Kredi kartı"
            ISE KART_BILGISI ← KART_BILGISI_AL()
        BITIR EGER
        
        ODEME_SONUCU ← ODEME_YAP(ODEME_YONTEMI, TOPLAM_TUTAR)
        
        EGER ODEME_SONUCU = BASARILI
            ISE
                SIPARIS_OLUSTUR(SEPET, ADRES, ODEME_YONTEMI)
                STOK_GUNCELLE(SEPET)
                FATURA_OLUSTUR()
                MESAJ_GÖSTER("Sipariş başarıyla tamamlandı!")
            DEGILSE
                MESAJ_GÖSTER("Ödeme başarısız. Lütfen tekrar deneyin.")
        BITIR EGER
    BITIR ODEME_ISLEMI


    // 7. SIPARIS SONRASI
    BASLA SIPARIS_SURECI
        KARGO_TAKIP_NUMARASI ← KARGO_GONDER()
        MESAJ_GÖSTER("Kargo takip numaranız: " + KARGO_TAKIP_NUMARASI)
        
        SIPARIS_DURUMU ← "Hazırlanıyor"
        SIPARIS_DURUMUNU_GUNCELLE(SIPARIS_DURUMU)
    BITIR SIPARIS_SURECI

BITIR SISTEM
