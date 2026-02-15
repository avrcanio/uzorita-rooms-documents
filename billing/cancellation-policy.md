# Otkaz rezervacije — sezonska politika

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