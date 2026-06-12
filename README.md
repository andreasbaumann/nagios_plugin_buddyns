# check_buddyns

Nagios/Icinga plugin for [BuddyNS](https://www.buddyns.com/) secondary DNS monitoring.

Monitors zones in use vs. plan limit and monthly query traffic vs. quota, with configurable warning/critical thresholds (% of plan). Emits performance data for graphing.

## Requirements

- bash
- curl
- jq
- awk

## Installation

```sh
cp check_buddyns /usr/lib/nagios/plugins/
chmod +x /usr/lib/nagios/plugins/check_buddyns
```

## Usage

```sh
# API token (preferred)
check_buddyns -k <api_token>

# Username + password
check_buddyns -u user@example.com -p secret

# Custom thresholds: warn at 70%, critical at 85% for both zones and traffic
check_buddyns -k <api_token> -w 70 -c 85 -W 70 -C 85
```

Run `check_buddyns -h` for all options.

## Nagios/Icinga configuration

**Command definition:**

```
define command {
    command_name  check_buddyns
    command_line  $USER1$/check_buddyns -k $ARG1$ -w $ARG2$ -c $ARG3$ -W $ARG4$ -C $ARG5$
}
```

**Service definition:**

```
define service {
    host_name             buddyns
    service_description   BuddyNS quota
    check_command         check_buddyns!<api_token>!80!90!80!90
    check_interval        60
    ...
}
```

For Icinga 2, store the API token in a `vars` custom variable or use Icinga's secret store rather than embedding it directly in the config.

## References

- [BuddyNS RESTful API v2 documentation](https://www.buddyns.com/support/api/v2/)

## Exit codes

| Code | Meaning  |
|------|----------|
| 0    | OK       |
| 1    | WARNING  |
| 2    | CRITICAL |
| 3    | UNKNOWN  |

## Performance data

`zones=`, `traffic_kqr=`, and `days_to_reset=` are emitted in standard Nagios perfdata format for use with PNP4Nagios, Graphite, or similar.
