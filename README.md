# Grafana.Dashboard
Grafana dashboard for monitoring virtual machines!!


#### <li> Install Sensors for Temp Monitoring
```ini
# Install lm-sensor
sudo apt install lm-sensors

# Detect all the sensors
sudo sensors-detect --auto
```
---

#### <li> Install Hddtemp for Temp Monitoring
```ini
# Install hddtemp
sudo apt install hddtemp
 
# Configure hddtemp daemon  
sudo dpkg-reconfigure hddtemp 
```
---
