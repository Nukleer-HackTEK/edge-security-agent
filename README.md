# Edge Security Agent
  &emsp;LiderAhenk merkezi yönetim sistemi ile uyumlu çalışan bu proje, "Uçta Bilişim (Edge Computing)" mimarisini benimseyen akıllı bir uçbirim ve hibrit telemetri ajanı prototipidir. Mevcut izleme sistemlerinin yarattığı ağ darboğazı ve sunucu G/Ç yükü sorunlarını, veriyi kaynağında işleyerek sunucu yükünü minimize eder.

---

# Problem
  &emsp;Mevcut telemetri sistemleri, uçbirimlerden gelen tüm ham veriyi (stabil CPU/RAM bilgileri, rutin loglar vb.) sürekli olarak merkeze iletir. Bu durum;

* Gereksiz ağ trafiği (Ağ Darboğazı),

- Sunucu tarafında veri yığınları ve donanım ihtiyacında artış,

+ Kritik güvenlik ihlallerinin gürültü arasında kaybolması riskini yaratır.

# Çözüm
  &emsp;Geliştirdiğimiz hibrit mimari ile veri, uçbirimde analiz edilir. Rutin veriler filtrelenir ve paketlenirken, kritik güvenlik ihlalleri "Bypass" kanalıyla anında merkeze raporlanır.

## Sistem Mimarisi
<p align="center">
<img width="750" height="671" alt="sistem_mimarisi" src="https://github.com/user-attachments/assets/dea0f8b0-9e00-4024-86b2-6231b3e71584" />
</p>

<p align="center">
Şema-1: Sistem mimarisi.
</p>




  &emsp;Projemiz, verinin toplanmasından sunucuya yazılmasına kadar olan süreci optimize eden hibrit bir akışa sahiptir.

__Ham Veri Toplama:__ Pardus donanım ve işletim sistemi seviyesinde telemetri verileri toplanır.

**Uçta Analiz & Gürültü Eleme:** Stabil durumdaki veriler elenir. Sadece anlamlı değişimler işleme alınır.

**Dinamik Önceliklendirme:** Bu aşamada veriler iki gruba ayrılır

  &emsp; 1. Kritik Veri (Bypass): Anormal CPU ve RAM kullanımı, anormal disk yazma hızı, USB ihlalleri, yetkisiz girişler, yetkisiz socket kullanımı gibi kritik hatalar anında iletilir.

  &emsp; 2. Rutin Veri (Batch): Periyodik loglar zlib ile sıkıştırılarak paketler halinde gönderilir.

**Ağ Farkındalığı:** Ajan, ağ yoğunluğunu analiz ederek gönderim zamanlamasını otonom olarak ayarlar.


## Öne Çıkan Teknik Özellikler

**Asenkron Motor:** Python asyncio altyapısı sayesinde, veri gönderimi yapılırken sistem takibi kesintisiz devam eder (Non-blocking I/O).

**Veri Optimizasyonu:** JSON paketleme ve zlib sıkıştırma ile ham veriye oranla %80'e varan bant genişliği tasarrufu sağlanır.

**Offline Resilience**: Ağ bağlantısı koptuğunda veriler yerel SQLite veritabanında tamponlanır (Buffering).


## Performans Kıyaslaması

| Metrik | Geleneksel Mimari | Nükleer HackTEK | Kazanım |
| :--- | :--- | :--- | :--- |
| **Ağ Trafiği** | 100 MB / Saat | 15 MB / Saat | **%85 Tasarruf** |
| **Sunucu G/Ç Yükü** | %45 | %12 | **%73 Verimlilik** |
| **Hatalı Alarm Oranı** | Yüksek (Filtresiz) | Çok Düşük (Akıllı Filtre) | **Yüksek Doğruluk** |
| **İletişim Kanalı** | Tek Hat (Sürekli) | Hibrit (Bypass + Batch) | **Dinamik Öncelik** |

# Kurulum ve Kullanımı
1. Bağımlılıkları Kurun
2. Ajanı Çalıştırın
   
# Güvenlik ve Gizlilik

Nükleer HackTEK, sadece ağı izlemekle kalmaz, topladığı verinin ve kendi sisteminin güvenliğini de **Sıfır Güven (Zero-Trust)** ve **Mahremiyet Odaklı Tasarım (Privacy by Design)** prensipleriyle sağlar:

* **Kriptografik Doğrulama:** USB erişim denetimleri sadece seri no (ID_SERIAL) ile değil, **AES-256 (Fernet)** şifreli anahtarlar ile donanımsal olarak yapılır. Hiçbir şifre veya sunucu adresi koda gömülmez (No Hardcoding), izole `.env` dosyalarıyla yönetilir.
* **Yerel Veri İzolasyonu:** Ağ kesintilerinde verilerin tamponlandığı yerel SQLite veritabanı (`edge_cache.db`), Linux yetkilendirme standartlarıyla katılaştırılarak dış okumalara kapatılmıştır. Sadece yetkili işlemler erişebilir.
* **Şifreli İletişim (Data in Transit):** Hibrit telemetri ajanı ile LiderAhenk sunucusu arasındaki "Bypass" ve "Batch" kanallarının tamamı Ortadaki Adam (MitM) saldırılarını önlemek için şifreli bağlantılar üzerinden gerçekleşir.
* **Veri Maskeleme ve Anonimleştirme:** Uçbirimdeki güvenlik logları merkeze iletilmeden önce uçta (edge) filtrelenir. Logların içine yanlışlıkla sızabilecek personelin kişisel verileri maskelenerek kurum içi mahremiyet korunur.

# Takım & Lisans
Bu proje Nüküleer HackTEK Takımı tarafından geliştirilmiştir.
MIT
