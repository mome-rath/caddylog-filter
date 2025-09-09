# Caddylog filter

A cli tool to filter logs in a compatible format (caddy log file format) for entries in a given date range.

## Example

```bash
ts_now="$(date +%s)"
ts_ten_hours_ago=$(( ts_now - 36000 ))

# The given date parameters will be parsed by `date` again, ISO 8601 or RFC 3339 will work as well.
# IF you use timestamps like we're doing here, they need to be prefixed with `@` characters!
./readlogs "@$ts_ten_hours_ago" "@$ts_now" /var/log/caddy/*.log
```

Or, if you are using multiple log files for multiple hosts and want to find out which one has most traffic:

```bash
for log in /var/log/caddy/*.log; do
  [ $(basename "$log")" = "caddy.log" ] && continue # Skip caddy.log
  echo "$(basename "$log"): $(sudo readlogs '5 minutes ago' 'now' "$log" | wc -l)"
done
```

## Prerequisites

You need to have `jq` installed and avalable via the `jq` command as the lines will be parsed with this tool.

## Log File Format

This tool is intended to filter Caddy log files. Any log file format with json lines will work, as long as there is a `ts` field which contains a parseable timestamp.

```json
{"level": "debug", "ts": 1750943340.0804148, "description": "This is just an example for a log file..."}
{"level": "very_critical", "ts": 1750943346, "description": "...that could be parsed with this tool."}
```
