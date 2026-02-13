# Audit log (MVP) — kompletan payload

## Odluka
Spremamo kompletan raw payload (JSON) iz Booking sinkronizacije.

## Tablice
### sync_run
- meta o svakom pokretanju (cron/manual), status, stats

### sync_event
- po entitetu/akciji
- payload: jsonb (kompletan)
- opcionalno payload_hash za deduplikaciju

## Napomena
Osobne i OCR podaci (check-in) NE idu u ovaj audit.