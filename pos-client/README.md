Restoran POS Sistemi

Offline-first, uçtan uca şifreli ve merkezi sunucuyla otomatik senkronize çalışan bir restoran kasa (POS) sistemi.

Durum: Aktif geliştirme aşamasında (Faz 0 — Kurulum)

Neden Bu Proje

Restoranlarda internet kesintisi "olursa" değil "ne zaman" sorusudur. Bu proje, kasanın internete bağımlı olmadan çalışmasını, verinin lokal olarak şifreli tutulmasını ve bağlantı geri geldiğinde hiçbir veri kaybı olmadan merkezi sunucuyla senkronize olmasını hedefler.

Mimari
Kasa (Wails + React)
   │
   ├─ SQLite + SQLCipher (lokal, şifreli veritabanı)
   ├─ Outbox tablosu (offline sipariş/ödeme kaydı)
   ├─ Termal yazıcı entegrasyonu (ESC/POS)
   │
   ▼ HTTPS + API Key/HMAC imza
Merkez API (Go + Fiber)
   │
   ├─ PostgreSQL (ana veri deposu)
   │
   ▼
Python Analitik Script (haftalık satış trendi, basit tahmin)
Teknoloji Seçimleri ve Gerekçeleri
Katman	Teknoloji	Neden
Kasa Arayüzü	Wails (Go + React)	Native performans + modern web UI, tek çalıştırılabilir dosya
Lokal Veritabanı	SQLite + SQLCipher	Offline çalışma + AES-256 şifreleme, donanım çalınsa bile veri okunamaz
Senkronizasyon	Outbox Pattern (SQLite tablo + Go worker)	Redis/Asynq gibi ek bağımlılık gerektirmeden kayıpsız veri aktarımı
Merkez API	Go (Fiber)	Yüksek performans, düşük kaynak tüketimi
Merkez Veritabanı	PostgreSQL	İlişkisel bütünlük, stok/sipariş verisi için sağlam altyapı
Analitik	Python (pandas)	Veri analizi ekosisteminin standardı, ana sistemden bağımsız çalışır
Bilinçli Olarak Kullanılmayanlar
Redis + Asynq: Tek şube senaryosunda outbox pattern tek başına yeterli; ek altyapı karmaşıklığı gereksiz.
mTLS: HTTPS + API Key/HMAC imzalama bu ölçekte yeterli güvenlik sağlıyor; sertifika otomasyonu tek işletme için orantısız operasyonel yük getirir.
Çoklu şube mimarisi: Kapsam bilinçli olarak tek şubeye odaklanıyor, kod tabanını sade tutuyor.