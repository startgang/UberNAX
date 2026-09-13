# Test guide

## 📦 INSTALL UTILITIES
```bash
sudo apt update
sudo apt install stress-ng lm-sensors s-tui htop intel-gpu-tools powercap-utils msr-tools devmem2 inxi
sudo sensors-detect --auto
```

## 📥 DOWNLOAD SETPL SCRIPT
```bash
cd ~
wget https://raw.githubusercontent.com/horshack-dpreview/setPL/master/setPL.sh
chmod +x setPL.sh
```

## 📦 INSTALL THROTTLED DAEMON
```bash
# Install git and python3-venv
sudo apt install git python3-venv

# Clone repository and install
git clone https://github.com/erpalma/throttled.git
cd throttled
sudo ./install.sh
```

## ⚙️ CONFIGURE THROTTLED
```bash
# Edit config (set PL1_Tdp_W, PL2_Tdp_W, Trip_Temp_C)
sudo nano /etc/throttled.conf

# Restart service after changes
sudo systemctl restart throttled
sudo systemctl status throttled
```

Example `[AC]` section with comments:
```ini
[AC]
# How often to re-apply limits (seconds)
Update_Rate_s: 5
# Long-term power limit (watts)
PL1_Tdp_W: 75
# Long-term time window (seconds)
PL1_Duration_s: 28
# Short-term power limit (watts)
PL2_Tdp_W: 90
# Short-term time window (seconds)
PL2_Duration_S: 0.002
# Temperature threshold for throttling (C)
Trip_Temp_C: 95
# cTDP mode: 0=normal, 1=down, 2=up
cTDP: 0
# Disable BD PROCHOT signal (experimental)
Disable_BDPROCHOT: False
```

Restart service after changes:
```bash
sudo systemctl restart throttled
sudo systemctl status throttled
```

## ⚠️ IMPORTANT NOTES
```bash
# Secure Boot must be disabled in BIOS, otherwise MSR/MMIO writes will be blocked
sudo mokutil --sb-state

# setPL.sh does not persist across reboots
# BIOS resets PL1/PL2 to defaults on every boot
# Use throttled daemon to re-apply limits automatically
```

## ⚙️ POWER LIMITS (MSR)
```bash
sudo ./setPL.sh 40 65
sudo ./setPL.sh 75 90
sudo ./setPL.sh 88 100
sudo powercap-info -p intel-rapl | grep power_limit_uw
sudo powercap-info -p intel-rapl | grep -E "power_limit_uw|time_window_us"
sudo rdmsr -p 0 0x610
sudo wrmsr -p 0 0x610 <value>
```

## 🌡️ THROTTLED (THROTTLING DIAGNOSTICS)
```bash
sudo systemctl stop throttled
sudo /opt/throttled/venv/bin/throttled --monitor
sudo systemctl start throttled
sudo systemctl restart throttled
sudo systemctl status throttled
```

## 🖥️ MONITORING
```bash
watch -n 1 'sensors; echo "---"; grep MHz /proc/cpuinfo'
grep MHz /proc/cpuinfo
s-tui
htop
inxi -G
```

## 🔥 STRESS TEST
```bash
stress-ng --cpu 0 --timeout 10m
stress-ng --cpu 4 --timeout 5m
stress-ng --cpu 6 --timeout 5m
stress-ng --cpu 8 --timeout 5m
stress-ng --cpu 12 --timeout 5m
stress-ng --cpu 16 --timeout 5m
```
> 0 - all cores

## 🔋 BIOS / MSR DIAGNOSTICS
```bash
sudo dmesg | grep -i rapl
sudo dmesg | grep -i "locked by BIOS"
sudo mokutil --sb-state
cpupower frequency-info
cat /sys/devices/system/cpu/intel_pstate/no_turbo
```

## 📊 QUICK STATE CHECK
```bash
sudo powercap-info -p intel-rapl | grep power_limit_uw
sensors
grep MHz /proc/cpuinfo
```
