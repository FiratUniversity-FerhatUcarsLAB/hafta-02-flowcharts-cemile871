BAŞLA

  // 1. Kimlik Doğrulama
  GİRİŞ: TCKimlikNo, Şifre
  eğer KimlikBilgileriDoğruMu(TCKimlikNo, Şifre) ise
      DEVAM ET
  değilse
      YAZDIR "Kimlik doğrulama başarısız. Lütfen tekrar deneyin."
      BİTİR

  // 2. Poliklinik Seçimi
  POLIKLINIKLER ← PoliklinikListesiniGetir()
  YAZDIR POLIKLINIKLER
  GİRİŞ: SecilenPoliklinik

  // 3. Doktor Listesi Görüntüleme
  DOKTORLAR ← DoktorListesiniGetir(SecilenPoliklinik)
  YAZDIR DOKTORLAR
  GİRİŞ: SecilenDoktor

  // 4. Uygun Saatleri Gösterme
  SAATLER ← UygunSaatleriGetir(SecilenDoktor)
  eğer SAATLER boş ise
      YAZDIR "Uygun saat bulunamadı. Lütfen başka bir gün veya doktor seçin."
      BİTİR
  aksi halde
      YAZDIR SAATLER
      GİRİŞ: SecilenSaat

  // 5. Randevu Onaylama
  YAZDIR "Randevunuz: " + SecilenPoliklinik + " - " + SecilenDoktor + " - " + SecilenSaat
  GİRİŞ: Onay (E/H)
  eğer Onay == 'E' ise
      RandevuKaydet(TCKimlikNo, SecilenPoliklinik, SecilenDoktor, SecilenSaat)
      // 6. SMS Gönderme
      TelefonNo ← TelefonNumarasiniGetir(TCKimlikNo)
      SMSGonder(TelefonNo, "Randevunuz onaylandı: " + SecilenSaat)
      YAZDIR "Randevu başarıyla oluşturuldu ve SMS gönderildi."
  değilse
      YAZDIR "Randevu iptal edildi."

BİTİR
