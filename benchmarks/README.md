# Результаты тестов
оформляются...

## ============================================================
## 📦 INSTALL UTILITIES
## ============================================================
sudo apt update
sudo apt install stress-ng lm-sensors s-tui htop intel-gpu-tools powercap-utils msr-tools devmem2 inxi
sudo sensors-detect --auto

## ============================================================
## ⚙️ POWER LIMITS (MSR)
## ============================================================
### Apply power limits via setPL.sh
sudo ./setPL.sh 40 65
sudo ./setPL.sh 75 90
sudo ./setPL.sh 88 100

### Verify applied limits
sudo powercap-info -p intel-rapl | grep power_limit_uw
sudo powercap-info -p intel-rapl | grep -E "power_limit_uw|time_window_us"

### Read/write MSR directly (for reference)
sudo rdmsr -p 0 0x610
sudo wrmsr -p 0 0x610 <value>

## ============================================================
## 🖥️ MONITORING
## ============================================================
### Temperature + frequency (single command)
watch -n 1 'sensors; echo "---"; grep MHz /proc/cpuinfo'

### Frequency of all cores
grep MHz /proc/cpuinfo

### s-tui (interactive monitoring + stress test)
s-tui

### htop (per-core load)
htop

### Hardware summary
inxi -G

## ============================================================
## 🔥 STRESS TEST
## ============================================================
### All cores
stress-ng --cpu 0 --timeout 10m

### Specific core count
stress-ng --cpu 4 --timeout 5m
stress-ng --cpu 6 --timeout 5m
stress-ng --cpu 8 --timeout 5m
stress-ng --cpu 12 --timeout 5m
stress-ng --cpu 16 --timeout 5m
stress-ng --cpu 22 --timeout 10m

## ============================================================
## 🌡️ THROTTLED (THROTTLING DIAGNOSTICS)
## ============================================================
### Stop service before running monitor
sudo systemctl stop throttled

### Real-time throttling monitor
sudo /opt/throttled/venv/bin/throttled --monitor

### Restart service
sudo systemctl start throttled
sudo systemctl restart throttled
sudo systemctl status throttled

## ============================================================
## 🔋 BIOS / MSR DIAGNOSTICS
## ============================================================
### Check if MSR is locked
sudo dmesg | grep -i rapl
sudo dmesg | grep -i "locked by BIOS"

### Secure Boot state
sudo mokutil --sb-state

### Frequency limits
cpupower frequency-info
cat /sys/devices/system/cpu/intel_pstate/no_turbo

## ============================================================
## 📊 QUICK STATE CHECK
## ============================================================
### Power limits
sudo powercap-info -p intel-rapl | grep power_limit_uw

### Temperature
sensors

# Frequency
grep MHz /proc/cpuinfo
