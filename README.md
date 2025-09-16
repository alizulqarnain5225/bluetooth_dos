# Bluetooth DoS Tool

> A PyQt5-based GUI tool for **controlled Bluetooth DoS testing** in lab environments.
> Built for authorized security testing and research — **do not** use this against devices or networks you do not own or have explicit written permission to test.

---

## ⚠️ Important Legal & Ethical Notice

This project contains code that can be used to perform denial-of-service (DoS) style tests against Bluetooth devices. Use **only** in controlled environments with explicit authorization (written permission). Unauthorized use may be illegal and unethical and can cause device damage, data loss, or service disruption.

By using this tool you confirm you are authorized to test the target device(s). The author and repository owner are **not** responsible for misuse.

---

## What it is

A desktop GUI application (PyQt5) that performs bluetooth-layer ping flooding using standard `bluez` utilities (e.g. `l2ping`, `hcitool`, `hciconfig`). It is intended for lab testing of Bluetooth resilience and device behaviour when subject to high ping/flood rates.

Key features:

* PyQt5 GUI for easy configuration and monitoring
* Scan nearby Bluetooth devices and pick a target
* Several pre-configured attack levels (Easy / Medium / Worst)
* Threaded flood engine with multi-interface support
* Real-time progress and monitoring of target responsiveness
* Logging and a short report generator

---

## ⚙️ Requirements

Tested on Linux (BlueZ stack). You will need:

* Python 3.8+
* PyQt5 (`pip install PyQt5`)
* BlueZ tools installed (`hcitool`, `l2ping`, `hciconfig`) — commonly available via your distribution packages (e.g. `sudo apt install bluez` on Debian/Ubuntu)
* Permissions: Running `l2ping`/`hciconfig` may require root privileges or CAP\_NET\_RAW capability.

Recommended:

* Run in an isolated network / test lab
* Use a dedicated Bluetooth adapter for testing

---

## Installation

1. Clone the repository:

```bash
git clone https://github.com/<your-username>/bluetooth-dos-tool.git
cd bluetooth-dos-tool
```

2. Create virtual environment (recommended):

```bash
python3 -m venv venv
source venv/bin/activate
```

3. Install Python dependencies:

```bash
pip install -r requirements.txt
```

> If you don't have a `requirements.txt`, the only Python dependency is `PyQt5`:

```bash
pip install PyQt5
```

4. Install BlueZ (if not already installed):

```bash
# Debian / Ubuntu
sudo apt update
sudo apt install bluez
```

---

## Usage

> **Always get written authorization** before targeting any device.

Run the GUI:

```bash
python3 main.py
```

### GUI Overview

* **Scan Devices** — scans nearby discoverable Bluetooth devices using the primary interface and fills the device list.
* **Target MAC** — pick a device from the list (click item) or paste a MAC address in the field.
* **Attack Level** — select `Easy`, `Medium`, or `Worst`. Each maps to:

  * `Easy` — fewer threads, small packet sizes, faster flood interval.
  * `Medium` — intermediate.
  * `Worst` — many threads, large packet sizes, slow flood interval (maximum stress).
* **Duration (seconds)** — length of the test.
* **Start Attack / Stop Attack** — controls the flood. Progress bar shows percent complete.
* **Log Output** — real-time messages shown in the GUI and saved to `bluetooth_dos.log`.
* At the end, a `dos_report.txt` is generated summarizing the test.

### Attack-level defaults (from the code)

```python
ATTACK_LEVELS = {
    'Easy':   {'threads': 20,  'package_size': 100,    'flood_rate': 0.05},
    'Medium': {'threads': 50,  'package_size': 600,    'flood_rate': 0.005},
    'Worst':  {'threads': 500, 'package_size': 65535,  'flood_rate': 0.001}
}
```

---

## Configuration & Internals (for maintainers / reviewers)

* **Entry file**: `main.py` (or whichever file you place the code in) — contains the PyQt GUI and threaded worker classes.
* **Core classes**

  * `DosThread` — QThread that spawns worker threads via `ThreadPoolExecutor` to run `l2ping` floods. Emits progress, logs and final packet count.
  * `MonitorThread` — QThread that periodically checks target responsiveness with single `l2ping` pings.
* **Logging** — application logs to `bluetooth_dos.log` (configured at top of script). A short text summary report is written to `dos_report.txt` after the attack completes.
* **Interface discovery** — uses `hciconfig` output. If interfaces are down, the code tries to bring them up automatically.

---

## Safety & Hardening Suggestions

If you plan to continue development or use the code as a platform for legitimate testing, consider:

* Implementing strict authorization checks (e.g., require a confirmation dialog that includes target MAC + signed test authorization file).
* Rate-limiting and guardrails to prevent accidental wide-scale floods.
* More robust process handling for `subprocess` calls and improved error reporting for missing permissions.
* Unit tests and CI workflows that only run non-destructive components.
* Add CLI mode for automated, auditable testing with a required `--auth-file` parameter containing proof of authorization.

---

## Troubleshooting

* **Missing tools (`hcitool`, `l2ping`, `hciconfig`)**
  Install BlueZ: `sudo apt install bluez`

* **Bluetooth interface not found**
  Ensure your adapter is connected and not blocked by rfkill:

  ```bash
  rfkill list
  sudo rfkill unblock bluetooth
  ```

* **Permission errors**
  Try running the app as root for testing (or grant CAP\_NET\_RAW to python binary in a controlled way).

* **No devices discovered**
  Make sure target devices are in discoverable mode within Bluetooth range.

---

## Contributing

Contributions are welcome from security researchers and maintainers — but please **only** propose features and changes that support safe, legal testing. Open an issue or PR describing the test environment and the safety measures you plan to use.

---

## Example `requirements.txt`

```
PyQt5>=5.15
```

---

## License

Choose a license appropriate to your intentions. Example:

* **MIT License** — permissive (still does not imply permission for illegal actions).
* **Responsible Disclosure / Research Use** addendum recommended.

---

## Acknowledgements

Built using Python, PyQt5 and BlueZ utilities. Thanks to the open-source Bluetooth and Python communities.

---

## Quick Start Checklist (copy-paste)

```bash
# clone
git clone https://github.com/alizulqarnain5225/bluetooth_dos.git
cd bluetooth_dos

# venv + deps
python3 -m venv venv
source venv/bin/activate
pip install PyQt5

# ensure BlueZ tools installed
sudo apt update
sudo apt install bluez

# run
sudo python3 main.py
```

---
Thanks me later :)
