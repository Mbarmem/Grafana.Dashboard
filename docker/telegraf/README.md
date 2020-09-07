## TELEGRAF CONFIG

#### Modify the following in the telegraf.conf as per your virtual machine.

```ini
hostname = "host"                     # CHANGE HOSTNAME

urls = ["http://192.168.1.252:8086"]  # CHANGE IP/PORT TO INFLUXDB SERVER

database = "docktelegraf"             # NAME OF THE DATABASE

interfaces = ["eth0"]                 # INTERFACE OF THE NEWTORK
```
