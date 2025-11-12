# 🌍 Global FM Radio (Optimized)

[![Python](https://img.shields.io/badge/Python-3.8+-blue.svg)](https://www.python.org/downloads/)
[![Platform](https://img.shields.io/badge/Platform-Raspberry%20Pi%205-c51a4a.svg)](https://www.raspberrypi.com/)
[![RTL-SDR](https://img.shields.io/badge/RTL--SDR-Blog%20V4-orange.svg)](https://www.rtl-sdr.com/)
[![License](https://img.shields.io/badge/License-MIT-green.svg)](LICENSE)

Prosty, ale wydajny odbiornik radia FM napisany w Pythonie z nowoczesnym interfejsem graficznym. Stworzony specjalnie dla **RTL-SDR Blog V4** i zoptymalizowany pod **Raspberry Pi 5**.

![Interfejs Radia FM](https://via.placeholder.com/800x450/1a1a1a/00d9ff?text=Global+FM+Radio+Interface)

-----

## ✨ Funkcje

- 🎵 **Pełne pasmo FM** (87.5 - 108 MHz)
- 📡 **Analizator widma w czasie rzeczywistym** z podziałką częstotliwości
- 🔍 **Inteligentny skaner automatyczny** z wykrywaniem szczytów i pauzą na sygnale
- 💾 **Pamięć stacji** z trwałym zapisem (JSON)
- 📊 **S-Meter** do monitorowania siły sygnału
- 🎚️ **Ręczna i automatyczna kontrola wzmocnienia (AGC)**
- ⏺️ **Nagrywanie audio** do plików WAV
- 🎨 **Nowoczesny ciemny interfejs** zbudowany w CustomTkinter
- ⚡ **Zoptymalizowana wydajność** dla Raspberry Pi 5

-----

## 🎯 Platforma docelowa: Raspberry Pi 5

Ten projekt został stworzony i przetestowany specjalnie dla **Raspberry Pi 5** z systemem **Raspberry Pi OS (Bookworm)**. Poniższe instrukcje instalacji są dostosowane do tej platformy i zawierają kompilację sterowników ze źródła, aby zapewnić pełną kompatybilność z RTL-SDR V4.

-----

## ⚠️ Wymagania sprzętowe (Krytyczne!)

### RTL-SDR Blog V4 - Wymagany oryginał!

**BARDZO WAŻNE:** Na rynku istnieje wiele podróbek RTL-SDR, które mogą nie działać poprawnie z tym oprogramowaniem, szczególnie z funkcją skanowania. Ten program jest zoptymalizowany pod **oryginalny RTL-SDR Blog V4**.

#### Gdzie kupić oryginał:

- 🛒 **Oficjalna lista sprzedawców:** <https://www.rtl-sdr.com/buy-rtl-sdr-dvb-t-dongles/>

#### Jak rozpoznać oryginalny V4:

- ✅ Metalowa obudowa (niebieska lub srebrna)
- ✅ Wyraźne logo “RTL-SDR Blog” na obudowie
- ✅ Złącze antenowe SMA (antena przykręcana, nie wciskana)
- ✅ Cena rynkowa: około $35-45 USD (ok. 140-180 PLN)

#### Oznaki podróbki:

- ❌ Brak logo “RTL-SDR Blog”
- ❌ Plastikowa, czarna obudowa
- ❌ Cena znacznie poniżej $25 USD
- ❌ Sprzedawca nie znajduje się na oficjalnej liście

-----

## 🚀 Instalacja (Raspberry Pi 5)

### Krok 1: Aktualizacja systemu

```bash
sudo apt update
sudo apt upgrade -y
```

### Krok 2: Instalacja sterowników RTL-SDR V4 (ze źródła)

RTL-SDR V4 wymaga specjalnych sterowników kompilowanych ze źródeł.

#### 1. Usuń stare sterowniki:

```bash
sudo apt purge -y ^librtlsdr* ^rtl-sdr*
sudo rm -rvf /usr/lib/librtlsdr* /usr/include/rtl-sdr* /usr/local/lib/librtlsdr* /usr/local/include/rtl-sdr* /usr/local/include/rtl_* /usr/local/bin/rtl_*
```

#### 2. Zainstaluj narzędzia kompilacji:

```bash
sudo apt-get install -y libusb-1.0-0-dev git cmake pkg-config build-essential
sudo apt-get install -y libportaudio2 portaudio19-dev python3-pip
```

#### 3. Pobierz i skompiluj sterowniki:

```bash
git clone https://github.com/rtlsdrblog/rtl-sdr-blog
cd rtl-sdr-blog/
mkdir build
cd build
cmake ../ -DINSTALL_UDEV_RULES=ON
make
```

#### 4. Zainstaluj sterowniki:

```bash
sudo make install
sudo ldconfig
```

#### 5. Zablokuj domyślny sterownik DVB:

```bash
echo 'blacklist dvb_usb_rtl28xxu' | sudo tee /etc/modprobe.d/blacklist-rtl-sdr.conf
```

#### 6. Restart systemu:

```bash
sudo reboot
```

### Krok 3: Sprawdzenie instalacji

Po restarcie, podłącz RTL-SDR V4 i wykonaj test:

```bash
rtl_test -t
```

**Oczekiwany wynik:** Informacje o urządzeniu (np. “Found RTL-SDR Blog V4”) i test zakończony pomyślnie.

### Krok 4: Instalacja bibliotek Python

```bash
pip install pyrtlsdr
pip install sounddevice
pip install numpy
pip install scipy
pip install customtkinter
pip install soundfile
```

-----

## 🎮 Uruchomienie

1. Upewnij się, że RTL-SDR V4 jest podłączony
1. Podłącz antenę
1. Uruchom skrypt:

```bash
python3 radio.py
```

-----

## 📖 Instrukcja obsługi

### Podstawowe sterowanie

- **▶️ START RADIO** - Uruchamia odbiornik
- **<< / >>** - Zmiana częstotliwości o ±1 MHz
- **< / >** - Zmiana częstotliwości o ±0.1 MHz
- **Pole MHz** - Bezpośrednie wprowadzanie częstotliwości

### Skaner stacji

- **Skanuj Pasmo FM ▶** - Uruchamia automatyczne skanowanie
- Skaner wykrywa stacje powyżej -35 dBm
- Automatycznie zatrzymuje się na wykrytych stacjach na 5 sekund
- Wznawia skanowanie, gdy sygnał zanika

### Zapisywanie stacji

1. Nastroić na wybraną stację
1. Wpisać nazwę w pole tekstowe
1. Kliknąć **Zapisz bieżącą**
1. Zapisane stacje pojawiają się na liście poniżej
1. Kliknięcie stacji na liście automatycznie się na nią stroi

### S-Meter

Pokazuje siłę sygnału w skali S0-S9 oraz w dBm:

- **S0-S4:** Słaby sygnał (zielony)
- **S5-S6:** Średni sygnał (pomarańczowy)
- **S7-S9:** Mocny sygnał (czerwony)

### Kontrola wzmocnienia

- **Auto Gain (AGC)** - Automatyczne dostosowanie wzmocnienia
- **Suwak Gain** - Ręczna regulacja (0-49.6 dB)

### Nagrywanie

1. Kliknąć **⏺️ RECORD** aby rozpocząć
1. Kliknąć **⏹️ STOP REC** aby zakończyć
1. Pliki zapisują się jako `recording_YYYYMMDD_HHMMSS.wav`

-----

## 🔧 Rozwiązywanie problemów

### Radio się nie uruchamia

```bash
# Sprawdź czy urządzenie jest wykryte
lsusb | grep RTL
# Powinno pokazać: "Realtek Semiconductor Corp. RTL2838..."

# Test sterownika
rtl_test -t
```

### Brak dźwięku

```bash
# Sprawdź urządzenia audio
aplay -l

# Testuj sounddevice
python3 -c "import sounddevice; print(sounddevice.query_devices())"
```

### Błędy uprawnień USB

```bash
# Dodaj użytkownika do grupy plugdev
sudo usermod -a -G plugdev $USER

# Przeloguj się lub restartuj
```

### Niska jakość sygnału

- ✅ Upewnij się, że używasz odpowiedniej anteny dla pasma FM
- ✅ Ustaw antenę pionowo
- ✅ Umieść antenę wyżej lub przy oknie
- ✅ Włącz AGC lub zwiększ Gain ręcznie
- ✅ Sprawdź, czy jesteś w zasięgu stacji FM

-----

## 📊 Specyfikacja techniczna

|Parametr                 |Wartość                    |
|-------------------------|---------------------------|
|Pasmo FM                 |87.5 - 108 MHz             |
|Częstotliwość próbkowania|288 kHz                    |
|Częstotliwość audio      |48 kHz                     |
|Demodulacja              |Wide-Band FM (WBFM)        |
|Filtr de-emphasis        |75 μs (standard FM)        |
|Zakres wzmocnienia       |0 - 49.6 dB (29 kroków)    |
|Format nagrań            |WAV (48 kHz, mono, float32)|

-----

## 📁 Struktura plików

```
.
├── radio.py              # Główny skrypt aplikacji
├── stations.json         # Zapisane stacje (tworzone automatycznie)
├── recording_*.wav       # Nagrania audio (tworzone przy nagrywaniu)
└── README.md            # Ten plik
```

-----

## 🤝 Współpraca

Zapraszamy do zgłaszania błędów i propozycji ulepszeń poprzez Issues lub Pull Requests!

-----

## 📝 Licencja

Ten projekt jest udostępniony na licencji MIT. Zobacz plik `LICENSE` po szczegóły.

-----

## 🙏 Podziękowania

- **RTL-SDR Blog** za wspaniały sprzęt i sterowniki
- **pyrtlsdr** za bibliotekę Python
- **CustomTkinter** za nowoczesne komponenty GUI
- Społeczność **Raspberry Pi** za wsparcie

-----

## 📧 Kontakt

Masz pytania? Otwórz Issue na GitHubie!

-----

**Ciesz się swoim radiem FM! 📻🎵**
