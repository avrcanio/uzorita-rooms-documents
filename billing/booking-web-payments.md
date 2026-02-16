# Booking web — plaćanje (sezonska pravila)

**Last updated:** 2026-02-15  
**Status:** Draft

Ovaj dokument definira pravila plaćanja na `booking.uzorita.hr`.

## Definicija sezone (MVP)
- **Visoka sezona:** mjeseci **6, 7, 8, 9** (lipanj–rujan)
- **Niska sezona:** svi ostali mjeseci
- Sezona se određuje po **datumu check-in-a**.

## Pravilo plaćanja
### Niska sezona
- Plaćanje: **po dolasku** (gotovina ili kartica)
- Rezervacija: **instant CONFIRMED** nakon `confirm`

### Visoka sezona
- Plaćanje: **online karticom** (obavezno)
- Rezervacija je **CONFIRMED tek nakon uspješne naplate**

## Što se naplaćuje online (MVP)
- Naplaćuje se **cijena smještaja** (`accommodation_total`)
- Boravišna pristojba se i dalje prikazuje i računa na checkoutu, ali se može naplatiti po dolasku (ako želiš kasnije, može i online)

## Statusi (preporuka)
- `HOLD` (privremeno držanje 10–15 min)
- `PENDING_PAYMENT` (kreirano, čeka naplatu)
- `CONFIRMED` (plaćeno / potvrđeno)
- `PAYMENT_FAILED` (neuspjelo plaćanje)
- `CANCELLED` (otkazano)
- `EXPIRED` (istekao hold ili payment window)

## Flow (sažeto)
1. `/search` ili `/rooms/[slug]` → `POST /public/holds` → dobiješ `hold_token`
2. `/checkout?hold=...`
3. Backend odlučuje `payment_required` na temelju sezone:
   - niska sezona → confirm odmah
   - visoka sezona → kreira booking `PENDING_PAYMENT` + otvara payment session

## API prijedlog (MVP)
- `POST /public/bookings/confirm`
  - niska sezona: vraća `status=CONFIRMED`, `booking_code`
  - visoka sezona: vraća `status=PENDING_PAYMENT`, `booking_code`, `payment_session` (provider-specific)

- `POST /webhooks/payments/...` (provider webhook)
  - uspjeh: `PENDING_PAYMENT -> CONFIRMED` + pošalji email potvrde
  - fail: `PENDING_PAYMENT -> PAYMENT_FAILED` (ili ostavi i dopusti retry)

## Timeout
Ako nije plaćeno unutar npr. 15 min:
- `PENDING_PAYMENT -> EXPIRED`
- oslobodi availability

mjenjam pravilo ovo gornje ću implementirati kasnije

# Booking web — plaćanje (postpaid / pay on arrival)

**Last updated:** 2026-02-16  
**Status:** Active (MVP)

## MVP pravilo
- Plaćanje je **po dolasku** (postpaid).
- Dozvoljene metode:
  - **Kartica po dolasku**
  - **Gotovina po dolasku**
- Nema online plaćanja u MVP-u (Viva nije dio MVP-a).

## Impl. napomena
- `POST /public/bookings/confirm` kreira booking kao `CONFIRMED` (ako je dostupno).
- I dalje koristiti `HOLD` (10 min) da se spriječi overbooking dok korisnik ispunjava checkout.