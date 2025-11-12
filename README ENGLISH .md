# Ghost Box Pi - Radio Scanner (FM/AM/AIR)

![Python](https://img.shields.io/badge/Python-3.7+-blue.svg)
![Platform](https://img.shields.io/badge/Platform-Raspberry%20Pi%205-red.svg)
![License](https://img.shields.io/badge/License-Open%20Source-green.svg)

Application for scanning radio bands (FM, AM, AIR, CB and more) using RTL-SDR v4 on Raspberry Pi. Ghost Box Pi enables automatic switching between radio stations with adjustable speed, volume and advanced noise control.

## Hardware Requirements

- **Raspberry Pi 5** (recommended) or Raspberry Pi 4
- **RTL-SDR v4** (USB dongle) - **ORIGINAL ONLY!** ⚠️
- **Official power supply** Raspberry Pi 5 (5V/5A USB-C)
- Speaker/headphones (HDMI, 3.5mm Jack or Bluetooth)
- USB 3.0 port (blue) - recommended for RTL-SDR

### ⚠️ Where to Buy Original RTL-SDR v4?

**VERY IMPORTANT**: There are many fake RTL-SDR devices on the market that may not work!

**Official stores:**
- 🔗 **List of authorized sellers**: [https://www.rtl-sdr.com/buy-rtl-sdr-dvb-t-dongles/](https://www.rtl-sdr.com/buy-rtl-sdr-dvb-t-dongles/)

**How to recognize original RTL-SDR Blog v4:**
- ✅ Metal case (blue or silver)
- ✅ "RTL-SDR Blog" logo on the case
- ✅ SMA connector (screw-on antenna)
- ✅ Price around $35-45 USD

**Signs of a fake:**
- ❌ No "RTL-SDR Blog" logo
- ❌ Plastic case
- ❌ Price below $25 USD
- ❌ Seller not listed on official website

## System Requirements

- **Operating System**: Raspberry Pi OS (64-bit) - latest version
- **Python**: 3.7 or newer
- **System libraries**: 
  - RTL-SDR v4 drivers
  - PortAudio
  - USB libraries

---

## Installation

### Step 1: System Update

Open terminal and execute the following commands:

```bash
sudo apt update
sudo apt upgrade -y
```

---

### Step 2: RTL-SDR v4 Driver Installation

RTL-SDR v4 requires special drivers compiled from source.

**Remove old drivers:**

```bash
sudo apt purge -y ^librtlsdr* ^rtl-sdr*
```

**Remove remnants manually (copy the entire line exactly!):**

```bash
sudo rm -rvf /usr/lib/librtlsdr* /usr/include/rtl-sdr* /usr/local/lib/librtlsdr* /usr/local/include/rtl-sdr* /usr/local/include/rtl_* /usr/local/bin/rtl_*
```

**Install build tools:**

```bash
sudo apt-get install libusb-1.0-0-dev git cmake pkg-config build-essential
```

**Get the source code:**
```bash
git clone https://github.com/rtlsdrblog/rtl-sdr-blog
cd rtl-sdr-blog/
```

**Build and compile:**
```bash
mkdir build
cd build
cmake ../ -DINSTALL_UDEV_RULES=ON
make
```

**Install the drivers:**
```bash
sudo make install
sudo cp ../rtl-sdr.rules /etc/udev/rules.d/
sudo ldconfig
```
**Block default DVB driver:**

```bash
echo 'blacklist dvb_usb_rtl28xxu' | sudo tee /etc/modprobe.d/blacklist-rtl-sdr.conf
```

**System restart:**

```bash
sudo reboot
```

---

### Step 3: Installation Verification

After restart, connect RTL-SDR v4 to USB port and run test:

```bash
rtl_test -t
```

**Expected result**: You should see device information (e.g. "RTL-SDR Blog V4") and successfully completed test.

---

### Step 4: Python and PIP Installation

```bash
sudo apt install -y python3-pip
```

---

### Step 5: Audio Dependencies Installation

```bash
sudo apt install -y libportaudio2 portaudio19-dev
```

---

### Step 6: Python Libraries Installation

Choose library set depending on which version you want to run.

#### A) For Basic Version (v1) and Advanced Version (v2):

```bash
pip install pyrtlsdr sounddevice numpy scipy --break-system-packages
```

#### B) For PRO Version (v4):

This command installs everything you need: customtkinter for UI and soundfile for recording.

```bash
pip install pyrtlsdr sounddevice numpy scipy customtkinter soundfile --break-system-packages
```

**Installed libraries:**
- `pyrtlsdr` - RTL-SDR communication
- `sounddevice` - audio playback
- `numpy` - numerical operations
- `scipy` - signal processing
- `customtkinter` - **(V4 ONLY)** modern graphical interface
- `soundfile` - **(V4 ONLY)** saving .wav audio files

---

### Step 7: Download Application

```bash
# Return to home directory
cd ~

# Clone repository
git clone https://github.com/gazda12345kamil-dotcom/ghost-box-pi.git
cd ghost-box-pi
```

Or download code manually and save as `ghostbox_fm.py` (basic), `ghostbox_fm_V2.py` (advanced) or `ghostbox_pi_PRO_v4.py` (PRO) on desktop.

---

## Running

### Basic Version:

```bash
python3 ghostbox_fm.py
```

### Advanced Version (V2):

```bash
python3 ghostbox_fm_V2.py
```

### PRO Version (v4):

```bash
python3 ghostbox_pi_PRO_v4.py
```

---
## Troubleshooting

### Problem 1: RTL-SDR Not Detected

**Solution:**

1. Check USB connection - use USB 3.0 port (blue)
2. Verify in system: `lsusb` - look for ID `0bda:2838`
3. Check power - use official 5V/5A power supply
4. Check if you have original: [https://www.rtl-sdr.com/buy-rtl-sdr-dvb-t-dongles/](https://www.rtl-sdr.com/buy-rtl-sdr-dvb-t-dongles/)
5. Disconnect and reconnect dongle
6. Check blacklist: `cat /etc/modprobe.d/blacklist-rtl-sdr.conf`

---

### Problem 2: No Sound

**Solution:**

1. **Select audio output:**
   - Right-click speaker icon in taskbar
   - Select appropriate device (HDMI, Headphones, Bluetooth)

2. **Check system volume:**
   - Left-click speaker icon
   - Make sure volume is not muted

3. **Test sounddevice:**
   ```python
   python3
   >>> import sounddevice as sd
   >>> import numpy as np
   >>> fs = 48000
   >>> t = np.arange(fs * 3)
   >>> audio = 0.5 * np.sin(2 * np.pi * 440 * t / fs)
   >>> sd.play(audio.astype(np.float32), fs)
   >>> sd.wait()
   >>> exit()
   ```

---

### Problem 3: Library Installation Errors

**Solution:**

1. Make sure all system dependencies are installed (Step 2 and 5)
2. Check internet connection
3. Update pip:
   ```bash
   pip install --upgrade pip --break-system-packages
   ```
4. Retry library installation (Step 6)

---

### Problem 4: "Device or resource busy"

**Solution:**

1. Close all SDR programs (SDR++, GQRX, CubicSDR)
2. Disconnect and reconnect dongle
3. As last resort: `sudo reboot`

---

### Problem 5: Version V2/V4 - Only Silence

**Solution:**

1. **Check Squelch slider** - if set too high, may mute all signals
2. Set Squelch to **0** (fully left) to disable function
3. Gradually increase value until noise between stations stops

---

### Problem 6: V4 Startup Error: "No module named 'customtkinter'"

**Solution:**

You haven't installed the interface library. Execute command:

```bash
pip install customtkinter --break-system-packages
```

---

### Problem 7: V4 Startup Error: "No module named 'soundfile'"

**Solution:**

You haven't installed the audio recording library. Execute command:

```bash
pip install soundfile --break-system-packages
```

---

## Features

### Basic Version:
- ✅ Full FM band scanning (87.5-108 MHz)
- ✅ Adjustable scanning speed (50-500 ms)
- ✅ Real-time volume control
- ✅ FM demodulation with automatic audio normalization
- ✅ Graphical interface (Tkinter)
- ✅ Event logging system

### Advanced Version (V2) - all above plus:
- ✅ **Squelch** - noise suppression on FM
- ✅ **Mix Mode (Random)** - random FM scanning
- ✅ Better sound quality control

### PRO Version (v4) - all V2 features plus:
- ✅ **Modern Interface** (CustomTkinter)
- ✅ **Multi-Band Scanning** (WBFM, AM, NBFM)
- ✅ **Full Band Mixing** (checkbox selection)
- ✅ **Advanced DSP Filtering** for each mode
- ✅ **Precise Squelch** based on signal power
- ✅ **Audio session recording (REC)** - save to .wav files
- ✅ **Signal strength indicator (S-Meter)** - signal power visualization
- ✅ **Settings save and load** - automatic configuration memory

---

## Configuration

### Band Configuration

Parameters can be adjusted directly in `.py` files.

#### Versions v1 and v2 (`ghostbox_fm.py`, `ghostbox_fm_V2.py`):

```python
# FM frequency range
FM_START_FREQ = 87.5e6
FM_END_FREQ = 108.0e6
FM_STEP = 0.1e6  # Scanning step

# SDR parameters
SDR_SAMPLE_RATE = 1.024e6
SDR_GAIN = 'auto'
AUDIO_SAMPLE_RATE = 48000
```

#### PRO Version v4 (`ghostbox_pi_PRO_v4.py`):

```python
# Band Definitions (WBFM, AM, NBFM)
BANDS_CONFIG = {
    "FM":      {'name': "FM",  'start': 87.5e6,  'end': 108.0e6, 'step': 0.1e6,  'mode': "WBFM"},
    "AIR":     {'name': "AIR", 'start': 108.1e6, 'end': 137.0e6, 'step': 0.025e6, 'mode': "AM"},
    "CB":      {'name': "CB",  'start': 26.965e6,'end': 27.405e6,'step': 0.01e6, 'mode': "AM"},
    "AM":      {'name': "AM", 'start': 531e3,   'end': 1701e3,  'step': 9e3,    'mode': "AM"},
    "WX":      {'name': "WX",  'start': 162.400e6,'end': 162.550e6,'step': 0.025e6,'mode': "NBFM"},
    "2M-HAM":  {'name': "2M-HAM",'start': 144.0e6, 'end': 146.0e6, 'step': 0.025e6,'mode': "NBFM"}
}

# SDR parameters
SDR_SAMPLE_RATE = 1.024e6
SDR_GAIN = 'auto'
AUDIO_SAMPLE_RATE = 48000
```

### Saved Settings

PRO Version (v4) automatically creates `ghostbox_config.json` file in the same folder. It stores:
- Last slider positions (volume, speed, squelch)
- Band checkbox states
- Random mode (Mix)

To reset settings to default values, simply delete the `ghostbox_config.json` file.

---

## Required Libraries

### Python
- `tkinter` - **(for v1, v2)** graphical interface (built into Python)
- `pyrtlsdr` - RTL-SDR interface
- `sounddevice` - audio playback
- `numpy` - numerical calculations
- `scipy` - signal processing
- `customtkinter` - **(for v4)** modern graphical interface
- `soundfile` - **(for v4)** saving .wav audio files

### System
- `librtlsdr` - RTL-SDR v4 drivers
- `libportaudio2` - audio library
- `libusb-1.0-0` - USB communication

---

## License

**Open Source** - This project is free software. Everyone can use it, modify and distribute without restrictions. Code is publicly available for educational and community purposes.

---

## Collaboration

Bug reports, suggestions and pull requests are welcome! The project is open to everyone who wants to help with its development.

---

## Disclaimers

- Application is intended for educational and experimental purposes
- Follow local regulations regarding radio device usage
- Do not illegally listen to radio transmissions
- Author is not responsible for misuse of the application

---

## Contact

For questions or problems, open an Issue on GitHub.

🔗 **More information about RTL-SDR**: [https://www.rtl-sdr.com](https://www.rtl-sdr.com)

---

**Built with ❤️ for the Raspberry Pi and SDR community**
