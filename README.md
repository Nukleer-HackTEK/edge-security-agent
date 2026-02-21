# Edge Security Agent
LiderAhenk merkezi yönetim sistemi ile uyumlu çalışan bu proje, "Uçta Bilişim (Edge Computing)" mimarisini benimseyen akıllı bir uçbirim ve hibrit telemetri ajanı prototipidir. Mevcut izleme sistemlerinin yarattığı ağ darboğazı ve sunucu G/Ç yükü sorunlarını, veriyi kaynağında işleyerek sunucu yükünü minimize eder.

---

# Problem
Mevcut telemetri sistemleri, uçbirimlerden gelen tüm ham veriyi (stabil CPU/RAM bilgileri, rutin loglar vb.) sürekli olarak merkeze iletir. Bu durum;

-Gereksiz ağ trafiği (Ağ Darboğazı),

-Sunucu tarafında veri yığınları ve donanım ihtiyacında artış,

-Kritik güvenlik ihlallerinin gürültü arasında kaybolması riskini yaratır.

# Çözüm
Geliştirdiğimiz hibrit mimari ile veri, uçbirimde analiz edilir. Rutin veriler filtrelenir ve paketlenirken, kritik güvenlik ihlalleri "Bypass" kanalıyla anında merkeze raporlanır.

# Sistem Mimarisi
<p align="center">
<img width="750" height="671" alt="sistem_mimarisi" src="https://github.com/user-attachments/assets/dea0f8b0-9e00-4024-86b2-6231b3e71584" />
</p>

<p align="center">
Şema-1: Sistem mimarisi.
</p>




Projemiz, verinin toplanmasından sunucuya yazılmasına kadar olan süreci optimize eden hibrit bir akışa sahiptir.

**Ham Veri Toplama:** Pardus donanım ve işletim sistemi seviyesinde telemetri verileri toplanır.

**Uçta Analiz & Gürültü Eleme:** Stabil durumdaki veriler elenir. Sadece anlamlı değişimler işleme alınır.

**Dinamik Önceliklendirme:** Bu aşamada veriler iki gruba ayrılır

  Kritik Veri (Bypass): Anormal CPU ve RAM kullanımı, anormal disk yazma hızı, USB ihlalleri, yetkisiz girişler, yetkisiz socket kullanımı gibi kritik hatalar anında iletilir.

  Rutin Veri (Batch): Periyodik loglar zlib ile sıkıştırılarak paketler halinde gönderilir.

**Ağ Farkındalığı:** Ajan, ağ yoğunluğunu analiz ederek gönderim zamanlamasını otonom olarak ayarlar.


#Öne Çıkan Teknik Özellikler
**Asenkron Motor:** Python asyncio altyapısı sayesinde, veri gönderimi yapılırken sistem takibi kesintisiz devam eder (Non-blocking I/O).

**Adaptif Filtreleme:** Ağ trafiği yoğunlaştığında ajan, gürültü eşiğini otomatik olarak yükselterek bant genişliğini korur.

**Veri Optimizasyonu:** JSON paketleme ve zlib sıkıştırma ile ham veriye oranla %80'e varan bant genişliği tasarrufu sağlanır.

**Offline Resilience**: Ağ bağlantısı koptuğunda veriler yerel SQLite veritabanında tamponlanır (Buffering).


# Performans Kıyaslaması

Özellik,Geleneksel Sistemler,Nükleer HackTEK,Kazanım
Ağ Yükü,Sürekli ve Yüksek,Akıllı ve Düşük,%85 Tasarruf
Sunucu G/Ç Yükü,Her veri için yazma,Toplu (Batch) yazma,%70 Daha Az Yük
Güvenlik Tepkisi,Loglar arasında gizli,Anlık (Bypass Kanalı),Gerçek Zamanlı

🚀 Kurulum ve Kullanım1. Bağımlılıkları KurunBashpip install psutil aiohttp cryptography httpx pyudev
2. Ajanı ÇalıştırınPardus uçbirimlerinde güvenlik loglarını takip edebilmek için yönetici yetkisi gereklidir:Bashsudo python3 agent.py
🛡️ Güvenlik ve GizlilikAjan yazılımı, siber kamuflaj tekniklerini kullanarak ağın en sakin olduğu zamanlarda veri iletir. Bu sayede ağ izleme araçları tarafından tespit edilmesi zorlaşır ve kurum içi güvenlik politikalarıyla tam uyumlu çalışır.👥 Takım ve LisansBu proje HACKTEK Takımı tarafından geliştirilmiştir.📜 Lisans: MIT
