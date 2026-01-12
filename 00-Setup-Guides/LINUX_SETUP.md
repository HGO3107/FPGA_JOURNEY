# Altera USB-Blaster Setup on Linux (Ubuntu/Debian)

Fix Quartus Prime USB-Blaster programming failures on Linux caused by insufficient USB permissions.

This guide applies to Intel/Altera USB-Blaster and USB-Blaster II devices on Ubuntu, Debian, and Debian-based distributions.

---

## Overview

On Linux systems, Quartus Prime may detect the USB-Blaster but fail to program the FPGA due to missing user-level permissions. This typically results in immediate programming failure.

This document shows how to resolve the issue using `udev` rules, without running Quartus as root.

---

## Tested Configuration

- **FPGA:** Intel/Altera Cyclone IV (EP4CE6E22)
- **Programmer:** USB-Blaster / USB-Blaster II (Terasic and compatible clones)
- **Software:** Quartus Prime Lite Edition (Linux)
- **OS:** Ubuntu / Debian / Debian-based distributions

---

## Problem

Quartus Prime lists the USB-Blaster under *Hardware Setup*, but programming a `.sof` file fails immediately.

Running:

```bash
jtagconfig
````

Results in:

```text
Unable to lock chain - Insufficient port permissions
```

---

## Solution

Add `udev` rules to grant user access to USB-Blaster devices.

---

## Setup Instructions

### 1. Create udev Rule File

```bash
sudo nano /etc/udev/rules.d/51-usbblaster.rules
```

---

### 2. Add USB-Blaster Rules

```bash
# Altera USB-Blaster
SUBSYSTEM=="usb", ATTR{idVendor}=="09fb", ATTR{idProduct}=="6001", MODE="0666"
SUBSYSTEM=="usb", ATTR{idVendor}=="09fb", ATTR{idProduct}=="6002", MODE="0666"
SUBSYSTEM=="usb", ATTR{idVendor}=="09fb", ATTR{idProduct}=="6003", MODE="0666"

# Altera USB-Blaster II
SUBSYSTEM=="usb", ATTR{idVendor}=="09fb", ATTR{idProduct}=="6010", MODE="0666"
SUBSYSTEM=="usb", ATTR{idVendor}=="09fb", ATTR{idProduct}=="6810", MODE="0666"
```

Save and exit the editor.

---

### 3. Reload udev Rules

```bash
sudo udevadm control --reload-rules
sudo udevadm trigger
```

Unplug and reconnect the USB-Blaster.

---

## Verification

Confirm access to the JTAG chain:

```bash
jtagconfig
```

Expected:

* No permission errors
* FPGA chain detected
* Successful `.sof` programming

---

## Troubleshooting

* Verify USB detection:

  ```bash
  lsusb
  ```

* Ensure vendor ID `09fb` is present

* Reboot if rules are not applied

* Do not run Quartus as root




