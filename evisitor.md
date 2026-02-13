# eVisitor — globalni flow (MVP)

## Cilj
Nakon potvrđenog check-ina (Confirm/Submit), sustav šalje prijavu boravka u **eVisitor (HR)**.

## Kada se šalje
- **tek nakon Confirm/Submit** u check-in wizardu
- kad se kasnije doda dodatni gost: šalje se prijava i za tog gosta (po potrebi)

## Flow (globalno)
1) Check-in završen (glavni gost potvrđen)
2) Sustav kreira zapis: **eVisitor PENDING**
3) Pokušaj slanja u eVisitor
4) Rezultat:
   - **SENT** → označi kao prijavljeno (spremi potvrdu/identifikator ako postoji)
   - **FAILED** → označi grešku + omogućiti retry

## Statusi
- `PENDING`
- `SENT`
- `FAILED`

## UI (recepcija)
Na detalju rezervacije (timeline i/ili na gostu):
- eVisitor: PENDING
- eVisitor: SENT (vrijeme)
- eVisitor: FAILED (kratka poruka) + gumb **Pokušaj ponovno**

## Podaci (iz OCR + ručno potvrđenih polja)
Koristimo širi set koji skupljamo:
- ime, prezime, spol, datum rođenja, državljanstvo
- tip dokumenta, broj dokumenta
- datum izdavanja, datum isteka
- mjesto rođenja, adresa (ako treba/ako postoji)
- OIB (ako postoji / ručno)

## Audit
- bilježimo svaki pokušaj slanja (vrijeme, rezultat, poruka)
- spremamo request/response payload (bez slika osobnih)

## Napomena
Detalji protokola/autentikacije eVisitor rješavaju se tijekom kodiranja, prema službenoj specifikaciji i pristupu.