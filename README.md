# Linux Server Health Check

A single Bash script that gives you the 60-second answer to *"is this server healthy?"*
— CPU, memory, disk, top processes, service status, and failed SSH logins, with
color-coded OK / WARNING / CRITICAL thresholds and a timestamped report file.

This is project 1 of a progression: next step is turning these same checks into
Prometheus alerts with Grafana dashboards.

## What it checks

| Check | Source | Thresholds |
|---|---|---|
| CPU usage | `/proc/stat` | warning 70%, critical 90% |
| Memory usage | `/proc/meminfo` | warning 75%, critical 90% |
| Disk usage (`/`) | `df` | warning 75%, critical 90% |
| Top processes | `ps` (by CPU and memory) | — |
| Services (`ssh`, `docker`, `cron`) | `systemctl` / `pgrep` | running or not |
| Failed SSH logins | `/var/log/auth.log` | count + last 5 (needs root to read) |

Edit the `WARN_*` / `CRIT_*` values and the `SERVICES` list at the top of the
script to match your own server.

## Usage

```bash
chmod +x health-check.sh
./health-check.sh                 # colored summary + saves report
./health-check.sh --report-only   # just save the report (for cron)
```

Reports land in `reports/` as `health-report-<hostname>-<timestamp>.txt`.

Tip — run it on a schedule and keep a history:

```bash
crontab -e
# every hour, keep reports
0 * * * * /path/to/health-check.sh --report-only
```

## Sample output

```
Health summary for myserver:
  CPU:    12.4% [OK]
  Memory: 38.2% [OK]
  Disk /: 41% [OK]

Full report saved to: reports/health-report-myserver-20261006-230500.txt
```

See [`reports/sample-output.txt`](reports/sample-output.txt) for a full example report.

## Skills practiced

Bash scripting, `/proc` filesystem, process management (`ps`, `pgrep`),
`systemd` service checks, log inspection, threshold-based alerting logic —
the same fundamentals behind real monitoring tools.
