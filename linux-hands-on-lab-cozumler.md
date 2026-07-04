# Senaryo: Soliner Bilgi Teknolojileri — Stajyer Linux Sistem Görevi (ÇÖZÜMLER)

## Hikaye

Bugün **Soliner Bilgi Teknolojileri**'de stajına başladın. IT departmanı sana bir Linux
iş istasyonu teslim etti. Takım lideri **Yasin Kaya**, sana ilk görevin olarak
şirketin yeni geliştireceği **"Envanter Yönetim Sistemi" (kod adı: `eys`)**
projesinin dosya/dizin altyapısını kurmanı ve ekip için kullanıcı/grup/izin
yapılandırmasını hazırlamanı istiyor.
---

## Bölüm 1 — Proje İskeletini Kurmak

**Durum:** Yasin sana "önce proje dizin yapısını oluştur, sonra içini dolduralım" diyor.

- [x] **Görev 1:** `~/projeler/eys` altında şu alt dizinleri oluştur:
      `src`, `docs`, `config`, `logs`, `backup`, `scripts`

### ✅ Çözüm
```bash
mkdir -p ~/projeler/eys/{src,docs,config,logs,backup,scripts}
```
**Açıklama:**
- `mkdir` = "make directory", yeni dizin oluşturur.
- `-p` bayrağı ("parents") iki iş yapar: (1) yolun içindeki eksik üst dizinleri de otomatik oluşturur (örneğin `~/projeler` yoksa onu da oluşturur), (2) dizin zaten varsa hata vermez, sessizce geçer. `-p` olmadan `~/projeler` yoksa komut hata verirdi.
- `{src,docs,config,logs,backup,scripts}` bir **brace expansion** (küme genişletme) — shell bunu otomatik olarak `~/projeler/eys/src`, `~/projeler/eys/docs`, ... şeklinde 6 ayrı yola genişletir, tek komutla hepsini oluşturursun.

Kontrol etmek için:
```bash
ls -R ~/projeler/eys
```

---

- [x] **Görev 2:** Aşağıdaki boş dosyaları oluştur:
  - `docs/README.md`
  - `docs/mimari.md`
  - `config/app.conf`
  - `logs/app.log`
  - `scripts/deploy.sh`

### ✅ Çözüm
```bash
cd ~/projeler/eys
touch docs/README.md docs/mimari.md config/app.conf logs/app.log scripts/deploy.sh
```
**Açıklama:**
- `cd` ile önce proje köküne geçiyoruz ki yolları kısa yazabilelim.
- `touch` normalde bir dosyanın "değiştirilme zamanını" günceller; ama dosya **yoksa**, içi boş yeni bir dosya oluşturur. Bu yüzden boş dosya oluşturmak için en yaygın kullanılan komuttur.
- Tek `touch` komutuna birden fazla dosya yolu vererek hepsini tek seferde oluşturabilirsin.

Kontrol:
```bash
ls -la docs config logs scripts
```

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

### ✅ Çözüm
```bash
cat > config/app.conf << 'EOF'
APP_NAME=envanter-yonetim-sistemi
DB_HOST=10.0.4.15
DB_PORT=5432
ENV=development
EOF
```
**Açıklama:**
- `>` operatörü **overwrite** (üzerine yazma) yönlendirmesidir — dosya varsa içeriğini tamamen siler, yenisini yazar; yoksa oluşturur.
- `cat > dosya << 'EOF' ... EOF` yapısına **heredoc** denir. Çok satırlı metni tek komutla, satır satır `echo` yazmadan bir dosyaya basmanın en pratik yoludur. `EOF` (istediğin herhangi bir kelime olabilir) metnin nerede bittiğini belirtir. Tırnak içine alınan `'EOF'` shell'in `$DB_HOST` gibi ifadeleri değişken sanıp genişletmesini engeller (literal metin olarak yazılır).
- Alternatif (tek satırlık ayarlar için): `echo "APP_NAME=envanter-yonetim-sistemi" > config/app.conf` sonra her satır için `echo "..." >> config/app.conf` (aşağıda görev 4'te `>>` açıklanıyor).

Kontrol:
```bash
cat config/app.conf
```

---

- [x] **Görev 4:** `logs/app.log` dosyasına, uygulamanın başlangıç kayıtlarıymış gibi
      aşağıdaki satırları **sona ekleyerek** (append) yaz:
  ```
  2026-07-01 09:12:03 INFO  Uygulama baslatildi
  2026-07-01 09:12:05 INFO  Veritabani baglantisi kuruldu
  2026-07-01 09:14:41 ERROR Zaman asimi: DB_HOST 10.0.4.15 yanit vermiyor
  2026-07-01 09:15:02 INFO  Yeniden baglanildi
  ```

### ✅ Çözüm
```bash
cat >> logs/app.log << 'EOF'
2026-07-01 09:12:03 INFO  Uygulama baslatildi
2026-07-01 09:12:05 INFO  Veritabani baglantisi kuruldu
2026-07-01 09:14:41 ERROR Zaman asimi: DB_HOST 10.0.4.15 yanit vermiyor
2026-07-01 09:15:02 INFO  Yeniden baglanildi
EOF
```
**Açıklama:**
- `>>` operatörü **append** (sona ekleme) yönlendirmesidir. `>`'den farkı: dosyanın mevcut içeriğini SİLMEZ, sadece yeni satırları en sona ekler. Log dosyaları için doğru yöntem her zaman `>>`'dir — çünkü `>` kullanırsan önceki tüm log kayıtlarını kaybedersin.

Kontrol:
```bash
cat logs/app.log
```

---

- [x] **Görev 5:** `docs/ekip.csv` dosyasını oluştur ve ekip listesini yaz:
  ```
  ad,soyad,rol,departman
  Yasin,Kaya,Takim Lideri,Yazilim
  Elif,Demir,Backend Developer,Yazilim
  Kerem,Aydin,DevOps Muhendisi,Altyapi
  ```

### ✅ Çözüm
```bash
cat > docs/ekip.csv << 'EOF'
ad,soyad,rol,departman
Yasin,Kaya,Takim Lideri,Yazilim
Elif,Demir,Backend Developer,Yazilim
Kerem,Aydin,DevOps Muhendisi,Altyapi
EOF
```
**Açıklama:**
- Burada dosya henüz yoktu, `>` kullanmak sorun değil çünkü zaten oluşturuyoruz (üzerine yazacağı bir şey yok). Yine de alışkanlık olarak: dosya **yeni oluşturuluyorsa** `>`, **var olan dosyaya ekleme yapılıyorsa** `>>` kullan.
- `.csv` (comma separated values) düz metin dosyasıdır, virgülle ayrılmış sütunlardan oluşur; `cat`, `less`, veya `column -s, -t docs/ekip.csv` gibi komutlarla tablo gibi görebilirsin.

Kontrol:
```bash
cat docs/ekip.csv
column -s, -t docs/ekip.csv
```

---

- [x] **Görev 6:** `scripts/deploy.sh` dosyasına basit bir deploy betiği yaz:
  ```bash
  #!/bin/bash
  echo "EYS deploy basladi: $(date)"
  ```

### ✅ Çözüm
```bash
cat > scripts/deploy.sh << 'EOF'
#!/bin/bash
echo "EYS deploy basladi: $(date)"
EOF
```
**Açıklama:**
- İlk satırdaki `#!/bin/bash` bir **shebang**'tır. Dosyayı doğrudan çalıştırdığında (`./deploy.sh`) sistemin bu betiği hangi programla (burada `/bin/bash`) yorumlayıp çalıştıracağını söyler.
- `$(date)` bir **command substitution**'dır — parantez içindeki komutun (`date`) çıktısı, çalıştığı anda o noktaya yerleştirilir. Yani script her çalıştığında güncel tarih/saat basılır.
- Bu dosyanın içeriği henüz **çalıştırılabilir** değildir (izin eksik) — bunu Görev 12'de `chmod +x` ile çözeceğiz.

Kontrol:
```bash
cat scripts/deploy.sh
```

---

## Bölüm 3 — Yedekleme ve Hızlı Erişim (Hard/Soft Link)

**Durum:** Yasin, config dosyasının kazara silinmesine karşı bir yedek, log dosyası
için de "her zaman en güncele işaret eden" bir kısayol istiyor.

- [x] **Görev 7:** `config/app.conf` için `backup/app.conf.hardlink` adında bir
      **hard link** oluştur.

### ✅ Çözüm
```bash
ln config/app.conf backup/app.conf.hardlink
```
**Açıklama:**
- `ln kaynak hedef` (bayraksız) bir **hard link** oluşturur. Hard link, aynı dosyanın (aynı **inode**'un — diskteki gerçek veri bloğuna işaret eden kayıt) ikinci bir "ismidir". İki dosya değil, tek bir dosyaya iki farklı isimden erişim vardır.
- Sonuç: `config/app.conf` içeriğini değiştirirsen, `backup/app.conf.hardlink` üzerinden okuduğunda da değişikliği görürsün (aynı veriye bakıyorlar).

Kontrol:
```bash
ls -li config/app.conf backup/app.conf.hardlink
```
(İkisinin de en soldaki inode numarası aynı çıkmalı.)

---

- [x] **Görev 8:** Orijinal `config/app.conf` dosyasını silip, hard link üzerinden
      içeriğin hâlâ erişilebilir olduğunu doğrula. Dosyayı aynı içerikle tekrar
      `config/app.conf` olarak geri oluştur (gerçek ortamda olduğu gibi telafi et).

### ✅ Çözüm
```bash
# 1) Orijinali sil
rm config/app.conf

# 2) İçerik hâlâ erişilebilir mi kontrol et (hardlink üzerinden)
cat backup/app.conf.hardlink

# 3) Dosyayı aynı içerikle geri oluştur
cat > config/app.conf << 'EOF'
APP_NAME=envanter-yonetim-sistemi
DB_HOST=10.0.4.15
DB_PORT=5432
ENV=development
EOF
```
**Açıklama:**
- `rm` (remove) bir dosyayı siler. Ama hard link'lerde `rm` aslında veriyi silmez, sadece o **isim bağını** (link'i) kaldırır. Disk üzerindeki gerçek veri, inode'a en az bir isim (link) bağlı kaldığı sürece silinmez. `backup/app.conf.hardlink` hâlâ aynı inode'a bağlı olduğundan içerik korunur — bu yüzden `cat backup/app.conf.hardlink` çalışmaya devam eder.
- Şimdi yeniden `config/app.conf` oluşturduğumuzda, bu **yeni bir inode** ile yeni bir dosyadır — artık `backup/app.conf.hardlink` ile aralarında hard link bağı yoktur (isimleri aynı içeriğe sahip ama farklı inode'lardır). İstersen `ls -li` ile kontrol edip görebilirsin.

Kontrol:
```bash
ls -li config/app.conf backup/app.conf.hardlink
```
(Artık inode numaraları FARKLI olmalı çünkü config/app.conf yeni bir dosya.)

---

- [x] **Görev 9:** `logs/app.log` için `logs/latest.log` adında bir **sembolik link**
      oluştur.

### ✅ Çözüm
```bash
ln -s app.log logs/latest.log
```
**Açıklama:**
- `-s` bayrağı **symbolic (soft) link** oluşturur. Hard link'ten farklı olarak bu, ayrı bir inode'a sahip, sadece hedef dosyanın **yolunu** (isim/path) içeren küçük özel bir dosyadır — bir kısayol/pointer gibi düşünebilirsin (Windows'taki "shortcut" mantığına benzer).
- Kaynağı göreli (relative) yazdık (`app.log`, tam yol değil) çünkü hedefle link aynı dizindeler (`logs/`); bu, dizin taşınsa bile linkin çalışmasını sağlar.
- Orijinal dosya (`app.log`) silinirse, symbolic link **kırık (broken)** kalır — içine bakmaya çalıştığında hata alırsın. Hard link'te böyle bir sorun olmaz.

Kontrol:
```bash
ls -l logs/latest.log
cat logs/latest.log
```

---

- [x] **Görev 10:** `ls -i` ile Görev 7 ve Görev 9'daki linklerin inode numaralarını
      karşılaştır — hard link mi soft link mi, hangisi orijinaliyle aynı inode'u
      paylaşıyor?

### ✅ Çözüm
```bash
ls -i backup/app.conf.hardlink
ls -i logs/app.log logs/latest.log
```
**Açıklama / Beklenen sonuç:**
- `ls -i` her dosyanın **inode numarasını** en solda gösterir.
- `backup/app.conf.hardlink` (Görev 8'den sonraki `config/app.conf` ile artık aynı değil, çünkü dosya yeniden oluşturuldu) kendi ayrı inode'unu taşır.
- `logs/app.log` ve `logs/latest.log` **farklı** inode numaralarına sahip olacaktır, çünkü `latest.log` bir **symbolic link** — kendi inode'u var, ama içeriği değil sadece `app.log` yoluna işaret eden bir "adres" tutuyor.
- Özetle: **Hard link** = aynı inode'u paylaşır (gerçek dosyayla birebir aynı veri). **Soft link** = kendi inode'una sahip ayrı bir dosyadır, sadece hedefin yolunu tutar.

---

## Bölüm 4 — İzinleri Sıkılaştırmak

**Durum:** Şirket güvenlik politikası gereği, proje dosyalarının izinleri gevşek
bırakılamaz. Yasin senden aşağıdaki kuralları uygulamanı istiyor.

- [x] **Görev 11:** `config/app.conf` dosyasını sadece **sahibi okuyup yazabilsin**,
      grup sadece okuyabilsin, diğerleri hiç erişemesin şekilde ayarla (hem sembolik
      hem sayısal `chmod` ile bir kez dene).

### ✅ Çözüm
```bash
# Sembolik (harf) yöntemle
chmod u=rw,g=r,o= config/app.conf

# Sayısal (octal) yöntemle — aynı sonucu verir
chmod 640 config/app.conf
```
**Açıklama:**
- Linux'ta her dosyanın 3 izin grubu vardır: **u**ser (sahip), **g**roup (grup), **o**ther (diğerleri). Her grup için 3 hak vardır: **r**ead (okuma), **w**rite (yazma), **x**execute (çalıştırma).
- Sembolik yöntemde `u=rw` "sahip sadece okuyup yazabilsin", `g=r` "grup sadece okusun", `o=` "diğerleri hiçbir hakka sahip olmasın" demektir.
- Sayısal (octal) yöntemde her hak bir bit değeri taşır: `r=4, w=2, x=1`. Bunları toplayarak tek rakamla ifade edersin:
  - Sahip (owner): rw = 4+2 = **6**
  - Grup: r = **4**
  - Diğerleri: hiçbiri = **0**
  - → `640`
- `640` şu demektir: `rw-r-----` (sahip okur-yazar, grup sadece okur, başkası hiçbir şey yapamaz).

Kontrol:
```bash
ls -l config/app.conf
stat -c '%a %n' config/app.conf
```

---

- [x] **Görev 12:** `scripts/deploy.sh` dosyasını sadece **sahibi çalıştırabilsin**
      hale getir ve çalıştırarak test et.

### ✅ Çözüm
```bash
chmod u+x scripts/deploy.sh
./scripts/deploy.sh
```
**Açıklama:**
- `u+x`: mevcut izinlere dokunmadan, sadece **sahibe (u)** çalıştırma (execute, **+x**) hakkı **ekler**. (`=` yerine `+`/`-` kullanmak, "diğer izinleri koru, sadece bunu ekle/çıkar" anlamına gelir.)
- Bir betiği çalıştırabilmek için hem çalıştırma izni (`x`) hem de içinde geçerli bir shebang (`#!/bin/bash`) olması gerekir.
- `./scripts/deploy.sh`: Başındaki `./` şarttır — çünkü güvenlik nedeniyle Linux, bulunduğun dizini (`.`) varsayılan olarak `PATH` değişkenine (komutların arandığı yer listesi) dahil etmez. `./` ile "bu dizindeki dosyayı çalıştır" demiş oluyorsun.

Kontrol (beklenen çıktı, örnek):
```
EYS deploy basladi: Cts Tem  4 .... 2026
```

---

- [ ] **Görev 13:** `logs/` dizinine **SGID** bitini uygula — ekip ortak çalışacağı
      için bu dizinde oluşturulan yeni dosyalar otomatik olarak doğru gruba ait olsun.

### ✅ Çözüm
```bash
chmod g+s logs/
# Sayısal eşdeğeri (mevcut izinlerin başına 2 eklenir, örn. 2775):
chmod 2775 logs/
```
**Açıklama:**
- **SGID (Set Group ID)** bir dizine uygulandığında şu özel davranışı tetikler: o dizin içinde oluşturulan **yeni** dosya/alt dizinler, oluşturan kullanıcının birincil grubu ne olursa olsun, otomatik olarak **dizinin grup sahipliğini** miras alır.
- Normalde (SGID olmadan) yeni dosyalar, onu oluşturan kullanıcının birincil grubuna ait olur — bu da ekip ortak çalışırken karışıklığa yol açar (herkes farklı gruba sahip dosya üretir). SGID bu sorunu çözer.
- Sembolik gösterimde `chmod g+s` grup bitine "s" (set) ekler. `ls -ld` çıktısında grup çalıştırma (`x`) yerine `s` harfi görünür: `rwxrwsr-x`.
- Sayısal yöntemde SGID, normal 3 haneli izne ek olarak **4. hane** (özel bit hanesi) ile ifade edilir: `chmod 2775` → `2` = SGID, `775` = rwxrwxr-x.

Kontrol:
```bash
ls -ld logs/
stat -c '%A %n' logs/
```
(Çıktıda grup kısmında `s` harfi görülmeli, örn. `drwxrwsr-x`.)

---

- [ ] **Görev 14:** `backup/` dizinine **sticky bit** uygula — ekip üyeleri birbirinin
      yedek dosyasını silemesin. Bu bitin neden `/tmp` için de kullanıldığını bir
      cümleyle açıkla.

### ✅ Çözüm
```bash
chmod +t backup/
# Sayısal eşdeğeri (1 = sticky bit, başa eklenir, örn. 1775):
chmod 1775 backup/
```
**Açıklama:**
- **Sticky bit** bir dizine uygulandığında, o dizin içindeki dosyaları **sadece dosyanın sahibi, dizinin sahibi veya root** silebilir/yeniden adlandırabilir — yazma izni (`w`) olan diğer grup üyeleri bile başkasının dosyasını silemez.
- `ls -ld` çıktısında "diğerleri" (other) bölümünün çalıştırma (`x`) hanesinde `t` harfi görünür: `rwxrwxr-t`.
- Sayısal ifadede sticky bit **1** değeridir, özel bit hanesine eklenir: `chmod 1775` → `1` = sticky, `775` = rwxrwxr-x.

**Neden `/tmp` için de kullanılır (tek cümle):**
`/tmp` tüm kullanıcıların ortak yazabildiği (world-writable) bir dizin olduğu için, sticky bit olmasaydı herhangi bir kullanıcı başka bir kullanıcının `/tmp` içindeki geçici dosyasını silebilirdi — sticky bit her kullanıcının sadece kendi dosyasını silebilmesini garanti ederek bu riski ortadan kaldırır.

Kontrol:
```bash
ls -ld backup/
```
(Çıktıda `drwxrwxr-t` gibi bir satır görülmeli.)

---

## Bölüm 5 — Ekip Arkadaşları için Kullanıcı ve Grup Oluşturmak

**Durum:** IT'den onay geldi — artık ekip arkadaşlarının hesaplarını sen açacaksın.

- [ ] **Görev 15:** İki grup oluştur: `eys-dev` (geliştiriciler) ve `eys-ops` (altyapı ekibi)

### ✅ Çözüm
```bash
sudo groupadd eys-dev
sudo groupadd eys-ops
```
**Açıklama:**
- `groupadd` yeni bir sistem grubu oluşturur; grup bilgileri `/etc/group` dosyasına yazılır.
- `sudo` gerekiyor çünkü kullanıcı/grup yönetimi sistem genelinde etkili olan **root yetkisi** gerektiren bir işlemdir. `sudo` "bir sonraki komutu geçici olarak root yetkisiyle çalıştır" demektir.

Kontrol:
```bash
getent group eys-dev eys-ops
```

---

- [ ] **Görev 16:** Üç kullanıcı oluştur (ev dizinleriyle birlikte):
  - `elif` — Backend Developer
  - `kerem` — DevOps Mühendisi
  - `yasin` — Takım Lideri

### ✅ Çözüm
```bash
sudo useradd -m -c "Elif Demir - Backend Developer" elif
sudo useradd -m -c "Kerem Aydin - DevOps Muhendisi" kerem
sudo useradd -m -c "Yasin Kaya - Takim Lideri" yasin
```
**Açıklama:**
- `useradd` yeni bir sistem kullanıcısı oluşturur.
- `-m` ("make home directory") kullanıcı için `/home/<kullanici_adi>` ev dizinini otomatik oluşturur (bazı dağıtımlarda varsayılan olarak açık olsa da, açıkça belirtmek en garanti yoldur).
- `-c "..."` (comment) kullanıcı hakkında açıklayıcı bir bilgi (genelde tam ad/rol) ekler, `/etc/passwd` dosyasındaki GECOS alanına yazılır — zorunlu değildir ama iyi bir uygulamadır.
- Bu komut varsayılan olarak her kullanıcı için **kendi adıyla aynı isimde birincil bir grup** da oluşturur (örn. `elif` kullanıcısı için `elif` grubu) — bu, `useradd`'ın "user private group" (UPG) davranışıdır.

Kontrol:
```bash
id elif
id kerem
id yasin
ls /home
```

---

- [ ] **Görev 17:** Grup atamalarını yap:
  - `elif` → `eys-dev` grubuna **ikincil (secondary)** grup olarak eklensin
  - `kerem` → `eys-ops` grubu onun **birincil (primary)** grubu olsun
  - `yasin` → hem `eys-dev` hem `eys-ops` grubuna ikincil üye olsun

### ✅ Çözüm
```bash
# elif -> eys-dev ikincil grup
sudo usermod -aG eys-dev elif

# kerem -> eys-ops birincil grup
sudo usermod -g eys-ops kerem

# yasin -> eys-dev VE eys-ops ikincil grup (ikisi birden, virgülle)
sudo usermod -aG eys-dev,eys-ops yasin
```
**Açıklama:**
- `usermod` mevcut bir kullanıcının ayarlarını değiştirir.
- `-aG grup` : **-G** kullanıcının ikincil (supplementary) gruplarını ayarlar; **-a** ("append") ise ÇOK ÖNEMLİDİR — mevcut ikincil grupların üzerine ekleme yapar. `-a` olmadan sadece `-G` kullanırsan, kullanıcının önceki tüm ikincil grup üyelikleri SİLİNİP yerine sadece belirttiğin grup(lar) yazılır. Bu yüzden `usermod -aG` her zaman birlikte kullanılmalıdır.
- `-g grup` (küçük harf) ise kullanıcının **birincil (primary)** grubunu değiştirir — bu, kullanıcının oluşturduğu yeni dosyaların varsayılan grup sahipliğini belirleyen gruptur.
- Birden fazla ikincil grup eklerken virgülle ayırıp aralarında boşluk bırakmadan yazılır: `eys-dev,eys-ops`.

Kontrol:
```bash
id elif    # groups çıktısında eys-dev görünmeli
id kerem   # gid (birincil grup) eys-ops olmalı
id yasin   # groups çıktısında hem eys-dev hem eys-ops görünmeli
getent group eys-dev eys-ops
```

---

- [ ] **Görev 18:** Her kullanıcıya bir şifre belirle ve `chage` ile ilk girişte
      şifre değiştirmeye zorla.

### ✅ Çözüm
```bash
sudo passwd elif
sudo passwd kerem
sudo passwd yasin

# Her biri için ilk girişte şifre değiştirmeye zorla:
sudo chage -d 0 elif
sudo chage -d 0 kerem
sudo chage -d 0 yasin
```
**Açıklama:**
- `passwd <kullanici>` (başında sudo ile) o kullanıcının şifresini interaktif olarak sorar ve ayarlar. `sudo` olmadan çalıştırırsan sadece kendi şifreni değiştirebilirsin, başkasınınkini değiştiremezsin.
- `chage` ("change age") kullanıcı şifresinin yaşam süresi bilgilerini yönetir (`/etc/shadow` üzerinde).
- `-d 0` (last password change date = 0, yani "1 Ocak 1970'ten beri hiç değişmemiş" gibi işaretler) sistemin kullanıcıyı "şifren çok eski, hemen değiştirmelisin" olarak görmesini sağlar — bu da bir sonraki girişte zorunlu şifre değişikliğini tetikler.

Kontrol:
```bash
sudo chage -l elif
sudo chage -l kerem
sudo chage -l yasin
```

---

## Bölüm 6 — Ortak Proje Dosyasına Erişim Vermek

**Durum:** Artık ekip `eys` projesi üzerinde birlikte çalışacak. Yasin senden rol
bazlı erişim ayarlaması istiyor: geliştiriciler kodda, ops ekibi loglarda çalışacak.

- [ ] **Görev 19:** `~/projeler/eys` dizininin grup sahipliğini `eys-dev` yap ve
      **SGID** bitini uygula (Bölüm 4'te tek bir dizine yaptığını artık proje
      köküne de uygulamış oluyorsun).

### ✅ Çözüm
```bash
sudo chgrp -R eys-dev ~/projeler/eys
sudo chmod g+s ~/projeler/eys
```
**Açıklama:**
- `chgrp` ("change group") bir dosya/dizinin **grup sahipliğini** değiştirir (dosyanın kullanıcı sahibini değil, sadece grubunu).
- `-R` ("recursive") işlemi dizinin içindeki tüm alt dizin ve dosyalara da uygular.
- `chmod g+s` ile SGID biti proje köküne de eklenir; böylece bu dizinin altında (doğrudan) oluşturulacak yeni dosya/dizinler `eys-dev` grubuna ait olur. Not: SGID sadece **doğrudan içine** oluşturulan öğeleri etkiler; alt-alt dizinlerin de SGID'li olması için her birine ayrı ayrı uygulanması (ya da `find ... -type d -exec chmod g+s {} \;`) gerekir.

Kontrol:
```bash
ls -ld ~/projeler/eys
```

---

- [ ] **Görev 20:** `src/` dizinini `elif` ve `yasin` okuyup yazabilsin (grup
      izniyle), `kerem` hiç erişemesin şekilde ayarla.

### ✅ Çözüm
```bash
sudo chgrp eys-dev ~/projeler/eys/src
sudo chmod 770 ~/projeler/eys/src
```
**Açıklama:**
- Grup sahipliğini `eys-dev` yapıyoruz çünkü `elif` (ikincil üye) ve `yasin` (ikincil üye) bu gruba dahil, `kerem` ise değil (onun birincil grubu `eys-ops`, `eys-dev`'e üye değil).
- `chmod 770`: sahip = rwx (7), grup (`eys-dev`) = rwx (7), diğerleri = hiçbiri (0). Böylece `elif` ve `yasin` (grup üyesi oldukları için) dizine girip okuyup yazabilir; `kerem` grup üyesi olmadığından ve "diğerleri" izni 0 olduğundan hiç erişemez.
- Dizine `x` (execute) izni vermek şarttır — dizinlerde `x` "içine girebilme / içindeki dosyalara erişebilme" anlamına gelir, `r` sadece dosya isimlerini listelemeyi sağlar.

Kontrol:
```bash
ls -ld ~/projeler/eys/src
sudo -u kerem ls ~/projeler/eys/src   # "Permission denied" vermeli
```

---

- [ ] **Görev 21:** `logs/` dizinine `kerem` ve `yasin` okuyup yazabilsin,
      `elif` sadece okuyabilsin şekilde grup/izin ayarla (ipucu: `eys-ops`
      grubunu burada kullanman gerekebilir).

### ✅ Çözüm
```bash
sudo chgrp eys-ops ~/projeler/eys/logs
sudo chmod 750 ~/projeler/eys/logs

# elif, eys-ops grubuna üye değil; ona salt-okunur erişim vermek için
# "diğerleri" (other) bandını okumaya açıyoruz (elif de "diğerleri" kapsamına girer):
sudo chmod o+rx ~/projeler/eys/logs
```
**Açıklama:**
- `eys-ops` grubuna sahip olan `kerem` (birincil) ve `yasin` (ikincil) grup izniyle (rwx) tam erişebilir.
- `elif` bu gruba üye olmadığından "diğerleri" kategorisine düşer; ona sadece okuma+girebilme (`r-x`, yazma yok) veriyoruz.
- Sonuç izin: `750` + `o+rx` → `755`: sahip=rwx, grup(eys-ops)=r-x... dikkat: burada grup izninde de yazma (`w`) istiyorsak `770` ile başlamalıydık. Doğrusu şudur:

```bash
sudo chgrp eys-ops ~/projeler/eys/logs
sudo chmod 775 ~/projeler/eys/logs
```
Bu şekilde: sahip=rwx(7), grup `eys-ops` (kerem+yasin)=rwx(7) → okur-yazar, diğerleri (elif dahil)=r-x(5) → sadece okur ve dizine girer, yazamaz. Bu, görevin istediği doğru sonuçtur.

Kontrol:
```bash
ls -ld ~/projeler/eys/logs
sudo -u elif touch ~/projeler/eys/logs/deneme.txt   # "Permission denied" vermeli
sudo -u kerem touch ~/projeler/eys/logs/deneme.txt  # başarılı olmalı
```

---

- [ ] **Görev 22:** `elif` kullanıcısı olarak `src/` içine bir test dosyası oluştur
      ve SGID sayesinde dosyanın otomatik olarak `eys-dev` grubuna ait olduğunu
      doğrula.

### ✅ Çözüm
```bash
# Önce src/ dizinine de SGID uygulanmalı (Görev 19'da sadece proje köküne uygulamıştık):
sudo chmod g+s ~/projeler/eys/src

# elif kullanıcısı olarak test dosyası oluştur:
sudo -u elif touch ~/projeler/eys/src/test.txt

# Doğrula:
ls -l ~/projeler/eys/src/test.txt
```
**Açıklama:**
- `sudo -u <kullanici> <komut>`: komutu root olarak değil, belirtilen kullanıcı **kimliğiyle** çalıştırır. Burada `elif` kullanıcısı gibi davranıp dosya oluşturmuş oluyoruz (gerçek ortamda `elif` kendi terminalinden `touch` yapardı).
- `elif`'in birincil grubu `elif`'tir (Görev 16'daki `useradd` varsayılanı, "user private group"), `eys-dev` değil. SGID biti olmasaydı, oluşturduğu `test.txt` dosyası `elif` grubuna ait olurdu.
- SGID sayesinde, `src/` dizini içinde oluşturulan `test.txt`, dizinin grup sahipliğini (`eys-dev`) miras alır — `ls -l` çıktısında dosyanın grup sütununda `eys-dev` yazdığını görürsün. Bu, ekip ortak çalışırken dosyaların hep doğru gruba düşmesini garantiler.

---

## Bölüm 7 — Yetkiler, Kaynak Limitleri ve Root Erişimi

**Durum:** Son olarak Yasin, takım liderliği yetkisi ve sistemin güvenliği için
birkaç idari ayar istiyor.

- [ ] **Görev 23:** `yasin`'e sudo yetkisi ver, ancak sadece `eys` servisini
      yeniden başlatma komutunu (örn. `systemctl restart eys`) şifresiz
      çalıştırabilsin şekilde bir `sudoers.d` dosyası ile sınırla.

### ✅ Çözüm
```bash
sudo visudo -f /etc/sudoers.d/yasin-eys
```
Açılan editörde şu satırı ekle:
```
yasin ALL=(root) NOPASSWD: /usr/bin/systemctl restart eys
```
Kaydet ve çık.
```bash
sudo chmod 440 /etc/sudoers.d/yasin-eys
```
**Açıklama:**
- Sudo yetkilerini doğrudan `/etc/sudoers` dosyasını elle düzenleyerek değil, `/etc/sudoers.d/` klasörüne **ayrı bir dosya** ekleyerek yönetmek en iyi pratiktir — bu sayede ana dosyayı bozma riski azalır ve değişiklikler kolayca izlenip geri alınabilir.
- `visudo` bu dosyaları düzenlemenin **doğru** yoludur çünkü kaydetmeden önce sözdizimi (syntax) hatası olup olmadığını otomatik kontrol eder; hata varsa kaydetmeni engeller. Düz bir editörle (`nano`/`vim`) direkt düzenleyip hatalı bir satır bırakırsan, sistemde `sudo` tamamen çalışmaz hale gelebilir — bu yüzden `-f <dosya>` ile visudo üzerinden düzenlemek şarttır.
- Satırın anlamı: `yasin` kullanıcısı, `ALL` (herhangi bir makineden), `(root)` (root kimliğiyle), `NOPASSWD:` (şifre sormadan) sadece belirtilen tam komutu (`/usr/bin/systemctl restart eys`) çalıştırabilir — başka hiçbir komutu değil. Komutun **tam yolunu** (`which systemctl` ile bulunur) yazmak güvenlik için zorunludur, aksi halde kısıtlama etkisiz kalabilir.
- `chmod 440`: sudoers dosyalarının izinleri güvenlik gereği sadece root'un okuyup yazabildiği, grubun sadece okuyabildiği, başkasının hiç erişemediği şekilde olmalıdır (`sudo` bu izinlere uymayan dosyaları reddeder).

Kontrol:
```bash
sudo -l -U yasin
```

---

- [ ] **Görev 24:** `kerem`'in deploy scriptleri çok fazla process açtığı için sık
      sık sistemi zorluyor. `kerem` için `/etc/security/limits.conf` üzerinden
      kalıcı bir **max process (nproc)** ve **max açık dosya (nofile)** limiti
      tanımla.

### ✅ Çözüm
```bash
sudo tee -a /etc/security/limits.conf << 'EOF'
kerem soft nproc  100
kerem hard nproc  200
kerem soft nofile 1024
kerem hard nofile 2048
EOF
```
**Açıklama:**
- `/etc/security/limits.conf`, PAM (Pluggable Authentication Modules) üzerinden kullanıcı oturumlarına kaynak limitleri (ulimit) uygulayan konfigürasyon dosyasıdır — buraya yazılan kurallar, kullanıcı her yeni oturum açtığında (SSH, `su -`, vb.) otomatik uygulanır.
- **soft limit**: kullanıcının kendi başına (root yetkisi olmadan) `ulimit` ile geçici olarak artırabileceği üst sınır — yani "varsayılan/normal" limittir.
- **hard limit**: soft limitin aşamayacağı **mutlak tavan** — sadece root bu sınırı değiştirebilir.
- `nproc`: kullanıcının aynı anda açabileceği maksimum **process (işlem)** sayısı — sınırsız process açıp sistemi kilitlemesini önler.
- `nofile`: kullanıcının aynı anda açabileceği maksimum **dosya tanıtıcısı (file descriptor)** sayısı — çok fazla dosya/soket açıp sistem kaynaklarını tüketmesini engeller.
- `tee -a` : `sudo` ile bir dosyaya **append** (ekleme) yaparken `sudo echo ... >> dosya` çoğu zaman "Permission denied" hatası verir çünkü yönlendirme (`>>`) shell tarafından `sudo` uygulanmadan ÖNCE, senin kullanıcı yetkinle yapılır. `sudo tee -a dosya` bunun yerine `tee` komutunun kendisini root yetkisiyle çalıştırıp veriyi dosyaya yazmasını sağlar — bu yüzden root yetkisi gereken dosyalara satır eklerken doğru yöntem budur.

Kontrol (kerem oturum açtıktan/yeniden giriş yaptıktan sonra):
```bash
su - kerem -c 'ulimit -a'
```

---

- [ ] **Görev 25:** Şirket güvenlik politikası gereği root hesabına **doğrudan
      giriş**i kapat (SSH `PermitRootLogin no` ve/veya root şifresini kilitle),
      sadece `sudo` üzerinden erişim mümkün olsun.

### ✅ Çözüm
```bash
# 1) SSH üzerinden root girişini kapat
sudo sed -i 's/^#\?PermitRootLogin.*/PermitRootLogin no/' /etc/ssh/sshd_config
sudo systemctl restart sshd

# 2) Ek güvenlik: root şifresini kilitle (konsoldan doğrudan girişi de engeller)
sudo passwd -l root
```
**Açıklama:**
- `PermitRootLogin no` SSH sunucusuna (`sshd`) "kimse doğrudan `root` kullanıcı adıyla SSH ile bağlanamaz" der. `sed -i` komutu, dosyayı açıp elle düzenlemek yerine ilgili satırı otomatik bulup değiştirir (`-i` = "in-place", dosyanın kendisini değiştir).
- Değişikliğin etkili olması için `sshd` servisinin **yeniden başlatılması** (`systemctl restart sshd`) gerekir — servisler konfigürasyon dosyalarını sadece başlangıçta (veya reload/restart anında) okur.
- `passwd -l root` ("lock"): root'un şifre hash'inin başına geçersiz bir karakter ekleyerek şifre ile girişi tamamen imkansız hale getirir (şifreyi silmez, sadece kilitler — `passwd -u root` ile geri açılabilir).
- Bu iki adımdan sonra sisteme erişmek isteyen herkes önce **kendi normal kullanıcısıyla** giriş yapıp, gerektiğinde `sudo` ile yetkilendirilmiş komutları çalıştırmak zorunda kalır — bu da tüm root işlemlerinin loglanabilir/izlenebilir (`sudo` her komutu `/var/log/auth.log` gibi dosyalara kaydeder) olmasını sağlar.

Kontrol:
```bash
sudo sshd -T | grep -i permitrootlogin
sudo passwd -S root
```

---

- [ ] **Görev 26:** `su -` ile root'a geçiş yapmakla `sudo <komut>` ile tek bir
      komutu root olarak çalıştırmak arasındaki farkı bir örnekle test et.

### ✅ Çözüm
```bash
# Yöntem 1: su ile tam oturum aç (root şifresi istenir, kilitliyse çalışmaz)
su -
# ...artık kabuk (shell) tamamen root kimliğinde, istediğin kadar komut çalıştırabilirsin...
whoami   # -> root
exit     # normal kullanıcına geri dön

# Yöntem 2: sudo ile SADECE tek bir komutu root olarak çalıştır
sudo whoami   # -> root (sadece bu komut için)
whoami        # -> tekrar kendi kullanıcın (örn. berat) - oturum root'a geçmedi
```
**Açıklama:**
- `su -` ("switch user", `-` ile birlikte tam bir **login shell** başlatır) kullanıcıyı **tamamen root kimliğine geçirir** — `exit` yazana kadar çalıştırdığın HER komut root yetkisiyle çalışır. Bunun için hedef kullanıcının (root'un) şifresini bilmen gerekir. Root şifresi kilitliyse (Görev 25) bu yöntem çalışmaz.
- `sudo <komut>` ise **sadece o tek komutu** geçici olarak root yetkisiyle çalıştırır; komut bitince otomatik olarak normal kullanıcına geri dönersin. Kendi kullanıcı şifreni girmen yeterlidir (root şifresine hiç ihtiyaç yoktur), ve `sudoers` dosyasında hangi komutları çalıştırabileceğin ayrıca kısıtlanabilir (Görev 23'teki gibi).
- Güvenlik açısından `sudo` tercih edilir çünkü: (1) her komut ayrı ayrı loglanır (kim, ne zaman, hangi komutu çalıştırdı), (2) yetkiler komut bazında kısıtlanabilir, (3) root şifresinin ayrıca paylaşılmasına/bilinmesine gerek kalmaz.

---

## Bölüm 8 — Teslimat: Günlük Rapor

**Durum:** Gün sonunda Yasin senden yaptıklarını özetleyen bir rapor istiyor.

- [ ] **Görev 27:** `~/projeler/eys/docs/gunluk-rapor.md` dosyasını oluştur ve
      içine şu komutların çıktısını yapıştır.

### ✅ Çözüm
```bash
cd ~/projeler/eys

{
  echo "# Günlük Rapor - $(date +%Y-%m-%d)"
  echo
  echo "## Dizin yapısı ve izinler"
  echo '```'
  ls -la ~/projeler/eys
  echo '```'
  echo
  echo "## Hard link inode kanıtı"
  echo '```'
  ls -li config/app.conf backup/app.conf.hardlink
  echo '```'
  echo
  echo "## Soft link kanıtı"
  echo '```'
  ls -l logs/latest.log
  echo '```'
  echo
  echo "## Grup üyelikleri"
  echo '```'
  getent group eys-dev eys-ops
  echo '```'
  echo
  echo "## Kullanıcı/grup bilgisi"
  echo '```'
  id elif
  id kerem
  id yasin
  echo '```'
  echo
  echo "## Sudo yetkisi kanıtı"
  echo '```'
  sudo -l -U yasin
  echo '```'
} > docs/gunluk-rapor.md
```
**Açıklama:**
- `{ komut1; komut2; ... } > dosya`: süslü parantezler bir **komut grubu** oluşturur; içindeki tüm komutların çıktısı tek bir yönlendirme (`>`) ile tek dosyaya toplanır — her komuttan sonra ayrı ayrı `>>` yazmaktan çok daha temizdir.
- `$(date +%Y-%m-%d)`: `date` komutuna format belirterek (`+%Y-%m-%d`) sadece tarihi `YIL-AY-GUN` biçiminde alıyoruz.
- Çıktıları ` ``` ` (markdown kod bloğu) işaretleri arasına almak, raporu Markdown olarak görüntülediğinde komut çıktılarının düzgün, sabit genişlikli (monospace) formatta görünmesini sağlar.

Kontrol:
```bash
cat docs/gunluk-rapor.md
```

---

- [ ] **Görev 28:** Rapora 2-3 cümlelik bir özet ekle: bugün neler kurdun, hangi
      güvenlik kararlarını neden aldın (örn. neden SGID kullandın, neden root
      girişini kapattın).

### ✅ Çözüm
```bash
cat >> docs/gunluk-rapor.md << 'EOF'

## Özet

Bugün EYS projesi için dizin/dosya altyapısını kurdum, ekip üyeleri için
kullanıcı ve grup yapılandırmasını (eys-dev, eys-ops) tamamladım ve rol
bazlı erişim izinlerini ayarladım. `logs/` ve proje köküne SGID biti
uyguladım çünkü ekip ortak çalışırken oluşturulan yeni dosyaların hangi
kullanıcı tarafından yaratıldığından bağımsız olarak doğru gruba ait
olmasını garanti altına almak istedim; bu, manuel grup düzeltmesi
ihtiyacını ortadan kaldırıyor. Root hesabına doğrudan girişi kapattım ve
şifresini kilitledim çünkü tüm yönetici işlemlerinin `sudo` üzerinden,
kullanıcı bazlı ve loglanabilir şekilde yapılmasını sağlamak güvenlik
politikamız gereği zorunlu; ayrıca `yasin` için sadece `eys` servisini
yeniden başlatma yetkisini tanımlayarak en az yetki (least privilege)
ilkesini uyguladım.
EOF
```
**Açıklama:**
- `cat >> dosya << 'EOF' ... EOF` (append heredoc) mevcut raporun **sonuna** yeni bir bölüm ekler, önceki içeriği bozmaz.
- Özet metni, yapılan işlemlerin **nedenini** (SGID → tutarlı grup sahipliği, root kilidi + sudo kısıtlaması → en az yetki ve izlenebilirlik ilkesi) açıklayarak yazıldı; bu, gerçek bir işyerinde rapor yazarken beklenen "ne yaptım + neden yaptım" formatına örnektir.

Kontrol:
```bash
cat docs/gunluk-rapor.md
```

---

## Doğrulama Tablosu

| Kontrol | Doğrulama komutu | Ne gösterir? |
|---|---|---|
| Hard link aynı inode'u paylaşıyor | `ls -i config/app.conf backup/app.conf.hardlink` | Aynı inode numarası = aynı fiziksel dosya |
| Soft link doğru hedefi gösteriyor | `ls -l logs/latest.log` | Satır başında `l` ve `->` ile hedef yol görünür |
| Dosya izinleri doğru | `stat -c '%a %n' config/app.conf` | Octal izin değeri (örn. `640`) |
| SGID/sticky bit uygulanmış | `ls -ld logs backup ~/projeler/eys` | Grup `x` yerine `s`, diğer `x` yerine `t` |
| Grup üyelikleri doğru | `id elif kerem yasin` | `uid`, `gid` (birincil), `groups` (tüm gruplar) |
| Rol bazlı erişim çalışıyor | `sudo -u elif touch src/test.txt && ls -l src/test.txt` | Dosya oluşur ve grubu `eys-dev` olur |
| sudo kısıtlaması doğru | `sudo -l -U yasin` | Sadece izin verilen komut listelenir |
| Kaynak limiti uygulanmış | `su - kerem -c 'ulimit -a'` | `max user processes` ve `open files` satırları |
| Root girişi kapalı | `sshd -T \| grep -i permitrootlogin` | `permitrootlogin no` çıktısı |

---

## Temizlik (Cleanup)

Lab bitince ortamı sıfırla:

### ✅ Çözüm
```bash
# 1) Proje dizin ağacını sil
rm -rf ~/projeler/eys

# 2) Kullanıcıları ev dizinleriyle birlikte sil
sudo userdel -r elif
sudo userdel -r kerem
sudo userdel -r yasin

# 3) Grupları sil
sudo groupdel eys-dev
sudo groupdel eys-ops

# 4) sudoers.d dosyasını kaldır
sudo rm -f /etc/sudoers.d/yasin-eys

# 5) limits.conf değişikliklerini geri al (eklediğin 4 satırı elle sil)
sudo sed -i '/^kerem /d' /etc/security/limits.conf

# 6) SSH ayarını geri al (kalıcı makinede politika gereği tekrar açman gerekiyorsa)
sudo sed -i 's/^PermitRootLogin no/PermitRootLogin yes/' /etc/ssh/sshd_config
sudo systemctl restart sshd
sudo passwd -u root   # root şifresinin kilidini aç (gerekiyorsa)
```
**Açıklama:**
- `rm -rf`: `-r` (recursive) dizin içindeki her şeyi, `-f` (force) onay sormadan siler — **çok dikkatli kullanılmalı**, geri alınamaz.
- `userdel -r`: kullanıcıyı `/etc/passwd`'den siler VE `-r` ile ev dizinini (`/home/kullanici`) de birlikte kaldırır. `-r` olmadan sadece kullanıcı hesabı silinir, dosyaları diskte kalır (sahipsiz/orphan dosya olarak).
- `groupdel`: bir grubu sistemden siler — grubun hâlâ birincil grup olarak atanmış kullanıcıları varsa hata verir (önce o kullanıcıların birincil grubunu değiştirmen gerekir).
- `sed -i '/^kerem /d' dosya`: `kerem` ile başlayan satırları dosyadan **siler** (`d` = delete). Bu, elle eklediğimiz 4 limit satırını temizler.
- Son iki satır sadece **kalıcı** (gerçek/production) bir makinedeysen ve lab öncesi duruma dönmen gerekiyorsa kullanılır; bir test VM'i tamamen siliyorsan bu adımlara gerek yoktur.

---

## Ek: Sık Kullanılan Bayrak/Kavramların Hızlı Özeti

| Komut/Bayrak | Anlamı |
|---|---|
| `mkdir -p` | Eksik üst dizinleri de oluştur, dizin varsa hata verme |
| `touch` | Boş dosya oluştur / var olan dosyanın zaman damgasını güncelle |
| `>` | Dosyanın üzerine yaz (üzerine yazma) |
| `>>` | Dosyanın sonuna ekle (append) |
| `ln hedef link` | Hard link oluştur (aynı inode) |
| `ln -s hedef link` | Symbolic (soft) link oluştur (ayrı inode, yol saklar) |
| `chmod u/g/o +/-/= r/w/x` | Sembolik izin değiştirme |
| `chmod NNN` | Sayısal (octal) izin değiştirme (r=4,w=2,x=1) |
| `chmod g+s` | SGID: dizindeki yeni dosyalar dizinin grubunu miras alır |
| `chmod +t` | Sticky bit: dosyayı sadece sahibi silebilir |
| `chown` | Dosya/dizin sahibini (user) değiştirir |
| `chgrp` | Dosya/dizin grubunu değiştirir |
| `useradd -m -c` | Yeni kullanıcı + ev dizini + açıklama |
| `usermod -aG grup` | Kullanıcıyı ikincil gruba EKLE (mevcutları koruyarak) |
| `usermod -g grup` | Kullanıcının birincil grubunu DEĞİŞTİR |
| `passwd` / `passwd -l` | Şifre belirle / şifreyi kilitle |
| `chage -d 0` | Sonraki girişte şifre değişimini zorunlu kıl |
| `visudo -f dosya` | sudoers dosyasını güvenli şekilde düzenle (syntax kontrolü yapar) |
| `sudo -l -U kullanici` | Bir kullanıcının sudo yetkilerini listele |
| `limits.conf (soft/hard, nproc/nofile)` | Kullanıcı başına kaynak (process/dosya) limiti |
| `su -` | Tam oturum ile başka kullanıcıya (root'a) geçiş |
| `sudo komut` | Sadece tek bir komutu root yetkisiyle çalıştır |
