# VESONA Gayrimenkul — Tam Kurulum Kılavuzu

Türkiye lüks villa satış şirketi için tam kapsamlı Next.js web sitesi.

---

## Proje Yapısı

```
vesona/
├── src/
│   ├── app/
│   │   ├── api/
│   │   │   ├── leads/route.ts         ← Lead kaydet + email gönder
│   │   │   └── properties/route.ts   ← Mülk arama API
│   │   ├── properties/
│   │   │   ├── page.tsx               ← Mülk listesi (filtrelemeli)
│   │   │   └── [slug]/page.tsx        ← Mülk detay sayfası
│   │   ├── hakkimizda/page.tsx        ← Hakkımızda
│   │   ├── hizmetler/page.tsx         ← Hizmetler
│   │   ├── galeri/page.tsx            ← Galeri
│   │   ├── iletisim/page.tsx          ← İletişim (3 form)
│   │   ├── page.tsx                   ← Ana sayfa
│   │   ├── layout.tsx                 ← Root layout
│   │   └── globals.css
│   ├── components/
│   │   ├── layout/
│   │   │   ├── Navbar.tsx
│   │   │   └── Footer.tsx
│   │   └── ui/
│   │       ├── ContactForm.tsx        ← Tekrar kullanılabilir form
│   │       └── PropertyCard.tsx
│   ├── data/
│   │   └── properties.ts              ← 6 örnek mülk verisi
│   ├── lib/
│   │   ├── mongodb.ts                 ← DB bağlantısı
│   │   └── mailer.ts                  ← Email gönderme
│   └── models/
│       └── Lead.ts                    ← MongoDB şeması
├── .env.example
├── next.config.js
├── tailwind.config.js
└── package.json
```

---

## Adım Adım Kurulum

### 1. Node.js Gereksinimi
Node.js 18+ gereklidir.
```bash
node --version   # 18.x veya üzeri olmalı
```

### 2. Projeyi Klonla / Klasöre Gir
```bash
cd vesona
```

### 3. Bağımlılıkları Yükle
```bash
npm install
```

### 4. `.env` Dosyasını Oluştur
```bash
cp .env.example .env.local
```
Sonra `.env.local` dosyasını düzenle (detaylar aşağıda).

### 5. Geliştirme Sunucusunu Başlat
```bash
npm run dev
```
Tarayıcıda `http://localhost:3000` adresini aç.

### 6. Production Build
```bash
npm run build
npm start
```

---

## .env.local Yapılandırması

```env
# ─────────────────────────────────────────
# MongoDB
# ─────────────────────────────────────────
# MongoDB Atlas (önerilen - ücretsiz):
MONGODB_URI=mongodb+srv://USERNAME:PASSWORD@cluster0.xxxxx.mongodb.net/vesona?retryWrites=true&w=majority

# Yerel MongoDB:
# MONGODB_URI=mongodb://localhost:27017/vesona

# ─────────────────────────────────────────
# Email (SMTP) Ayarları
# ─────────────────────────────────────────
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_SECURE=false
SMTP_USER=sizin-gmail@gmail.com
SMTP_PASS=xxxx-xxxx-xxxx-xxxx   # Gmail App Password (aşağıya bak)

# ─────────────────────────────────────────
# Alıcı Email (Lead bildirimleri nereye gitsin)
# ─────────────────────────────────────────
OWNER_EMAIL=info@vesona.com

# ─────────────────────────────────────────
# Admin Panel Şifresi (leads GET endpoint'i)
# ─────────────────────────────────────────
ADMIN_SECRET_KEY=gizli-anahtar-123

# ─────────────────────────────────────────
NEXT_PUBLIC_SITE_URL=http://localhost:3000
```

---

## Gmail App Password Nasıl Alınır

1. Google Hesabı → **Güvenlik** sekmesi
2. **2 Adımlı Doğrulama**'yı aç (zorunlu)
3. Güvenlik → **Uygulama şifreleri** (App Passwords)
4. "Uygulama seç" → **Diğer** → "VESONA" yaz
5. **Oluştur** → 16 haneli şifreyi kopyala
6. `.env.local` içinde `SMTP_PASS` değerine yapıştır

> **Not:** Gmail yerine Resend, SendGrid veya Mailgun kullanmak istersen `src/lib/mailer.ts` dosyasını düzenle.

---

## MongoDB Atlas Kurulumu (Ücretsiz)

1. [mongodb.com/cloud/atlas](https://mongodb.com/cloud/atlas) → Ücretsiz hesap
2. Yeni cluster oluştur → **Free** tier seç
3. Database Access → Kullanıcı oluştur (kullanıcı adı + şifre)
4. Network Access → `0.0.0.0/0` ekle (her yerden erişim)
5. Connect → Drivers → connection string'i kopyala
6. String içindeki `<password>` yerine şifreni yaz
7. `.env.local` içindeki `MONGODB_URI`'ye yapıştır

---

## API Endpoint'leri

### Lead Oluştur
```
POST /api/leads
Content-Type: application/json

{
  "type": "contact" | "consultancy" | "property_inquiry",
  "name": "Ad Soyad",
  "phone": "+90 5xx xxx xxxx",
  "email": "ornek@email.com",        // opsiyonel
  "message": "Mesaj metni",          // opsiyonel
  "propertyTitle": "Villa Adı",      // mülk sorgusu için
  "propertyId": "p001",              // mülk sorgusu için
  "preferredDate": "2024-12-15"      // danışmanlık için
}

Response:
{ "success": true, "message": "...", "id": "mongo_id" }
```

### Lead Listesi (Admin)
```
GET /api/leads?key=ADMIN_SECRET_KEY

Response:
{ "success": true, "leads": [...] }
```

### Mülk Arama
```
GET /api/properties?location=Bodrum&minPrice=1000000&type=villa&bedrooms=3

Response:
{ "success": true, "properties": [...], "total": 2 }
```

---

## Sayfa Listesi

| URL | Açıklama |
|-----|----------|
| `/` | Ana Sayfa |
| `/properties` | Mülk Listesi (filtrelemeli) |
| `/properties/[slug]` | Mülk Detay |
| `/hakkimizda` | Hakkımızda |
| `/hizmetler` | Hizmetler |
| `/galeri` | Fotoğraf Galerisi |
| `/iletisim` | İletişim (3 form) |

---

## Mülk Ekleme

`src/data/properties.ts` dosyasına yeni obje ekle:

```typescript
{
  id: 'p007',
  slug: 'istanbul-sariyer-villa',        // URL slug (benzersiz olmalı)
  title: 'Sarıyer Boğaz Manzaralı Villa',
  location: 'İstanbul, Sarıyer',
  district: 'Sarıyer',
  price: 8500000,
  currency: '₺',
  area: 550,
  bedrooms: 6,
  bathrooms: 4,
  type: 'villa',                          // 'villa' | 'penthouse' | 'residence'
  status: 'satilik',                      // 'satilik' | 'kiralik'
  readyStatus: 'hazir',                   // 'hazir' | 'proje'
  features: ['Yüzme Havuzu', 'Boğaz Manzarası', 'Akıllı Ev'],
  lifestyle: ['Denize Yakın', 'Lüks Yaşam'],
  description: 'Mülk açıklaması...',
  image: 'https://...',                   // Ana fotoğraf URL
  gallery: ['https://...', 'https://...'], // Ek fotoğraflar
  agent: {
    name: 'Danışman Adı',
    title: 'Unvan',
    phone: '+90 5xx xxx xxxx',
    image: 'https://...',
  },
}
```

---

## Deployment (Vercel - Önerilen)

```bash
npm install -g vercel
vercel login
vercel --prod
```

Vercel Dashboard → Project → Settings → Environment Variables → Tüm `.env.local` değerlerini ekle.

---

## Teknoloji Stack

- **Frontend:** Next.js 14 (App Router) + TypeScript
- **Styling:** Tailwind CSS + Cormorant Garamond & Jost fontları
- **Backend:** Next.js API Routes
- **Database:** MongoDB + Mongoose
- **Email:** Nodemailer (SMTP)
- **Icons:** Lucide React
- **Images:** Next.js Image (Unsplash)
# vesona
