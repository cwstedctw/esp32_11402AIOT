# ESP32 MicroPython Project (11402 AIOT)

This project is set up for ESP32 MicroPython development with VS Code, uv, mpremote, and esptool.

## 快速開始（推薦）

### 1. 安裝 uv

```powershell
powershell -ExecutionPolicy ByPass -c "irm https://astral.sh/uv/install.ps1 | iex"
```

### 2. 一鍵環境檢查 + 自動修復

```powershell
powershell -ExecutionPolicy Bypass -File tools\check_env.ps1 -Fix
```

這個指令會自動：
- 檢查 Python（3.10+）+ uv 是否安裝
- 用 `uv sync` 安裝套件（mpremote, esptool）
- 偵測並自動安裝 USB 驅動（CP210x / CH340 / FTDI）
- 偵測 ESP32 COM port（多板子智慧選擇）
- 下載並燒錄 MicroPython 韌體（失敗自動換 port + BOOT 模式重試）

不加 `-Fix` 只檢查不修改：
```powershell
powershell -ExecutionPolicy Bypass -File tools\check_env.ps1
```

### 3. VS Code 快捷任務

按 `Ctrl+Shift+P` → 輸入 "Tasks: Run Task"：

| 任務 | 說明 |
|------|------|
| ESP32: 環境檢查 | 檢查開發環境是否正確 |
| ESP32: 環境設定 (自動修復) | 自動安裝套件、驅動、韌體 |
| ESP32: Deploy | 上傳 boot.py / main.py / lib/ |
| ESP32: Run current MicroPython file | 掛載專案目錄並執行目前焦點的 .py（預設 `test_ir_oled.py`） |
| ESP32: Soft reset | 軟重啟 ESP32 |
| ESP32: Serial monitor | 開啟序列埠監控（mpremote repl） |

> 提示：跑任何 mpremote 指令前要確保 COM3 沒被佔用（關掉 Serial Monitor、其他 REPL 視窗）。

---

## 手動操作（進階）

### 同步套件

```powershell
uv sync
```

檢查工具：

```powershell
uv run python --version
uv run mpremote --help
uv run esptool version
```

### 找 ESP32 Port

```powershell
uv run mpremote connect list
```

### Run Without Writing to Flash

```powershell
uv run mpremote connect COM3 run test_led.py
uv run mpremote connect COM3 run test_sensor.py
uv run mpremote connect COM3 run test_wifi.py
```

### Mount Local Folder

```powershell
uv run mpremote connect COM3 mount . + run main.py
```

### Deploy to ESP32

```powershell
powershell -ExecutionPolicy Bypass -File tools/deploy.ps1 -Port COM3
```

### Flash MicroPython Firmware（手動）

```powershell
powershell -ExecutionPolicy Bypass -File tools/flash.ps1 -Port COM3 -Firmware .\firmware\ESP32_GENERIC-20260406-v1.28.0.bin
```

> 提示：`tools\check_env.ps1 -Fix` 已包含自動下載 + 燒錄，建議優先使用。

## IR 紅外線遙控

`ir.py` 為 NEC 協定純 Python 解碼器（GPIO `33`），三種模式：

- `capture_keys()`：引導使用者按下指定鍵錄製 raw code
- `monitor_keys()`：持續解碼並印出
- `confirm_all_keys()`：對照已知按鍵，按一次印一次（目前 `main()` 預設執行）

`test_ir_oled.py` 整合 IR + OLED：按下遙控器後，OLED 會顯示按鍵名稱、raw code、address / command、累計次數。預設用 `Run current MicroPython file` task 即可跑起來。

`tools/record-ir.ps1` 跑 `ir.py` 並把終端輸出 Tee 到 `ir_capture.txt`，方便事後比對波形。

## Secrets

Copy `config.example.py` to `config.py`, then edit Wi-Fi settings. `config.py` is ignored by Git.
