BAŞLA

GİRİŞ:
    GİRİŞ_YAP(kullanıcı_adı, şifre)
    EĞER kimlik_doğrula(kullanıcı_adı, şifre) = DOĞRU İSE
        DEVAM_ET
    DEĞİLSE
        YAZ("Hatalı kullanıcı adı veya şifre.")
        BİTİR

DERS LİSTESİ:
    ders_listesi ← getir_dersler(öğrencinin_bölümü, öğrencinin_dönemi)

DERS SEÇİMİ:
    seçilen_dersler ← BOŞ_LİSTE
    toplam_kredi ← 0

    DÖNGÜ ders İÇİN ders_listesi
        YAZ(ders.kod, ders.ad, ders.kredi, ders.kontenjan, ders.zaman)
    SON_DÖNGÜ

    YAZ("Seçmek istediğiniz ders kodlarını girin (BİTİR yazınca durur):")

    DÖNGÜ
        giriş ← KULLANICI_GİRDİSİ()
        EĞER giriş = "BİTİR" İSE
            ÇIK
        DEĞİLSE
            seçilen_ders ← ders_bul(giriş, ders_listesi)
            EĞER seçilen_ders ≠ YOK İSE
                # --- KONTROLLER ---

                # 1. Kontenjan kontrolü
                EĞER seçilen_ders.kontenjan > 0 İSE

                    # 2. Ön koşul kontrolü
                    EĞER önkoşul_tamam(seçilen_ders, öğrenci) = DOĞRU İSE

                        # 3. Zaman çakışması kontrolü
                        çakışma_var ← YANLIŞ
                        DÖNGÜ d İÇİN seçilen_dersler
                            EĞER zaman_çakışıyor(seçilen_ders, d) = DOĞRU İSE
                                çakışma_var ← DOĞRU
                                ÇIK_DÖNGÜ
                            BİTİR_EĞER
                        SON_DÖNGÜ

                        EĞER çakışma_var = YANLIŞ İSE

                            # 4. Kredi limiti kontrolü
                            EĞER toplam_kredi + seçilen_ders.kredi ≤ kredi_limiti(öğrenci) İSE
                                # Ders eklenebilir
                                seçilen_dersler ← seçilen_dersler + seçilen_ders
                                toplam_kredi ← toplam_kredi + seçilen_ders.kredi
                                seçilen_ders.kontenjan ← seçilen_ders.kontenjan - 1
                                YAZ("Ders eklendi: ", seçilen_ders.ad)
                            DEĞİLSE
                                YAZ("Kredi limiti aşıldı. Ders eklenemedi.")
                            BİTİR_EĞER

                        DEĞİLSE
                            YAZ("Zaman çakışması var. Ders eklenemedi.")
                        BİTİR_EĞER

                    DEĞİLSE
                        YAZ("Ön koşul dersi alınmamış.")
                    BİTİR_EĞER

                DEĞİLSE
                    YAZ("Kontenjan dolu. Ders eklenemedi.")
                BİTİR_EĞER

            DEĞİLSE
                YAZ("Geçersiz ders kodu.")
            BİTİR_EĞER
        BİTİR_EĞER
    SON_DÖNGÜ

ONAYLAMA:
    YAZ("Seçilen dersler:")
    DÖNGÜ d İÇİN seçilen_dersler
        YAZ("-", d.ad, "(", d.kredi, " kredi )")
    SON_DÖNGÜ

    YAZ("Danışman onayına gönderiliyor...")
    EĞER danışman_onayla(öğrenci, seçilen_dersler) = DOĞRU İSE
        YAZ("Ders kaydı başarılı!")
    DEĞİLSE
        YAZ("Danışman onayı reddedildi. Lütfen düzenleyin.")
    BİTİR_EĞER

BİTİR
