
# Grafana.Dashboard
Grafana dashboard for monitoring virtual machines, pihole, nas, docker containers and plex ecosystem!!

![License](https://img.shields.io/badge/license-GPL-blue)
![Status](https://img.shields.io/badge/status-In%20progress-orange)

### Table of Contents
- [How it Works](#How-it-Works)
- [Installing InfluxDB](#Installing-InfluxDB)
- [Installing Telegraf](#Installing-Telegraf)
  * [Installing HDDTemp](##Install-HDDTemp)
  * [Installing Sensors for Temp Monitoring](##Install-Sensors-for-Temp-Monitoring)
- [Installing Varken](#Installing-Varken)
- [Installing Sabnzbd Script](#Installing-Sabnzbd-Script)
- [Installing Speedtest](#Installing-Speedtest)

---

<p align="center"><img src="assets/dashboard.png"></p>

---

## How it Works
In getting all this setup, there are 3 main moving parts. Telegraf, InfluxDB and Grafana.
Telegraf is what collects all the different system metrics and outputs it to an InfluxDB database that Grafana uses to visualize everything with pretty graphs and bars.

<p align="center">
  <img src="assets/tig.png">
</p>

---

## Installing InfluxDB

1. Download the config file and place it in the influxdb appdata folder i.e. ./docker/influxdb
2. Run the docker-compose.

```ini
# INFLUXDB - DATABASE FOR SENSOR DATA
  influxdb:
    image: influxdb:latest
    container_name: influxdb
    restart: always
    security_opt:
      - no-new-privileges:true
    ports:
      - 8086:8086
      - 8089:8089/udp
    volumes:
      - ./docker/influxdb/influxdb.conf:/etc/influxdb/influxdb.conf:ro
      - ./docker/influxdb/db:/var/lib/influxdb
    environment:
      - TZ=Europe/Amsterdam
      - INFLUXDB_HTTP_ENABLED=true
      - INFLUXDB_DB=host
    command: -config /etc/influxdb/influxdb.conf
```
---

## Installing Telegraf

1. Download the config file and place it in the telegraf appdata folder i.e. ./docker/telegraf
2. Edit the telegraf.conf file. Scroll down to **OUTPUT PLUGINS** and edit the **url** on line 106 and **database** on the 110.

```ini
# Configuration for sending metrics to InfluxDB
[[outputs.influxdb]]
  urls = ["http://192.168.1.252:8086"]

  ## The target database for metrics; will be created as needed.
  ## For UDP url endpoint database needs to be configured on server side.
 database = "docktelegraf"
```

###### 192.168.1.252 is the IP address of the server running InfluxDB and 8086 is the default InfluxDB port. InfluxDB will save the metrics sent from telegraf under docktelegraf database.

3. Incase you are using default config file, the following input plugins needs to be enabled so that all the panels on the Grafana dashboard will work. It is curently enabled in the attached config file.
```
[[inputs.docker]]

[[inputs.hddtemp]]

[[inputs.net]]

[[inputs.netstat]]

[[inputs.sensors]]
```

## Installing HDDTemp

```ini
# HDDTEMP - MONITOR HDD TEMPS
  hddtemp:
    image: drewster727/hddtemp-docker:latest
    container_name: hddtemp
    restart: unless-stopped
    privileged: true
    environment:
      - HDDTEMP_ARGS="-q -d -F /dev/sd*"
      - TZ=Europe/Amsterdam
```

## Installing Sensors for Temp Monitoring

```ini
# Install lm-sensor
sudo apt install lm-sensors

# Detect all the sensors
sudo sensors-detect --auto
```

4. Run the docker-compose.

```ini
# TELEGRAF - SERVER TELEMERTY AND METRICS COLLECTOR
  telegraf:
    image: telegraf:latest
    container_name: telegraf
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    volumes:
      - ./docker/telegraf/telegraf.conf:/etc/telegraf/telegraf.conf:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - /sys:/rootfs/sys:ro
      - /proc:/rootfs/proc:ro
      - /etc:/rootfs/etc:ro
    environment:
      - TZ=Europe/Amsterdam
      - HOST_PROC=/rootfs/proc
      - HOST_SYS=/rootfs/sys
      - HOST_ETC=/rootfs/etc
```
---

## Installing Varken

1. Download the config file and place it in the varken appdata folder i.e. ./docker/varken
2. Edit the config file. Detailed instructions can be found [here](https://wiki.cajun.pro/books/varken/page/breakdown).
3. Run the docker-compose.

```ini
# VARKEN - MONITOR PLEX, SONARR, RADARR, AND OTHER DATA
  varken:
    image: boerderij/varken:latest
    container_name: varken
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    volumes:
      - ./docker/varken:/config
    environment:
      - PUID=1000
      - PGID=1000
      - TZ=Europe/Amsterdam
```

## Installing Sabnzbd Script

1. Edit the environmental values in the docker-compose.
2. Run the docker-compose.

```ini
# SABNZBD SCRIPT – SABNZBD STATS FOR INFLUXDB
  sabnzbd-influxdb:
    image: mbarmem/sabnzbd_influxdb:latest
    container_name: sabnzbd-influxdb
    restart: unless-stopped
    volumes:
    - ./docker/telegraf:/config
    environment:
      - INTERVAL=5
      - SABNBZBD_HOST=192.168.1.252                     # IP ADDRESS OF SABNZBD SERVER
      - SABNZBD_PORT=8080                               # PORT OF SABNZBD SERVER
      - SABNZBD_KEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX    # API KEY FOR SABNZBD
      - INFLUXDB_HOST=192.168.1.252                     # IP ADDRESS OF INFLUXDB SERVER
      - INFLUXDB_PORT=8086                              # PORT OF INFLUXDB SERVER
      - INFLUXDB_DB=sabnzbd                             # NAME OF SABNZBD DATABASE
```
---

## Installing Speedtest

1. Download the config file and place it in the speedtest appdata folder i.e. ./docker/speedtest
2. Edit the config file and modify the **adress** and **port** under Influxdb.

```ini
[INFLUXDB]
Address = 192.168.1.252         # IP ADDRESS OF INFLUXDB SERVER
Port = 8086                     # PORT OF INFLUXDB SERVER
```
3. Run the docker-compose.

```ini
# SPEEDTEST – SPEEDTEST COLLECTOR FOR INFLUXDB
  speedtest:
    image: atribe/speedtest-for-influxdb-and-grafana:latest
    container_name: speedtest
    volumes:
      - ./docker/speedtest/config.ini:/src/config.ini
    restart: unless-stopped
```
