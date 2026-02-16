# Milestone: Booking web (booking.uzorita.hr)

**Status:** Draft  
**Last updated:** 2026-02-16

Cilj: public booking web u Next.js, s dostupnošću, multi-room combo ponudama, checkoutom, boravišnom pristojbom i obaveznim online plaćanjem u visokoj sezoni (Viva.com).

---

## 0) Setup / infrastruktura

- [ ] DNS: kreirati `booking.uzorita.hr`
- [ ] Deploy okruženje (staging + prod)
- [ ] SSL certifikat
- [ ] Basic monitoring/logging (min: error tracking + request logs)

## 1) Public web (Next.js)

### Stranice i routing
- [ ] `/` (hero search-first)
- [ ] `/search` (grid 2×2 desktop, 1× mobile)
- [ ] `/rooms/[slug]` (SEO + availability widget + 2-mjesečni kalendar)
- [ ] `/checkout?hold=...` (checkout samo s hold tokenom)
- [ ] `/confirmation?code=...` (public-safe prikaz + polling)

### SEO
- [ ] Dynamic metadata (title/description/OG) za `/rooms/[slug]`
- [ ] Schema.org (Room/Accommodation)
- [ ] Sitemap (barem rooms)
- [ ] Robots.txt

### UI detalji
- [ ] Sobe prikazati i kad nisu dostupne (zasjenjeno)
- [ ] Klik na nedostupnu sobu: modal s kalendarom dostupnosti
- [ ] Preporučene kombinacije (combo) za veće grupe

## 2) Availability + combos (backend)

- [ ] `GET /public/availability?checkin&checkout&adults&children`
  - [ ] vraća svih 5 soba s `available=true/false`
  - [ ] vraća `pricing.accommodation_total` (bez boravišne)
  - [ ] vraća `combos[]` (1–3 preporuke) s `allocation`
- [ ] `GET /public/rooms/{room_id}/calendar?month=YYYY-MM`

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