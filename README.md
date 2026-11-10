# Legami Adventskalender Watch

This project checks a Legami Advent calendar product page and sends a notification when availability changes.

It is designed to work with ntfy (push notifications) and optionally SMTP mail.

## Quick start

1. Install Python 3.11+.
2. Install dependencies:

   ```bash
   python -m venv .venv
   source .venv/bin/activate
   pip install -r requirements.txt
   ```

3. Create a local `.env` file or export the variables directly:

   ```bash
   export WATCH_URL="https://www.legami.com/de-de/adventskalender-dream-peaks-VADC0004.html"
   export NTFY_TOPIC="your-topic-name"
   export NTFY_SERVER="https://ntfy.sh"
   export STATE_FILE=".legami_state.json"
   ```

   Optional SMTP configuration:

   ```bash
   export SMTP_HOST="smtp.example.com"
   export SMTP_PORT="465"
   export SMTP_USER="you@example.com"
   export SMTP_PASS="secret"
   export MAIL_TO="you@example.com"
   export MAIL_FROM="you@example.com"
   ```

4. Test notification delivery:

   ```bash
   python legami_watch.py --test-notify
   ```

5. Run a single check:

   ```bash
   python legami_watch.py --debug
   ```

6. Run continuously (cron/systemd):

   ```bash
   python legami_watch.py
   ```

## ntfy details

- `NTFY_TOPIC` is the topic name to publish to.
- `NTFY_SERVER` defaults to `https://ntfy.sh`.
- For self-hosted ntfy servers, set `NTFY_SERVER` to your server URL.
- If your ntfy instance requires authentication, set either:
  - `NTFY_TOKEN` for bearer-token auth, or
  - `NTFY_USERNAME` + `NTFY_PASSWORD` for basic auth.

## Example with a self-hosted ntfy server

```bash
export NTFY_SERVER="https://ntfy.example.com"
export NTFY_TOPIC="christmas-watch"
export NTFY_USERNAME="user"
export NTFY_PASSWORD="secret"
python legami_watch.py --debug
```

## State file

The script stores the latest signature/status in `.legami_state.json` by default, so it only alerts when the product status changes.

## Cron example

```cron
*/5 * * * * cd /path/to/Christmas-Calendar && . .venv/bin/activate && NTFY_TOPIC="your-topic" NTFY_SERVER="https://ntfy.sh" python legami_watch.py >> /tmp/legami-watch.log 2>&1
```

## Notes

- The script intentionally exits successfully even on short fetch failures so cron runs stay quiet unless the problem persists.
- It keeps a small state file so repeated checks do not spam notifications.
