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

## 🌡️ THROTTLED (THROTTLING DIAGNOSTICS)
```bash
sudo systemctl stop throttled
sudo /opt/throttled/venv/bin/throttled --monitor
sudo systemctl start throttled
sudo systemctl restart throttled
sudo systemctl status throttled
```

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
