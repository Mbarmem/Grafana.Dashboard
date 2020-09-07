## SPEEDTEST CONFIG

#### Modify the config.ini file as

```ini
[GENERAL]
Delay = 3600                    # DELAY BETWEEN TESTS IN SECONDS - DEFAULT IS ONE HOUR

[INFLUXDB]
Address = 192.168.1.252         # IP ADDRESS OF INFLUXDB SERVER
Port = 8086                     # PORT OF INFLUXDB SERVER
Database = speedtest            # NAME OF THE DATABASE
Username =
Password =
Verify_SSL = False

[SPEEDTEST]
# Leave blank to auto pick server
Server =                        # YOU CAN SELECT SPECIFIC SPEED TEST SERVER

[LOGGING]
# Valid Options: critical, error, warning, info, debug
Level = info
```
