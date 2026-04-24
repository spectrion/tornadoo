![Tornadoo-Logo](https://spectrion.github.io/assets/IMG_4101.png)

**Live Android boot tool for iPhone 7 and iPhone 7 Plus.**

Boots Android from RAM using checkm8 + PongoOS + Project Sandcastle.
iOS stays on your device untouched. Power off = back to iOS, no trace.

---

## ⚠ WARNING

**This may brick your device. Read fully before use.**

- Only iPhone 7 and 7 Plus are supported
- Do NOT unplug during the exploit window (Tornadoo warns you)
- Back up your device fully before starting
- Android will have NO camera, NO cellular, NO Bluetooth, NO audio
- You run this entirely at your own risk

---

## Requirements

- **macOS** or **Linux** (Windows not supported — checkra1n has no Windows build)
- **Node.js 18+**
- **Python 3** with `pyusb`: `pip3 install pyusb`
- **USB cable** (Lightning to USB-A or USB-C)
- **iPhone 7 or 7 Plus** (A10 chip)

Optional but recommended:
- `libimobiledevice` for automatic device detection (`brew install libimobiledevice`)

---

## Install

```bash
npm install
```

---

## Run

```bash
npm start
# or
node bin/tornadoo.js
# or with verbose logging
TORNADOO_VERBOSE=1 npm start
```

On Linux you may need sudo for USB access:
```bash
sudo npm start
```

---

## What happens step by step

1. **Warning screen** — you type `I ACCEPT THE RISK` to continue
2. **Device detection** — plug in your iPhone, Tornadoo identifies the model
3. **DFU mode guide** — step-by-step button combo instructions with timing
4. **Download** — checkra1n, PongoOS, and the Android image are downloaded once and cached at `~/.tornadoo/cache`
5. **Exploit** — checkra1n runs checkm8, loads PongoOS into the bootrom
6. **Android load** — PongoOS receives the Android kernel over USB, boots it into RAM
7. **Done** — Android is running, you can safely unplug

---

## What works in Android

| Feature         | Status |
|-----------------|--------|
| Display + touch | ✅ Works |
| Wi-Fi           | ✅ Works |
| USB             | ✅ Works |
| Basic apps      | ✅ Works |
| Camera          | ❌ Not supported |
| Cellular / SIM  | ❌ Not supported |
| Bluetooth       | ❌ Not supported |
| Audio output    | ❌ Not supported |
| GPU accel       | ❌ Not supported |
| Persistent storage | ❌ RAM only — nothing saves |

---

## To boot Android again after power off

Just plug in and run `npm start` again. The full exploit chain takes about 2-3 minutes.

---

## Troubleshooting

**"Permission denied" on Linux**
```bash
sudo npm start
# or add udev rules:
echo 'SUBSYSTEM=="usb", ATTR{idVendor}=="05ac", MODE="0666"' | sudo tee /etc/udev/rules.d/99-apple.rules
sudo udevadm control --reload-rules
```

**Device not detected**
- Try a different USB cable (some cables are charge-only)
- Try a different USB port
- On macOS: check System Settings → Privacy & Security if checkra1n is blocked

**DFU mode timing is hard**
- iPhone 7 DFU: Side button + Volume Down together for 8s, release Side only, keep Volume Down for 5s
- The screen must be completely black (not the Apple logo)
- Takes a few tries for most people — Tornadoo will retry automatically

**Device stuck after failed flash**
- Connect to iTunes (Windows/macOS) or Finder (macOS Catalina+)
- iTunes will offer to restore — this puts iOS back fully

---

## How it works technically

checkm8 is a permanent bootrom exploit in Apple's A10 and older chips.
It cannot be patched by iOS updates because it's in read-only boot ROM.

Tornadoo uses it to boot PongoOS — a custom bootloader — instead of iOS.
PongoOS then receives the Android kernel over USB and boots it directly into RAM.

The NAND flash (where iOS lives) is never written to. Android has no storage.
Every boot is completely fresh.

---

## Credits

- [checkra1n](https://checkra.in) — checkm8 exploit implementation
- [PongoOS](https://github.com/checkra1n/PongoOS) — custom bootloader
- [Project Sandcastle](https://projectsandcastle.org) — Android port for iPhone 7
- Tornadoo — orchestration, UX, and automation layer

---

## Legal

Tornadoo is a tool for use on devices you own. Using it on someone else's device
without permission may be illegal in your jurisdiction. You assume all risk.
