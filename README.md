# Damai Ticket Purchase Automation System

An automated ticket-purchasing tool for Damai, based on Selenium and Appium, supporting both Web and Mobile ticket purchase.

## 🚀 Features

- **Dual-end support**: Web (Selenium) and Mobile (Appium)
- **Smart ticketing**: Automatically selects city, price, and attendees
- **High performance**: Optimized click strategy for ticket-grabbing scenarios
- **Configurable**: Flexible config files for various events
- **Retry mechanism**: Built-in retry logic to improve success rate

## 📋 System Requirements

### Base environment

- **Python**: 3.9+
- **Node.js**: 20.19.0+ or 22.12.0+ or 24.0.0+
- **OS**: macOS / Windows / Linux

### Web ticketing

- **Chrome browser**: Latest version
- **ChromeDriver**: Auto-downloaded

### Mobile ticketing

- **Android SDK**: Environment variables configured
- **Appium**: 3.1.0+
- **Android device**: Real device or emulator

## 🛠️ Installation Guide

### 1. Clone the project

```bash
git clone <repository-url>
cd ticket-purchase
```

### 2. Install Python dependencies

```bash
# Using Poetry (recommended)
poetry install

# Or using pip
pip install -r requirements.txt
```

### 3. Mobile environment setup (only needed for mobile ticketing)

#### 3.1 Install Node.js

```bash
# macOS (Homebrew)
brew install node

# Verify version (must be 20.19.0+)
node --version
```

#### 3.2 Install Appium

```bash
# Install Appium globally
npm install -g appium

# Install UiAutomator2 driver
appium driver install uiautomator2

# Verify installation
appium --version
```

#### 3.3 Configure Android environment

```bash
# Set environment variables (add to ~/.zshrc or ~/.bashrc)
export ANDROID_HOME=/path/to/your/android/sdk
export ANDROID_SDK_ROOT=/path/to/your/android/sdk

# Verify ADB
adb devices
```

## ⚙️ Configuration

### Mobile config (`config.jsonc`)

```json
{
  "server_url": "http://127.0.0.1:4723",
  "keyword": "Liu Ruoying",
  "users": [
    "Attendee1",
    "Attendee2"
  ],
  "city": "Quanzhou",
  "date": "10.04",
  "price": "799元",
  "price_index": 1,
  "if_commit_order": true
}
```

> You can keep Chinese values for `keyword`, `city`, `users`, etc. if they better match the Damai UI; only the explanations here are translated.

#### Config parameter description

| Parameter | Type | Description | Example |
|----------|------|-------------|---------|
| `server_url` | string | Appium server URL | `"http://127.0.0.1:4723"` |
| `keyword` | string | Search keyword | `"刘若英"` |
| `users` | array | Attendee list | `["Zhang San", "Li Si"]` |
| `city` | string | Event city | `"泉州"` |
| `date` | string | Event date | `"10.04"` |
| `price` | string | Price label text | `"799元"` |
| `price_index` | number | Price index (0-based) | `1` |
| `if_commit_order` | boolean | Auto submit order or not | `true` |

### Web config (`config.json`)

```json
{
  "index_url": "https://www.damai.cn/",
  "login_url": "https://passport.damai.cn/login",
  "target_url": "https://detail.damai.cn/item.htm?id=xxx",
  "users": ["Zhang San", "Li Si"],
  "city": "Guangzhou",
  "date": "2023-10-28",
  "price": "1039",
  "if_commit_order": true
}
```

## 🚀 Usage

### Mobile ticketing (recommended)

#### 1. Start Android device

```bash
# Start emulator
/Users/shengwang/Library/Android/sdk/emulator/emulator -avd YourAVDName

# Or connect real device (USB debugging enabled)
adb devices
```

#### 2. Install Damai app

Install the Damai app on the Android device and log in with your account.

#### 3. Start Appium server

```bash
# Set environment variables
export ANDROID_HOME=/Users/shengwang/Library/Android/sdk
export ANDROID_SDK_ROOT=/Users/shengwang/Library/Android/sdk

# Start Appium server
appium --port 4723
```

#### 4. Configure ticketing parameters

Edit `damai_appium/config.jsonc` and set:

- Search keyword  
- Attendees  
- City, date, price  
- Other options

#### 5. Run the mobile ticketing script

```bash
cd damai_appium
ANDROID_HOME=/Users/shengwang/Library/Android/sdk ANDROID_SDK_ROOT=/Users/shengwang/Library/Android/sdk python damai_app_v2.py
```

### Web ticketing

#### 1. Configure parameters

Edit `damai/config.json` and set the target event URL and other options.

#### 2. Run the Web ticketing script

```bash
cd damai
python damai.py
```

## 🔧 Troubleshooting

### Common issues

#### 1. Node.js version incompatible

```text
Error: Node version must be at least ^20.19.0 || ^22.12.0 || >=24.0.0
```

**Solution**: Upgrade Node.js to a compatible version.

```bash
# macOS
brew upgrade node
```

#### 2. Android environment variables not set

```text
Error: Neither ANDROID_HOME nor ANDROID_SDK_ROOT environment variable was exported
```

**Solution**: Set the environment variables.

```bash
export ANDROID_HOME=/path/to/android/sdk
export ANDROID_SDK_ROOT=/path/to/android/sdk
```

#### 3. Device connection issue

```text
Error: Unable to find an active device or emulator
```

**Solution**:

- Check device connection: `adb devices`  
- Make sure USB debugging is enabled  
- Verify Android version compatibility  

#### 4. Appium connection failed

```text
Error: Connection refused
```

**Solution**:

- Ensure Appium server is running  
- Check if port 4723 is in use  
- Verify the server URL in your config  

### Debugging tips

#### 1. Check device status

```bash
# List connected devices
adb devices

# Check Android version
adb shell getprop ro.build.version.release

# Check if device is fully booted
adb shell getprop sys.boot_completed
```

#### 2. Verify Appium status

```bash
# Check Appium server status
curl http://127.0.0.1:4723/status
```

#### 3. View app package name

```bash
# List installed packages (filter for Damai)
adb shell pm list packages | grep damai
```

## 📁 Project Structure

```text
ticket-purchase/
├── damai/                    # Web ticketing
│   ├── damai.py              # main script
│   ├── config.py             # config classes
│   ├── config.json           # config file
│   └── requirements.txt      # dependencies
├── damai_appium/             # Mobile ticketing
│   ├── damai_app_v2.py       # optimized main script
│   ├── damai_app.py          # original main script
│   ├── config.py             # config classes
│   ├── config.jsonc          # config file
│   └── app.md                # app notes
├── tests/                    # tests
├── doc/                      # docs
├── img/                      # images
└── README.md                 # this document
```

## 🎯 End-to-end Mobile Ticketing Flow

1. **Environment preparation**  
   - Install Node.js (20.19.0+)  
   - Install Appium and drivers  
   - Configure Android SDK environment variables  

2. **Device preparation**  
   - Start Android emulator or connect a real device  
   - Install Damai app and log in  

3. **Configure parameters**  
   - Edit `config.jsonc`  
   - Set event info, attendees, etc.  

4. **Start services**  
   - Start Appium server  
   - Verify device connection  

5. **Run ticketing**  
   - Open Damai app on the device/emulator  
   - Search the target event  
   - Run the ticketing script  

6. **Monitor result**  
   - Script runs the ticketing flow automatically  
   - Watch console output  
   - Check order status in the app  

## ⚠️ Notes

1. **Legal use**: Please follow Damai’s terms of use and use automation reasonably.  
2. **Account safety**: Prefer using a dedicated account for testing.  
3. **Network**: Ensure a stable network connection.  
4. **Device performance**: A higher-performance device is recommended.  
5. **Timing**: Configure and start the script ahead of the actual sale time.  

## 🤝 Contributing

Issues and Pull Requests are welcome to help improve this project.

## 📄 License

This project is for learning and research only. Do not use it for commercial purposes.

---

**Last updated**: October 2024  
**Version**: 2.0.0