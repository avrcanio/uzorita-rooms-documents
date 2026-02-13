# M2 Email Ingest Spec (IMAP + Parser)

**Owner:** TBD
**Last updated:** 2026-02-13
**Status:** In Progress

## Kontekst
- Booking.com onboarding za nove connectivity integracije je trenutno pauziran.
- MVP koristi fallback: ingest rezervacijskih emailova sa mailbox-a `room_reservations@uzorita.hr`.

## Trenutno implementirano
- Django app: `communications`.
- Modeli:
  - `InboundEmail`
  - `OutboundEmail`
  - `EmailAttachment`
  - `ParseError`
- IMAP ingest komanda:
  - `python manage.py fetch_booking_emails --limit 50 --mark-seen`
- Deduplikacija:
  - po `Message-ID` (`InboundEmail.message_id` je unique).
- Admin registracija communications modela je aktivna.

## Runtime konfiguracija (env)
- `MAILBOX_EMAIL`
- `MAILBOX_PASSWORD`
- `IMAP_HOST`
- `IMAP_PORT`
- `IMAP_USE_SSL`
- `IMAP_FOLDER`
- `SMTP_HOST`
- `SMTP_PORT`
- `SMTP_USE_SSL`
- `SMTP_USE_TLS`
- `SMTP_USER`
- `SMTP_PASSWORD`

## Operativne komande
```bash
cd /opt/stacks/uzorita/rooms/code/backend
docker compose run --rm django sh -lc "pip install --no-cache-dir -r requirements.txt && python manage.py fetch_booking_emails --limit 50 --mark-seen"
```

## Cron job
- Aktivni raspored: svake 2 minute.

```cron
*/2 * * * * cd /opt/stacks/uzorita/rooms/code/backend && docker compose run --rm django sh -lc "pip install --no-cache-dir -r requirements.txt >/dev/null && python manage.py fetch_booking_emails --limit 50 --mark-seen" >> /var/log/uzorita-mail-sync.log 2>&1
```

- Log datoteka:
  - `/var/log/uzorita-mail-sync.log`

## Mail DNS status (uzorita.hr)
- DKIM:
  - `hostingermail-a._domainkey` -> `hostingermail-a.dkim.mail.hostinger.com`
  - `hostingermail-b._domainkey` -> `hostingermail-b.dkim.mail.hostinger.com`
  - `hostingermail-c._domainkey` -> `hostingermail-c.dkim.mail.hostinger.com`
- DMARC:
  - `_dmarc` -> `v=DMARC1; p=none`
- SPF:
  - `v=spf1 include:_spf.mail.hostinger.com ~all`

## Otvoreno (sljedece)
- Parser Booking template-a (`new/modify/cancel`) u strukturirani payload.
- Mapiranje payload-a na `reception.Reservation` i `reception.Guest`.
- Status workflow: `parsed`, `partial`, `failed`.
- Admin queue za `partial/failed` sa ručnom korekcijom.
