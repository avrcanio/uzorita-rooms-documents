# eVisitor — globalni flow (MVP)

## Cilj
Nakon potvrđenog check-ina (Confirm/Submit), sustav **automatski** šalje prijavu boravka u eVisitor (HR) i recepciji jasno pokaže je li prijava **poslana i primljena** (ack).

## Kada se šalje
- automatski odmah nakon Confirm/Submit u check-in wizardu
- kad se kasnije doda dodatni gost: automatski slanje i za tog gosta (po potrebi)

## Flow (globalno)
1) Check-in završen (glavni gost potvrđen)
2) Sustav postavi status: eVisitor PENDING
3) Sustav pošalje prijavu u eVisitor
4) Sustav primi odgovor (ack)
5) Rezultat:
   - SENT (primljeno/OK) → označi prijavljeno + spremi identifikator/potvrdu ako postoji
   - FAILED → označi grešku + omogućiti retry

## Statusi
- PENDING
- SENT (poslano i primljeno)
- FAILED

## UI (recepcija)
Na detalju rezervacije (timeline i/ili uz gosta):
- eVisitor: PENDING (npr. "Šaljem…")
- eVisitor: SENT ("Primljeno") + vrijeme
- eVisitor: FAILED (kratka poruka) + gumb "Pokušaj ponovno"

## Podaci (iz OCR + ručno potvrđenih polja)
Koristimo širi set koji skupljamo:
- ime, prezime, spol, datum rođenja, državljanstvo
- tip dokumenta, broj dokumenta
- datum izdavanja, datum isteka
- mjesto rođenja, adresa (ako treba/ako postoji)
- OIB (ako postoji / ručno)

## Audit
- bilježimo svaki pokušaj slanja (vrijeme, korisnik, rezultat, poruka)
- spremamo request/response payload (bez slika osobnih)

## Napomena
Detalji protokola/autentikacije eVisitor implementiraju se tijekom kodiranja prema službenoj specifikaciji i pristupu.