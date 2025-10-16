BAŞLA

    // Sabitler
    GÜNLÜK_LIMIT ← 2000
    KULLANILAN_LIMIT ← 0
    BAKİYE ← 5000
    DOĞRU_PIN ← "1234"
    PIN_HAK ← 3

    // PIN Doğrulama
    DÖNGÜ PIN_HAK > 0 TEKRAR
        YAZ "Lütfen 4 haneli PIN kodunuzu giriniz:"
        OKU GIRILEN_PIN

        EĞER GIRILEN_PIN = DOĞRU_PIN İSE
            ÇIKIŞ DÖNGÜ // Doğru PIN girildi, döngüden çık
        DEĞİLSE
            PIN_HAK ← PIN_HAK - 1
            EĞER PIN_HAK > 0 İSE
                YAZ "Hatalı PIN. Kalan hakkınız: ", PIN_HAK
            DEĞİLSE
                YAZ "3 kez hatalı PIN girildi. Kartınız bloke edildi."
                SON
            BİTİŞ
        BİTİŞ
    BİTİŞ

    // İşlem Döngüsü
    DÖNGÜ DOĞRU TEKRAR

        YAZ "Çekmek istediğiniz tutarı giriniz (20 TL ve katı olmalı):"
        OKU TUTAR

        // Tutar geçerlilik kontrolü
        EĞER TUTAR MOD 20 ≠ 0 İSE
            YAZ "Tutar 20 TL'nin katı olmalıdır."
            DEVAM DÖNGÜ
        BİTİŞ

        // Bakiye kontrolü
        EĞER TUTAR > BAKİYE İSE
            YAZ "Yetersiz bakiye. Mevcut bakiyeniz: ", BAKİYE, " TL"
            DEVAM DÖNGÜ
        BİTİŞ

        // Günlük limit kontrolü
        EĞER (KULLANILAN_LIMIT + TUTAR) > GÜNLÜK_LIMIT İSE
            KALAN_LIMIT ← GÜNLÜK_LIMIT - KULLANILAN_LIMIT
            YAZ "Günlük limit aşılıyor. Kalan günlük limit: ", KALAN_LIMIT, " TL"
            DEVAM DÖNGÜ
        BİTİŞ

        // Para çekme işlemi
        BAKİYE ← BAKİYE - TUTAR
        KULLANILAN_LIMIT ← KULLANILAN_LIMIT + TUTAR
        YAZ "İşlem başarılı. Yeni bakiyeniz: ", BAKİYE, " TL"
        YAZ "Bugün çektiğiniz toplam tutar: ", KULLANILAN_LIMIT, " TL"

        // İşlem tekrar seçeneği
        YAZ "Başka bir işlem yapmak istiyor musunuz? (E/H)"
        OKU CEVAP

        EĞER CEVAP = "H" VEYA CEVAP = "h" İSE
            ÇIKIŞ DÖNGÜ
        BİTİŞ

    BİTİŞ

    YAZ "İyi günler. Kartınızı almayı unutmayın."
SON
