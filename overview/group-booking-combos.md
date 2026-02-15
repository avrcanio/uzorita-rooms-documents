# Group booking — kombinacije soba (multi-room)

**Last updated:** 2026-02-15  
**Status:** Draft

Opisuje kako booking web slaže ponudu (kombinacije soba) kad gosti ne stanu u jednu sobu.

## Pravila kapaciteta (sažetak)

- Sve sobe osim trokrevetne: **max 2 osobe ukupno** (djeca ne povećavaju kapacitet)
- Trokrevetna:
  - default **max 3 ukupno**
  - iznimka: **točno 2 odrasla + 2 djece => max 4 ukupno**

## UI zahtjev

- Nakon unosa datuma i broja gostiju prikazati **svih 5 soba**.
- Nedostupne sobe prikazati **zasjenjeno**.
- Klik na nedostupnu sobu otvara **kalendar dostupnosti** te sobe.
- Iznad liste soba prikazati **1–3 preporučene kombinacije** (kad treba).
- Checkout mora podržavati **rezervaciju više soba odjednom**.

## API prijedlog (MVP)

### 1) Dostupnost + preporučene kombinacije
`GET /public/availability?checkin=YYYY-MM-DD&checkout=YYYY-MM-DD&adults=6&children=2`

Backend vraća:
- `rooms[]`: svih 5 soba sa `available=true/false`
- `combos[]`: 0–3 preporučene kombinacije (ako treba)

Primjer (skraćeno):
```json
{
  "checkin": "2026-07-12",
  "checkout": "2026-07-16",
  "adults": 6,
  "children": 2,
  "rooms": [
    {"room_id":"triple","name":"Deluxe trokrevetna","available":true},
    {"room_id":"d2a","name":"Deluxe dvokrevetna 1","available":true},
    {"room_id":"d2b","name":"Deluxe dvokrevetna 2","available":false},
    {"room_id":"dk","name":"Deluxe kingsize","available":true},
    {"room_id":"sk","name":"Standard kingsize","available":true}
  ],
  "combos": [
    {
      "combo_id": "c1",
      "rooms": ["triple","d2a","dk"],
      "allocation": {
        "triple": {"adults": 2, "children": 2},
        "d2a": {"adults": 2, "children": 0},
        "dk": {"adults": 2, "children": 0}
      }
    }
  ]
}