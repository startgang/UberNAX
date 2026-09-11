# Throttled Setup Guide

## Installation
```bash
git clone https://github.com/erpalma/throttled.git
cd throttled
sudo ./install.sh
```

## Configuration File
```bash
sudo nano /etc/throttled.conf
```

### General Settings
```ini
[GENERAL]
Enabled: True
Sysfs_Power_Path: /sys/class/power_supply/AC*/online
Autoreload: True
```

### AC Power Profile
```ini
[AC]
Update_Rate_s: 5
PL1_Tdp_W: 75
PL1_Duration_s: 56
PL2_Tdp_W: 90
PL2_Duration_S: 0.002
Trip_Temp_C: 95
cTDP: 0
Disable_BDPROCHOT: False
```

### Battery Profile
```ini
[BATTERY]
Update_Rate_s: 30
PL1_Tdp_W: 35
PL1_Duration_s: 28
PL2_Tdp_W: 50
PL2_Duration_S: 0.002
Trip_Temp_C: 85
```

## Service Management
```bash
sudo systemctl start throttled
sudo systemctl enable throttled
sudo systemctl status throttled
sudo systemctl restart throttled
```

## Monitoring
```bash
sudo systemctl stop throttled
sudo /opt/throttled/venv/bin/throttled --monitor
```
