BAŞLA

SİSTEM_DURUMU ← "AKTİF"
ALARM_DURUMU ← "KAPALI"

FONKSİYON Sensörleri_Oku():
    hareket ← HareketSensörü_Oku()
    kapı ← KapıSensörü_Oku()
    duman ← DumanSensörü_Oku()
    su_basması ← SuSensörü_Oku()
    DÖN {hareket, kapı, duman, su_basması}
SON

FONKSİYON Tehdit_Algıla(sensör_verileri):
    EĞER sensör_verileri.duman = TRUE İSE
        DÖN "YÜKSEK"
    EĞER sensör_verileri.hareket = TRUE VE evde_kimse_yok = TRUE İSE
        DÖN "ORTA"
    EĞER sensör_verileri.kapı = ZORLA_AÇILDI İSE
        DÖN "YÜKSEK"
    EĞER sensör_verileri.su_basması = TRUE İSE
        DÖN "ORTA"
    DEĞİLSE
        DÖN "DÜŞÜK"
SON

FONKSİYON Alarm_Ver(tehdit):
    EĞER tehdit = "YÜKSEK" İSE
        ALARM_DURUMU ← "AÇIK"
        Siren_AktifEt()
        Işık_FlaşEt()
        Bildirim_Gönder("Ciddi tehdit algılandı! Yetkililer bilgilendirildi.")
    EĞER tehdit = "ORTA" İSE
        Bildirim_Gönder("Şüpheli durum algılandı, lütfen kontrol edin.")
    EĞER tehdit = "DÜŞÜK" İSE
        // Hiçbir işlem yapılmaz
SON

FONKSİYON Alarm_Sıfırla():
    ALARM_DURUMU ← "KAPALI"
    Siren_Kapat()
    Işık_Normal()
    Bildirim_Gönder("Alarm sıfırlandı.")
SON

// === ANA DÖNGÜ ===
WHILE SİSTEM_DURUMU = "AKTİF" DO
    sensör_verileri ← Sensörleri_Oku()
    tehdit ← Tehdit_Algıla(sensör_verileri)
    Alarm_Ver(tehdit)

    EĞER ALARM_DURUMU = "AÇIK" İSE
        EĞER Kullanıcı_AlarmSıfırlamaKomutu() = TRUE İSE
            Alarm_Sıfırla()
        DEĞİLSE
            // Alarm açık kalır, döngü devam eder
        SON
    SON

    Bekle(1 saniye) // CPU tasarrufu için kısa bekleme
SON

BİTİR
