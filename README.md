# Linux Hands On - Soliner Bilgi Teknolojileri

Bu repo, **Soliner Bilgi Teknolojileri** stajyerlik programı kapsamında hazırlanan Linux sistem yönetimi laboratuvar çalışmasını içerir.

## İçerik

- **linux-hands-on-lab (1).md** — Lab senaryosu ve 28 görev talimatı
- **linux-hands-on-lab-cozumler.md** — Tüm görevlerin adım adım çözümleri ve açıklamaları
- **projeler/eys/** — Oluşturulan "Envanter Yönetim Sistemi" (EYS) proje dosya/dizin yapısı

## Proje Yapısı

```
projeler/eys/
├── backup/          # Yedek dosyaları (hard link)
├── config/          # Uygulama konfigürasyonu
├── docs/            # Dokümantasyon ve raporlar
├── logs/            # Uygulama logları (soft link)
├── scripts/         # Deploy betikleri
└── src/             # Kaynak kodu
```

## Konular

- Linux dosya/dizin yönetimi
- Hard link ve sembolik (soft) link
- Dosya izinleri (chmod, SGID, sticky bit)
- Kullanıcı ve grup yönetimi
- Sudo yetkilendirme
- Kaynak limitleri (ulimit)
- SSH güvenlik yapılandırması
