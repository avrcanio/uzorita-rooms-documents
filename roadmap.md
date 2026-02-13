# Uzorita Rooms — Roadmap

## Faza 1 (MVP) — read-only sync (Booking → Django)
Cilj: lokalni backend ima točan prikaz soba i rezervacija s Bookinga.

### Uključeno
- polling svakih 30 min + "Sync now"
- spremanje soba (read-only)
- spremanje rezervacija (create/update/cancel)
- log sinkronizacije (audit)

### Nije uključeno (još)
- slanje promjena natrag u Booking (cijene, dostupnost, sadržaj)
- channel manager

## Faza 2 — check-in + eVisitor
- slika osobne → OCR → potvrda → slanje u eVisitor

## Faza 3 — check-out + račun + fiskalizacija
- R1 račun + fiskalizacija (FINA) → JIR

## Faza 4 — upravljanje cijenama i dostupnošću (write prema Bookingu)
- cijene, availability, pravila