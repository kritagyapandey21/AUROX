# AUROX Deployment Context

## Repository

- GitHub: `https://github.com/kritagyapandey21/AUROX`
- VPS checkout: `/var/www/aurox`
- Latest deployment commits include the timezone feature, Hostinger setup, backend isolation, and `/pocketoption/` routing.

## Production Architecture

- Domain: `tradingsignal.in`
- Login URL: `https://tradingsignal.in/pocketoption/`
- Frontend: `/var/www/aurox/frontend`
- Backend service: `aurox-backend`
- Backend bind address: `127.0.0.1:8001`
- Nginx proxies `/api/` to port `8001` and serves the frontend under `/pocketoption/`.
- HTTPS is installed with Let's Encrypt.
- The existing website on the VPS must remain isolated and unchanged.

## Implemented Features

- Searchable IANA timezone selector at login.
- Trader timezone preference persisted by Trader ID in SQLite.
- Existing Trader ID verification remains unchanged.
- Existing traders default to `Asia/Kolkata`.
- Signal API includes absolute ISO timestamps while preserving legacy time fields.
- Frontend displays timezone-converted times without showing dates.
- Countdown uses absolute timestamps and is independent of display timezone.
- Pocket Option integration remains the signal data source.

## Current Remaining Issue

The public website and backend health endpoint work, but `/api/signal` previously returned `Market data unavailable`. Backend logs showed Pocket Option SSID parsing/connection failure. The production `LO_UID` is expired or invalid.

Next steps:

1. Obtain a fresh Pocket Option WebSocket `42["auth", ...]` session token.
2. Replace only `LO_UID` in `/var/www/aurox/backend/.env`.
3. Keep `.env` private and set permissions to `600`.
4. Restart and inspect the service:

```bash
systemctl restart aurox-backend
journalctl -u aurox-backend -f
```

5. Confirm the log contains a successful Pocket Option connection.
6. Verify:

```bash
curl https://tradingsignal.in/api/health
curl https://tradingsignal.in/api/signal
```

Do not store or commit Telegram, Pocket Option, or other credential values in this file. Previously exposed credentials should be rotated.
