# Broj računa — numeracija (MVP)

## Odluka
Broj računa se generira **automatski** prema pravilu (bez ručnog unosa recepcije).

## Princip (globalno)
- broj ide sekvencijalno (inkrement)
- format uključuje:
  - **broj računa**
  - **oznaku poslovnog prostora**
  - **oznaku naplatnog uređaja**

## UI
- broj računa se prikazuje na računu i u PDF-u
- u slučaju retry fiskalizacije, broj računa ostaje isti (ne mijenja se)

## Napomena
Konkretan format i reset (po godini ili kontinuirano) definiraju se tijekom implementacije.