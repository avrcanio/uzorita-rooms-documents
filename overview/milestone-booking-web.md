# Milestone: Booking web (booking.uzorita.hr)

**Status:** In progress  
**Last updated:** 2026-02-16

Cilj: public booking web u Next.js, s dostupnošću, multi-room combo ponudama, checkoutom, boravišnom pristojbom i obaveznim online plaćanjem u visokoj sezoni (Viva.com).

---

## 0) Setup / infrastruktura

- [x] DNS: kreirati `booking.uzorita.hr`
  - [x] Cloudflare record je **Proxied** (A/AAAA pokazuju na Cloudflare edge IP-e), verified `2026-02-16`
- [ ] Deploy okruženje (staging + prod)
  - [x] Booking web dignut kao Docker servis iza Traefik-a (router `booking.uzorita.hr`), verified `2026-02-16`
- [ ] SSL certifikat
  - [x] HTTPS radi kroz Cloudflare proxy, verified `2026-02-16`
- [ ] Basic monitoring/logging (min: error tracking + request logs)

## 1) Public web (Next.js)

- [x] Repo scaffold: `code/booking` (Next.js + Tailwind + docker-compose + osnovni routing)
  - [x] Docker image/service: `uzorita-booking-web` (Traefik labels, `proxy` network)
  - [x] Helper skripta za Cloudflare DNS upsert: `code/ops/cloudflare_dns.py` (zahtijeva `CLOUDFLARE_API_TOKEN` ili `CLOUDFLARE_GLOBAL_API_KEY` + `CLOUDFLARE_EMAIL`)

### Stranice i routing
- [x] `/` (hero search-first) (placeholder UI)
- [x] `/search` (grid 2×2 desktop, 1× mobile) (trenutno 1 kolona; placeholder data)
- [x] `/rooms/[slug]` (SEO + availability widget + 2-mjesečni kalendar) (placeholder)
- [x] `/checkout?hold=...` (checkout samo s hold tokenom) (guard + placeholder)
- [x] `/confirmation?code=...` (public-safe prikaz + polling) (placeholder)

### SEO
- [x] Dynamic metadata (title/description/OG) za `/rooms/[slug]`
- [x] Schema.org (Room/Accommodation)
- [x] Sitemap (barem rooms)
- [x] Robots.txt

### UI detalji
- [x] Sobe prikazati i kad nisu dostupne (zasjenjeno)
- [ ] Klik na nedostupnu sobu: modal s kalendarom dostupnosti
- [x] Preporučene kombinacije (combo) za veće grupe

### Implementirano (update 2026-02-16)

- [x] Branding i jezik
  - [x] Naziv booking weba je `Uzorita Luxury Rooms`
  - [x] Multi-language (`hr`/`en`) preko `?lang=`, `booking_lang` cookie i `Accept-Language`
  - [x] Google Search Console verification meta dodan
- [x] Home (`/`)
  - [x] Rendera podatke s `GET /api/public/property/` (about, company_info, neighborhood, surroundings, address, maps)
  - [x] Dodan Google Maps launcher PNG i WhatsApp launcher PNG (klikabilne ikone)
  - [x] Floating WhatsApp widget (`wa.me/<broj>`)
  - [x] Dodan blok `Pregled soba / Rooms Preview` ispod adrese, iz `primary_room_photos`
- [x] Search (`/search`)
  - [x] Aktivna pretraga na samoj stranici (`checkin/checkout/adults/children` + submit)
  - [x] Kartice soba renderaju `primary_photo_url` kao glavnu sliku kartice
  - [x] Kartice soba koriste `GET /api/public/availability/` (status dostupnosti + `accommodation_total`)
  - [x] Sobe su sortirane po najnižoj cijeni
  - [x] Linkovi vode na `/rooms/[slug]` uz `checkin/checkout/adults/children/lang`
  - [x] Combo blok prikazan iznad liste soba, puni se iz API-ja (`combos[]`)
- [x] Room detalj (`/rooms/[slug]`)
  - [x] Carousel s auto-slide svakih 5s
  - [x] Glavna slika koristi `url`, thumbnail strip koristi `url_small` (160px)
  - [x] Klik na thumbnail mijenja glavnu sliku
  - [x] Uklonjen debug/API info card (`API (Public)`) iz UI
  - [x] Kalendar zauzeća po fizičkim sobama (`K1/K2/...`) preko `GET /api/public/rooms/{room_id}/calendar/?month=YYYY-MM`
  - [x] Navigacija kalendara: `Sljedeci mjesec` / `Prosli mjesec` (prosli limitiran do tekućeg mjeseca)
- [x] Header/nav pravila
  - [x] Na home i search stranici skriveni su gumbi `Pocetna` i `Pretraga` (ostaje logo + language switch)
  - [x] Na room stranici također nema `Pocetna`/`Pretraga`

### Public API (rooms/property) – implementirano

- [x] `GET /api/public/rooms/`
- [x] `GET /api/public/rooms/{id}/`
  - [x] Vraća i `primary_photo_url`
  - [x] `photos[]` uključuje `url` i `url_small` (thumbnail 160px)
- [x] `GET /api/public/property/`
  - [x] Vraća property sadržaj za booking web
  - [x] Vraća `whatsapp_phone`
  - [x] Vraća `primary_room_photos[]` (`room_type_id`, `room_type_code`, `room_type_slug`, `url`)
- [x] OpenAPI dokumentacija dostupna na `https://rooms.uzorita.hr/api/docs/` (tag `Public`)

### RoomTypePhoto (backend) – implementirano

- [x] Dodano polje `is_primary` na `RoomTypePhoto`
- [x] Admin inline prikazuje checkbox `is_primary`
- [x] DB pravilo: po `room_type` samo jedna fotografija može biti `is_primary=true` (`UniqueConstraint`)

### Pricing (backend) – implementirano

- [x] Cjenik je prebačen na razinu fizičke sobe (`Room`), ne `RoomType`
- [x] Dodani modeli:
  - [x] `RoomTypePricingPlan` (po sobi: base cijena, valuta, period, default)
  - [x] `RoomTypePricingRule` (override po `season/month/week/day`)
- [x] Dodana occupancy pravila u cjeniku:
  - [x] `adults_count`
  - [x] `children_count`
- [x] Pravila prioriteta cijena:
  - [x] `day > week > month > season > base`
- [x] Admin podrška:
  - [x] unos cjenika direktno na `Room` (inline planovi + pravila)

## 2) Availability + combos (backend)

- [x] `GET /api/public/availability/?checkin&checkout&adults&children`
  - [x] vraća sve aktivne sobe s `available=true/false`
  - [x] vraća `pricing.accommodation_total` (bez boravišne)
  - [x] vraća `combos[]` (1–3 preporuke) s `allocation`
- [x] `GET /api/public/rooms/{room_id}/calendar/?month=YYYY-MM`
  - [x] vraća dnevni kalendar dostupnosti za mjesec
  - [x] vraća `pricing.accommodation_nightly` (bez boravišne)

## 3) HOLD (anti-overbooking)

- [ ] `POST /public/holds` (10 min)
- [ ] `GET /public/holds/{hold_token}` snapshot
  - [ ] vraća `combo.allocation` ako postoji
  - [ ] kad je CONSUMED vraća `booking_code` (+ payment snapshot ako je PENDING_PAYMENT)
- [ ] Job: expire holds (10 min) → oslobodi availability

## 4) Checkout + quote (boravišna pristojba)

- [ ] Checkout forma: glavni gost (ime, prezime, email, telefon)
- [ ] DOB za svako dijete (obavezno ako children > 0)
- [ ] `POST /public/pricing/quote` (računa boravišnu)
- [ ] Prikaz cijena:
  - [ ] Smještaj (bez takse)
  - [ ] Boravišna
  - [ ] Ukupno

## 5) Booking confirm

- [ ] `POST /public/bookings/confirm` (idempotentno po hold_token)
  - [ ] vraća `hold_status`
  - [ ] niska sezona → odmah `CONFIRMED`
  - [ ] visoka sezona (6–9) → `PENDING_PAYMENT` + `payment_url`
- [ ] Booking code `UZR-XXXXXXXX` (unique)

## 6) Viva.com (Smart Checkout) — visoka sezona

- [ ] Viva setup:
  - [ ] Payment Source + `sourceCode`
  - [ ] demo credentials
  - [ ] prod credentials
- [ ] Kreiranje payment ordera na confirmu (visoka sezona)
- [ ] Webhook: `POST /webhooks/payments/viva`
  - [ ] signature verification
  - [ ] `PENDING_PAYMENT -> CONFIRMED`
  - [ ] spremiti `orderCode` (string) + transaction id
- [ ] Timeout: PENDING_PAYMENT 15 min → EXPIRED + oslobodi availability

## 7) Email

- [ ] Email potvrde na CONFIRMED (SMTP već postoji)
- [ ] Email otkaza (ako je otkaz dio MVP-a)

## 8) Cancellation (public)

- [ ] `POST /public/bookings/cancel/request` (email link)
- [ ] `GET /public/bookings/cancel/preview?token=...` (fee% po sezoni)
- [ ] `POST /public/bookings/cancel/confirm`
- [ ] Pravila:
  - [ ] niska sezona 0%
  - [ ] visoka sezona: 30+ dana 0%, 8–29 dana 50%, 0–7 dana 90%

## 9) Smoke testovi

- [ ] Niska sezona: CONFIRMED + email
- [ ] Visoka sezona: PENDING_PAYMENT + payment_url
- [ ] Webhook success: CONFIRMED + email
- [ ] Timeout: EXPIRED + availability free
- [ ] Cancel: fee% po pravilima

---

## Definition of Done

- [ ] End-to-end flow radi za nisku i visoku sezonu
- [ ] Nema overbookinga (hold + payment timeout)
- [ ] SEO stranice soba indexable
- [ ] Email potvrde stiže

---

## Ops / komande (trenutno)

- Build + run booking web:
  - `cd code/booking && docker compose up -d --build`
- Provjera:
  - `docker ps | rg uzorita-booking-web`
  - `curl -I https://booking.uzorita.hr/`
