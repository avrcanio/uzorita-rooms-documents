# Uzorita Rooms — TODO & Milestones (procjena sati)

Procjene su **grube** i odnose se na *programiranje* (bez čekanja na vanjske pristupe, birokraciju, itd.).

---

## M0 — Dokumentacija i odluke
**Procjena:** 6 h
- [ ] dovršiti/srediti sve ključne MD dokumente

---

## M1 — Booking read-only sync
**Procjena:** 50 h
- [ ] modeli: Room, Reservation
- [ ] polling (30 min) + “Sync now”
- [ ] idempotent upsert + audit (raw + mapping)
- [ ] statusi: booking_status + ops_status

---

## M2 — Recepcija UI (PWA) + timeline
**Procjena:** 70 h
- [ ] login (Django auth) + role (Admin/Reception)
- [ ] lista dolazaka danas+sutra
- [ ] detalj rezervacije: gosti + timeline
- [ ] timeline: Booking + recepcija eventi

---

## M3 — Check-in wizard + OCR + gosti
**Procjena:** 120 h
- [ ] wizard: front → back → OCR review → Confirm
- [ ] OCR polja (širi set) + ručna korekcija + “Sve provjereno”
- [ ] storage slika: disk, year/month/reservation, UUID
- [ ] dodatni gosti: “Dodaj gosta” (isti wizard)
- [ ] pravila: glavni edit-only; dodatni add/edit/hard delete (user u timeline)

---

## M4 — eVisitor (auto send + potvrda primljeno)
**Procjena:** 60 h
- [ ] auto slanje nakon check-ina
- [ ] statusi: PENDING / SENT / FAILED
- [ ] potvrda “poslano + primljeno”
- [ ] UI: timeline + badge uz glavnog gosta
- [ ] audit request/response (bez slika)

---

## M5 — Check-out + račun + fiskalizacija
**Procjena:** 110 h
- [ ] iznos iz Bookinga + korekcije (EUR)
- [ ] plaćanje: Booking / gotovina / kartica (MVP)
- [ ] R1 default iz glavnog gosta + uređivanje
- [ ] numeracija računa automatski (MVP 1/1)
- [ ] fiskalizacija: broj računa + ZKI + JIR
- [ ] auto retry fiskalizacije
- [ ] check-out moguć i bez fiskalizacije

---

## M6 — PDF računa
**Procjena:** 35 h
- [ ] PDF odmah nakon izdavanja
- [ ] osvježi PDF kad dođe broj računa + ZKI + JIR
- [ ] storage PDF-a + link “Preuzmi PDF”

---

## M7 — Admin & stabilizacija
**Procjena:** 40 h
- [ ] Django admin pregled: eVisitor, računi, fiskalizacija
- [ ] monitoring/logging
- [ ] backup (DB + storage)

---

## Total (M0–M7)
**Procjena ukupno:** 391 h

---

## Roadmap (kasnije) — nije u totalu
- [ ] viva.com Tap on Phone (NFC plaćanje)
- [ ] više poslovnih prostora/uređaja
- [ ] Booking write-back (cijene/dostupnost)