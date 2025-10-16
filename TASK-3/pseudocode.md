
BAŞLA

  // --- 1. KİMLİK DOĞRULAMA ---
  GİRİŞ: TCKimlikNo, Şifre
  eğer KimlikBilgileriDoğruMu(TCKimlikNo, Şifre) ise
      YAZDIR "Giriş başarılı."
  değilse
      YAZDIR "Kimlik doğrulama başarısız. Lütfen tekrar deneyin."
      BİTİR

  // --- 2. ANA MENÜ ---
  TEKRAR
      YAZDIR "----- ANA MENÜ -----"
      YAZDIR "1. Randevu İşlemleri"
      YAZDIR "2. Tahlil Sonucu Görüntüleme"
      YAZDIR "3. Çıkış"
      GİRİŞ: Secim

      EĞER Secim == 1 ise
          // === RANDEVU MODÜLÜ ===

          // Poliklinik Seçimi
          POLIKLINIKLER ← PoliklinikListesiniGetir()
          YAZDIR POLIKLINIKLER
          GİRİŞ: SecilenPoliklinik

          // Doktor Seçimi
          DOKTORLAR ← DoktorListesiniGetir(SecilenPoliklinik)
          YAZDIR DOKTORLAR
          GİRİŞ: SecilenDoktor

          // Uygun Saat Seçimi
          SAATLER ← UygunSaatleriGetir(SecilenDoktor)
          eğer SAATLER boş ise
              YAZDIR "Uygun saat bulunamadı. Lütfen başka bir gün seçin."
          aksi halde
              YAZDIR SAATLER
              GİRİŞ: SecilenSaat

              // Randevu Onayı
              YAZDIR "Randevunuz: " + SecilenPoliklinik + " - " + SecilenDoktor + " - " + SecilenSaat
              GİRİŞ: Onay (E/H)
              eğer Onay == 'E' ise
                  RandevuKaydet(TCKimlikNo, SecilenPoliklinik, SecilenDoktor, SecilenSaat)
                  TelefonNo ← TelefonNumarasiniGetir(TCKimlikNo)
                  SMSGonder(TelefonNo, "Randevunuz onaylandı: " + SecilenSaat)
                  YAZDIR "Randevu başarıyla oluşturuldu ve SMS gönderildi."
              değilse
                  YAZDIR "Randevu iptal edildi."

      EĞER Secim == 2 ise
          // === TAHLİL SONUCU MODÜLÜ ===

          TAHLILLER ← TahlilListesiniGetir(TCKimlikNo)
          eğer TAHLILLER boş ise
              YAZDIR "Henüz kayıtlı bir tahliliniz bulunmamaktadır."
          aksi halde
              YAZDIR "Mevcut tahliller:"
              YAZDIR TAHLILLER
              GİRİŞ: SecilenTahlil

              eğer TahlilSonucuHazirMi(SecilenTahlil) ise
                  SONUC ← TahlilSonucunuGetir(SecilenTahlil)
                  YAZDIR "Tahlil Sonucu:"
                  YAZDIR SONUC

                  GİRİŞ: IndirilsinMi (E/H)
                  eğer IndirilsinMi == 'E' ise
                      PDFIndir(SONUC)
                      YAZDIR "Tahlil sonucu PDF olarak indirildi."
                  değilse
                      YAZDIR "PDF indirme iptal edildi."
              değilse
                  YAZDIR "Tahlil sonucunuz henüz hazır değil. Lütfen daha sonra tekrar kontrol edin."

      EĞER Secim == 3 ise
          YAZDIR "Sistemden çıkılıyor..."
          ÇIK

  // Menüden çıkış koşulu
  TA Kİ Secim == 3 olana kadar

BİTİR
