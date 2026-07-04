# Senaryo: Soliner Bilgi Teknolojileri — Stajyer Linux Sistem Görevi

## Hikaye

Bugün **Soliner Bilgi Teknolojileri**'de stajına başladın. IT departmanı sana bir Linux
iş istasyonu teslim etti. Takım lideri **Yasin Kaya**, sana ilk görevin olarak
şirketin yeni geliştireceği **"Envanter Yönetim Sistemi" (kod adı: `eys`)**
projesinin dosya/dizin altyapısını kurmanı ve ekip için kullanıcı/grup/izin
yapılandırmasını hazırlamanı istiyor.

Görevleri sırasıyla, birbirinin üzerine inşa ederek tamamla — adım 8, adım 2'de
oluşturduğun dosyaya bağlı; adım 17, adım 13'te oluşturduğun kullanıcıyı kullanıyor.

> Bu lab'ı bir VM, konteyner veya test amaçlı bir kullanıcı hesabında çalıştır.
> Kullanıcı/grup ve root erişimi görevleri `sudo`/root yetkisi gerektirir.

---

## Bölüm 1 — Proje İskeletini Kurmak

**Durum:** Yasin sana "önce proje dizin yapısını oluştur, sonra içini dolduralım" diyor.

- [x] **Görev 1:** `~/projeler/eys` altında şu alt dizinleri oluştur:
      `src`, `docs`, `config`, `logs`, `backup`, `scripts`
- [x] **Görev 2:** Aşağıdaki boş dosyaları oluştur:
  - `docs/README.md`
  - `docs/mimari.md`
  - `config/app.conf`
  - `logs/app.log`
  - `scripts/deploy.sh`

---

## Bölüm 2 — Dosyalara Kayıt Yazmak

**Durum:** Yasin proje bilgilerini sana e-posta ile gönderdi, bunları ilgili
dosyalara işlemen gerekiyor.

- [x] **Görev 3:** `config/app.conf` dosyasının içine aşağıdaki satırları yaz:
  ```
  APP_NAME=envanter-yonetim-sistemi
  DB_HOST=10.0.4.15
  DB_PORT=5432
  ENV=development
  ```
- [x] **Görev 4:** `logs/app.log` dosyasına, uygulamanın başlangıç kayıtlarıymış gibi
      aşağıdaki satırları **sona ekleyerek** (append) yaz:
  ```
  2026-07-01 09:12:03 INFO  Uygulama baslatildi
  2026-07-01 09:12:05 INFO  Veritabani baglantisi kuruldu
  2026-07-01 09:14:41 ERROR Zaman asimi: DB_HOST 10.0.4.15 yanit vermiyor
  2026-07-01 09:15:02 INFO  Yeniden baglanildi
  ```
- [x] **Görev 5:** `docs/ekip.csv` dosyasını oluştur ve ekip listesini yaz:
  ```
  ad,soyad,rol,departman
  Yasin,Kaya,Takim Lideri,Yazilim
  Elif,Demir,Backend Developer,Yazilim
  Kerem,Aydin,DevOps Muhendisi,Altyapi
  ```
- [x] **Görev 6:** `scripts/deploy.sh` dosyasına basit bir deploy betiği yaz:
  ```bash
  #!/bin/bash
  echo "EYS deploy basladi: $(date)"
  ```

---

## Bölüm 3 — Yedekleme ve Hızlı Erişim (Hard/Soft Link)

**Durum:** Yasin, config dosyasının kazara silinmesine karşı bir yedek, log dosyası
için de "her zaman en güncele işaret eden" bir kısayol istiyor.

- [x] **Görev 7:** `config/app.conf` için `backup/app.conf.hardlink` adında bir
      **hard link** oluştur.
- [x] **Görev 8:** Orijinal `config/app.conf` dosyasını silip, hard link üzerinden
      içeriğin hâlâ erişilebilir olduğunu doğrula. Dosyayı aynı içerikle tekrar
      `config/app.conf` olarak geri oluştur (gerçek ortamda olduğu gibi telafi et).
- [x] **Görev 9:** `logs/app.log` için `logs/latest.log` adında bir **sembolik link**
      oluştur.
- [x] **Görev 10:** `ls -i` ile Görev 7 ve Görev 9'daki linklerin inode numaralarını
      karşılaştır — hard link mi soft link mi, hangisi orijinaliyle aynı inode'u
      paylaşıyor?

---

## Bölüm 4 — İzinleri Sıkılaştırmak

**Durum:** Şirket güvenlik politikası gereği, proje dosyalarının izinleri gevşek
bırakılamaz. Yasin senden aşağıdaki kuralları uygulamanı istiyor.

- [x] **Görev 11:** `config/app.conf` dosyasını sadece **sahibi okuyup yazabilsin**,
      grup sadece okuyabilsin, diğerleri hiç erişemesin şekilde ayarla (hem sembolik
      hem sayısal `chmod` ile bir kez dene).
- [x] **Görev 12:** `scripts/deploy.sh` dosyasını sadece **sahibi çalıştırabilsin**
      hale getir ve çalıştırarak test et.
- [ ] **Görev 13:** `logs/` dizinine **SGID** bitini uygula — ekip ortak çalışacağı
      için bu dizinde oluşturulan yeni dosyalar otomatik olarak doğru gruba ait olsun.
- [ ] **Görev 14:** `backup/` dizinine **sticky bit** uygula — ekip üyeleri birbirinin
      yedek dosyasını silemesin. Bu bitin neden `/tmp` için de kullanıldığını bir
      cümleyle açıkla.

---

## Bölüm 5 — Ekip Arkadaşları için Kullanıcı ve Grup Oluşturmak

**Durum:** IT'den onay geldi — artık ekip arkadaşlarının hesaplarını sen açacaksın.

- [ ] **Görev 15:** İki grup oluştur: `eys-dev` (geliştiriciler) ve `eys-ops` (altyapı ekibi)
- [ ] **Görev 16:** Üç kullanıcı oluştur (ev dizinleriyle birlikte):
  - `elif` — Backend Developer
  - `kerem` — DevOps Mühendisi
  - `yasin` — Takım Lideri
- [ ] **Görev 17:** Grup atamalarını yap:
  - `elif` → `eys-dev` grubuna **ikincil (secondary)** grup olarak eklensin
  - `kerem` → `eys-ops` grubu onun **birincil (primary)** grubu olsun
  - `yasin` → hem `eys-dev` hem `eys-ops` grubuna ikincil üye olsun
- [ ] **Görev 18:** Her kullanıcıya bir şifre belirle ve `chage` ile ilk girişte
      şifre değiştirmeye zorla.

---

## Bölüm 6 — Ortak Proje Dosyasına Erişim Vermek

**Durum:** Artık ekip `eys` projesi üzerinde birlikte çalışacak. Yasin senden rol
bazlı erişim ayarlaması istiyor: geliştiriciler kodda, ops ekibi loglarda çalışacak.

- [ ] **Görev 19:** `~/projeler/eys` dizininin grup sahipliğini `eys-dev` yap ve
      **SGID** bitini uygula (Bölüm 4'te tek bir dizine yaptığını artık proje
      köküne de uygulamış oluyorsun).
- [ ] **Görev 20:** `src/` dizinini `elif` ve `yasin` okuyup yazabilsin (grup
      izniyle), `kerem` hiç erişemesin şekilde ayarla.
- [ ] **Görev 21:** `logs/` dizinine `kerem` ve `yasin` okuyup yazabilsin,
      `elif` sadece okuyabilsin şekilde grup/izin ayarla (ipucu: `eys-ops`
      grubunu burada kullanman gerekebilir).
- [ ] **Görev 22:** `elif` kullanıcısı olarak `src/` içine bir test dosyası oluştur
      ve SGID sayesinde dosyanın otomatik olarak `eys-dev` grubuna ait olduğunu
      doğrula.

---

## Bölüm 7 — Yetkiler, Kaynak Limitleri ve Root Erişimi

**Durum:** Son olarak Yasin, takım liderliği yetkisi ve sistemin güvenliği için
birkaç idari ayar istiyor.

- [ ] **Görev 23:** `yasin`'e sudo yetkisi ver, ancak sadece `eys` servisini
      yeniden başlatma komutunu (örn. `systemctl restart eys`) şifresiz
      çalıştırabilsin şekilde bir `sudoers.d` dosyası ile sınırla.
- [ ] **Görev 24:** `kerem`'in deploy scriptleri çok fazla process açtığı için sık
      sık sistemi zorluyor. `kerem` için `/etc/security/limits.conf` üzerinden
      kalıcı bir **max process (nproc)** ve **max açık dosya (nofile)** limiti
      tanımla.
- [ ] **Görev 25:** Şirket güvenlik politikası gereği root hesabına **doğrudan
      giriş**i kapat (SSH `PermitRootLogin no` ve/veya root şifresini kilitle),
      sadece `sudo` üzerinden erişim mümkün olsun.
- [ ] **Görev 26:** `su -` ile root'a geçiş yapmakla `sudo <komut>` ile tek bir
      komutu root olarak çalıştırmak arasındaki farkı bir örnekle test et.

---

## Bölüm 8 — Teslimat: Günlük Rapor

**Durum:** Gün sonunda Yasin senden yaptıklarını özetleyen bir rapor istiyor.

- [ ] **Görev 27:** `~/projeler/eys/docs/gunluk-rapor.md` dosyasını oluştur ve
      içine şu komutların çıktısını yapıştır:
  - `ls -la ~/projeler/eys` (dizin yapısı ve izinler)
  - `ls -li config/app.conf backup/app.conf.hardlink` (hard link inode kanıtı)
  - `ls -l logs/latest.log` (soft link kanıtı)
  - `getent group eys-dev eys-ops` (grup üyelikleri)
  - `id elif`, `id kerem`, `id yasin` (kullanıcı/grup bilgisi)
  - `sudo -l -U yasin` (sudo yetkisi kanıtı)
- [ ] **Görev 28:** Rapora 2-3 cümlelik bir özet ekle: bugün neler kurdun, hangi
      güvenlik kararlarını neden aldın (örn. neden SGID kullandın, neden root
      girişini kapattın).

---

## Doğrulama Tablosu

| Kontrol | Doğrulama komutu |
|---|---|
| Hard link aynı inode'u paylaşıyor | `ls -i config/app.conf backup/app.conf.hardlink` |
| Soft link doğru hedefi gösteriyor | `ls -l logs/latest.log` |
| Dosya izinleri doğru | `stat -c '%a %n' config/app.conf` |
| SGID/sticky bit uygulanmış | `ls -ld logs backup ~/projeler/eys` (izinlerde `s`/`t`) |
| Grup üyelikleri doğru | `id elif kerem yasin` |
| Rol bazlı erişim çalışıyor | `sudo -u elif touch src/test.txt && ls -l src/test.txt` |
| sudo kısıtlaması doğru | `sudo -l -U yasin` |
| Kaynak limiti uygulanmış | `su - kerem -c 'ulimit -a'` |
| Root girişi kapalı | `sshd -T \| grep -i permitrootlogin` |

---

## Temizlik (Cleanup)

Lab bitince ortamı sıfırla:
- [ ] `~/projeler/eys` dizin ağacını sil
- [ ] `elif`, `kerem`, `yasin` kullanıcılarını `-r` ile (ev dizinleriyle) sil
- [ ] `eys-dev`, `eys-ops` gruplarını sil
- [ ] Eklediğin `/etc/sudoers.d/` dosyasını kaldır
- [ ] `limits.conf` ve SSH ayarlarındaki değişiklikleri geri al (kalıcı makine ise)
