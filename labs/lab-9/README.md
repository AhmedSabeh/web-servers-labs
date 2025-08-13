# Lab 9 — Troubleshooting and Monitoring
### Task 9.1: Create Intentional Configuration Errors

A. Bad directive (syntax error)
```
# Add this WRONG line inside any server{} temporarily
echo '    listen eighty;' | sudo tee -a /etc/nginx/sites-available/secure-site
sudo nginx -t
# Expect: syntax error near "eighty"
```
B. Port conflict
```
# Duplicate a listen to conflict with another server
sudo sed -i '0,/listen 8087;/s//listen 8086;/' /etc/nginx/sites-available/secure-site
sudo nginx -t
# Might pass syntax but fail to (re)start if 8086 already used
```
C. Missing root
```
# Point root to a non-existent directory
sudo sed -i 's|root /var/www/secure-site;|root /var/www/secure-site-missing;|' /etc/nginx/sites-available/secure-site
sudo nginx -t && sudo systemctl reload nginx
# Then curl to get 404 or log errors
```

### Task 9.2: Practice Troubleshooting
-  Standard workflow that’ll save you in prod:

Syntax
```
sudo nginx -t
```
Service health & last logs
```
sudo systemctl status nginx --no-pager -l
sudo journalctl -u nginx -n 50 --no-pager
```
Which ports are listening?
```
sudo ss -tulnp | grep nginx
```
Active vhosts & includes
```
nginx -T | less   # shows merged, final config
```
Access & error logs (tail live)
```
sudo tail -n 50 -f /var/log/nginx/error.log /var/log/nginx/access.log
```
Revert the intentional errors now:
```
# Revert "listen eighty;" and wrong port
sudo sed -i '/listen eighty;/d' /etc/nginx/sites-available/secure-site
sudo sed -i 's/listen 8086;/listen 8087;/' /etc/nginx/sites-available/secure-site
# Revert missing root
sudo sed -i 's|root /var/www/secure-site-missing;|root /var/www/secure-site;|' /etc/nginx/sites-available/secure-site
sudo nginx -t && sudo systemctl reload nginx
```
### Task 9.3: Create Monitoring Scripts
Create a lightweight process & port watcher.
```
# /usr/local/bin/nginx-watch.sh
sudo tee /usr/local/bin/nginx-watch.sh > /dev/null <<'EOF'
#!/bin/bash
LOG="/var/log/nginx/monitor.log"
TS="$(date '+%Y-%m-%d %H:%M:%S')"

# 1) Is nginx process up?
if ! pgrep -x nginx >/dev/null; then
  echo "$TS ALERT: nginx process is DOWN" | tee -a "$LOG"
  exit 1
fi

# 2) Is port 8087 listening?
if ! ss -ltn 2>/dev/null | awk '{print $4}' | grep -q ':8087$'; then
  echo "$TS ALERT: nginx not listening on 8087" | tee -a "$LOG"
fi

# 3) Quick HTTP probe (HEAD)
if ! curl -sI http://secure.local:8087/ >/dev/null; then
  echo "$TS ALERT: HTTP probe failed for secure.local:8087" | tee -a "$LOG"
fi
EOF
sudo chmod +x /usr/local/bin/nginx-watch.sh
```
Run once to test:
```
sudo /usr/local/bin/nginx-watch.sh && tail -n 20 /var/log/nginx/monitor.log
```
### Task 9.4: Create Log Analysis Script
Summarize status codes, top IPs, and hot paths from access logs.
```
# /usr/local/bin/nginx-analyze.sh
sudo tee /usr/local/bin/nginx-analyze.sh > /dev/null <<'EOF'
#!/bin/bash
ACCESS="/var/log/nginx/access.log"
echo "=== NGINX LOG ANALYSIS @ $(date) ==="
echo
echo "-- Status code counts --"
awk '{print $9}' "$ACCESS" | grep -E '^[0-9]{3}$' | sort | uniq -c | sort -nr
echo
echo "-- Top 10 IPs --"
awk '{print $1}' "$ACCESS" | sort | uniq -c | sort -nr | head -10
echo
echo "-- Top 10 requested paths --"
awk '{print $7}' "$ACCESS" | sort | uniq -c | sort -nr | head -10
echo
echo "-- 4xx/5xx last 50 lines --"
tail -n 50 "$ACCESS" | awk '$9 ~ /4[0-9][0-9]|5[0-9][0-9]/ {print}'
EOF
sudo chmod +x /usr/local/bin/nginx-analyze.sh
```
Run:
```
sudo /usr/local/bin/nginx-analyze.sh
```
### Task 9.5: Create Health Check Script
Check status, latency, and headers; fail on slow/5xx.
```
# /usr/local/bin/nginx-health.sh
sudo tee /usr/local/bin/nginx-health.sh > /dev/null <<'EOF'
#!/bin/bash
URL="http://secure.local:8087/"
MAX_MS=300   # fail if slower than 300ms

read -r HTTP_CODE TIME_TOTAL <<< "$(curl -s -o /dev/null -w "%{http_code} %{time_total}\n" "$URL")"
MS=$(awk -v t="$TIME_TOTAL" 'BEGIN{printf "%.0f", t*1000}')

echo "Health: code=$HTTP_CODE time=${MS}ms"

if [[ "$HTTP_CODE" -ge 500 ]]; then
  echo "CRITICAL: 5xx from $URL"
  exit 2
fi
if [[ "$MS" -gt "$MAX_MS" ]]; then
  echo "WARNING: latency ${MS}ms > ${MAX_MS}ms"
  exit 1
fi
exit 0
EOF
sudo chmod +x /usr/local/bin/nginx-health.sh
```
Run:
```
/usr/local/bin/nginx-health.sh
```
### Task 9.6: Set Up Automated Monitoring
Use cron to run watchers periodically.
```
# Open root's crontab
sudo crontab -e
```
Add:
```
# Run every minute
* * * * * /usr/local/bin/nginx-watch.sh >> /var/log/nginx/monitor.log 2>&1
# Analyze logs every 5 minutes
*/5 * * * * /usr/local/bin/nginx-analyze.sh >> /var/log/nginx/analysis.log 2>&1
# Health check every minute
* * * * * /usr/local/bin/nginx-health.sh >> /var/log/nginx/health.log 2>&1
```
Verify after a few minutes:
```
sudo tail -n 50 /var/log/nginx/monitor.log
sudo tail -n 50 /var/log/nginx/analysis.log
sudo tail -n 50 /var/log/nginx/health.log
```
### Task 9.7: Test Monitoring and Alerting
Trigger some conditions and confirm logs:

Latency & 5xx: Temporarily break upstream or add a sleep in a backend to slow it down, then:
```
/usr/local/bin/nginx-health.sh
tail -n 20 /var/log/nginx/health.log
```
Port/Process down:
```
sudo systemctl stop nginx
sudo /usr/local/bin/nginx-watch.sh
sudo systemctl start nginx
```
Burst traffic (rate limiting/4xx):
```
ab -n 200 -c 50 http://secure.local:8087/
sudo /usr/local/bin/nginx-analyze.sh
```
You should see warnings/alerts appended to the log files accordingly.

### Task 9.8: Nginx Troubleshooting Playbook

#### Quick Checks
- Syntax: `sudo nginx -t`
- Service: `sudo systemctl status nginx --no-pager -l`
- Logs: `sudo tail -F /var/log/nginx/error.log`
- Ports: `sudo ss -tulnp | grep nginx`
- Full config dump: `nginx -T | less`

## Common Issues & Fixes
1. **Syntax error / bad directive**
   - Symptom: `nginx -t` fails.
   - Fix: Correct directive names/values; remove stray characters.

2. **Port already in use**
   - Symptom: Service fails to start; `ss` shows port taken.
   - Fix: Change `listen` port or stop conflicting service.

3. **Permission problems**
   - Symptom: 403 on static files.
   - Fix: Ensure `/var/www/...` readable by `www-data`; check SELinux/AppArmor.

4. **Wrong root / missing files**
   - Symptom: 404 for expected paths.
   - Fix: Verify `root` and `index`; confirm files exist.

5. **Upstream down (proxy)**
   - Symptom: 502/504 errors.
   - Fix: Check backend processes/ports; adjust `proxy_read_timeout`.

## Monitoring & Health
- Process/port: `/usr/local/bin/nginx-watch.sh`
- Health & latency: `/usr/local/bin/nginx-health.sh`
- Log analysis: `/usr/local/bin/nginx-analyze.sh`

## Change Management
- After edits: `sudo nginx -t && sudo systemctl reload nginx`
- Rollback: Revert config file or restore from backup; validate with `nginx -t`.
