# Django Admin (MVP) — operativni pregled

## Odluka
Za MVP koristimo Django Admin za:
- pregled izdanih računa
- pregled statusa fiskalizacije (FISCALIZED / FAILED / RETRYING)
- pregled eVisitor statusa (PENDING / SENT / FAILED)

## Što recepcija radi u aplikaciji
- check-in / gosti / eVisitor
- check-out / izdavanje računa / preuzimanje PDF-a

## Što admin radi u Django Adminu
- pregled i kontrola “problematičnih” stavki (npr. fiskalizacija u retry/failed)
- ručne korekcije ako treba (ovisno o dopuštenjima)