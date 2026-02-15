9# Otkaz rezervacije — sezonska politika

**Last updated:** 2026-02-15  
**Status:** Draft

Ovaj dokument definira pravila otkaza koja koristi public flow otkaza (vidi `operations/booking-cancellation.md`).

## Definicija sezone

- **Visoka sezona:** mjeseci **6, 7, 8, 9** (lipanj–rujan)
- **Niska sezona:** svi ostali mjeseci

**MVP pravilo:** sezona se određuje po **datumu check-in-a**.

## Pravila otkaza

### Niska sezona
- Otkaz je moguć **u bilo kojem trenutku**
- Naknada: **0%**

### Visoka sezona
Naknada ovisi o broju dana do check-in-a (`days_before`):

- `days_before >= 30` → **0%**
- `8 <= days_before <= 29` → **50%**
- `0 <= days_before <= 7` → **90%**

## Izračun `days_before`

- Koristi samo datume (bez vremena) u zoni **Europe/Zagreb**:
  - `today = localdate()`
  - `days_before = (checkin_date - today).days`

## Baza za obračun naknade

**MVP preporuka:**
- naknada se računa na **cijenu smještaja** (`accommodation_total`)
- **ne** na boravišnu pristojbu

Primjer:
- smještaj 420 €
- naknada 50% → 210 €

## Edge cases

- Ako je `days_before < 0` (check-in je prošao):
  - `cancellable=false` (preporuka) ili tretirati kao 90% — odaberi u implementaciji
- Ako rezervacija prelazi preko više mjeseci:
  - MVP: gledaj samo check-in mjesec

Korak 2 — Nadopuni cancel “preview” response
U GET /public/bookings/cancel/preview?token=... vrati još:
fee_percent
fee_amount
policy_note
Primjer:
Json
Copy code
{
  "booking_code": "UZR-ABCDEFGH",
  "cancellable": true,
  "fee_percent": 50,
  "fee_amount": 210,
  "policy_note": "Visoka sezona: otkaz 8–29 dana prije dolaska nosi naknadu 50% (računa se na smještaj).",
  "expires_at": "..."
}
Korak 3 — UX na /cancel?token=...
Prije gumba “Potvrdi otkaz” pokaži blok:
“Naknada za otkaz: 50% (210 €)”
“Plaćanje po dolasku — naknada se može naplatiti prema pravilima objekta.”
checkbox: “Razumijem uvjete otkaza” (da smanjiš prigovore)
Korak 4 — Spremi naknadu u booking kod otkaza
Kad gost potvrdi otkaz, spremi snapshot u booking:
cancelled_at
cancellation_fee_percent
cancellation_fee_amount
cancellation_policy_version (opcionalno, ali korisno)