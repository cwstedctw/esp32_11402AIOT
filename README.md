# ESP32 MicroPython Project

This project is set up for ESP32 MicroPython development with VS Code, uv, mpremote, and esptool.

## First Setup

Install uv, then sync the project environment:

```powershell
uv sync
```

Check tools:

```powershell
uv run python --version
uv run mpremote --help
uv run esptool version
```

## Find ESP32 Port

```powershell
uv run mpremote connect list
```

The examples below use `COM3`. Replace it with your actual port.

## Run Without Writing to Flash

```powershell
uv run mpremote connect COM3 run test_led.py
uv run mpremote connect COM3 run test_sensor.py
uv run mpremote connect COM3 run test_wifi.py
```

## Mount Local Folder

```powershell
uv run mpremote connect COM3 mount . + run main.py
```

## Deploy to ESP32

```powershell
powershell -ExecutionPolicy Bypass -File tools/deploy.ps1 -Port COM3
```

## Flash MicroPython Firmware

Download an `ESP32_GENERIC` firmware `.bin` file from MicroPython first, then run:

```powershell
powershell -ExecutionPolicy Bypass -File tools/flash.ps1 -Port COM3 -Firmware .\firmware\ESP32_GENERIC-20260406-v1.28.0.bin
```

## Secrets

Copy `config.example.py` to `config.py`, then edit Wi-Fi settings. `config.py` is ignored by Git.
