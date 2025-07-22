# Ford OBD2 Smart Gauge - Technical Summary

## 📋 **Libraries และ Dependencies**

### Core Libraries

- `Arduino.h` - ESP32 Arduino framework
- `math.h` - คำนวณทางคณิตศาสตร์
- `FreeRTOS` (`freertos/FreeRTOS.h`, `freertos/task.h`, `freertos/semphr.h`) - Real-time operating system
- `esp_task_wdt.h` - Watchdog timer
- `BLEDevice.h` - Bluetooth Low Energy
- `Preferences.h` - Non-volatile storage
- `lvgl.h` - GUI framework (v8.3.9)

### Custom Headers

- `esp_bsp.h` - Board Support Package
- `display.h` - Display control
- `lv_port.h` - LVGL port configuration

## 🔌 **การเชื่อมต่อ Hardware**

### ESP32-S3 JC3248W535 Pinout

```c
#define BSP_I2C_NUM                     (I2C_NUM_0)
#define BSP_I2C_CLK_SPEED_HZ            400000

// QSPI Display Pins
#define EXAMPLE_PIN_NUM_QSPI_CS         (GPIO_NUM_45)
#define EXAMPLE_PIN_NUM_QSPI_PCLK       (GPIO_NUM_47)
#define EXAMPLE_PIN_NUM_QSPI_DATA0      (GPIO_NUM_21)
#define EXAMPLE_PIN_NUM_QSPI_DATA1      (GPIO_NUM_48)
#define EXAMPLE_PIN_NUM_QSPI_DATA2      (GPIO_NUM_40)
#define EXAMPLE_PIN_NUM_QSPI_DATA3      (GPIO_NUM_39)
#define EXAMPLE_PIN_NUM_QSPI_DC         (GPIO_NUM_8)
#define EXAMPLE_PIN_NUM_QSPI_TE         (GPIO_NUM_38)
#define EXAMPLE_PIN_NUM_QSPI_BL         (GPIO_NUM_1)
```

### Display Specifications

- **Resolution:** 320x480 pixels
- **Color Format:** RGB565 (16-bit color)
- **Interface:** QSPI
- **Buffer Size:** 307,200 bytes (full screen)

## 📡 **OBD2 Communication Protocol**

### BLE Service/Characteristics

```c
BLEUUID serviceUUID("0000fff0-0000-1000-8000-00805f9b34fb");
BLEUUID txCharUUID("0000fff2-0000-1000-8000-00805f9b34fb");
BLEUUID rxCharUUID("0000fff1-0000-1000-8000-00805f9b34fb");
```

### ELM327 Initialization Sequence

```
ATZ    - Reset (5000ms timeout)
ATE0   - Echo off (1000ms timeout)
ATL0   - Line feeds off (1000ms timeout)
ATH0   - Headers off (1000ms timeout)
ATS0   - Spaces off (1000ms timeout)
ATSP0  - Auto protocol selection (3000ms timeout)
0100   - Test PID support (8000ms timeout)
```

### Supported Adapters

- VeePeak BLE+ (รองรับ auto-detection)
- ELM327 compatible adapters
- Automatic device scanning by name patterns: "OBD", "VEEPEAK", "VGATE"

## 🧮 **สูตรการคำนวณและ PID Formulas**

### Standard OBD2 PIDs

#### Engine RPM (PID 010C)

```c
// Input: 2 bytes (A, B)
rpm = ((256 * A) + B) / 4
// Validation: 0 ≤ rpm ≤ 8000
```

#### Coolant Temperature (PID 0105)

```c
// Input: 1 byte (A)
temp_celsius = A - 40
// Validation: 0 < A < 255
```

#### Vehicle Speed (PID 010D)

```c
// Input: 1 byte (A)
speed_kmh = A
// Validation: 0 ≤ A ≤ 255
```

#### Engine Load (PID 0104)

```c
// Input: 1 byte (A)
load_percent = A * 100.0 / 255.0
// Validation: 0.0 ≤ load ≤ 100.0
```

#### Intake Air Temperature (PID 010F)

```c
// Input: 1 byte (A)
iat_celsius = A - 40
// Validation: 0 < A < 255
```

#### MAP Pressure (PID 010B)

```c
// Input: 1 byte (A)
map_kpa = A
// Validation: 0 < A ≤ 255
```

#### Barometric Pressure (PID 0133)

```c
// Input: 1 byte (A)
baro_kpa = A
// Validation: 0 < A ≤ 255
```

### Ford-Specific PIDs

#### ATF Temperature (PID 221674)

```c
// Input: 2 bytes (A, B)
raw_value = A * 256 + B
atf_temp = (raw_value * 5.0 / 72.0) - 18.0
// Validation: -40.0 ≤ temp ≤ 200.0
```

#### Ford Barometric Pressure (PID 221127)

```c
// Ford-specific barometric pressure reading
```

#### Ford IAT Voltage (PID 22114A)

```c
// Ford-specific intake air temperature voltage
```

#### Ford ECT Voltage (PID 22114D)

```c
// Ford-specific engine coolant temperature voltage
```

### Boost Pressure Calculation

```c
struct BoostData {
    float mapPressure;           // kPa from MAP sensor
    float barometricPressure;    // kPa from barometric sensor
    float boostPressureKpa;      // Calculated boost pressure
    float boostPressurePsi;      // PSI conversion
    bool isValidMAP;             // MAP sensor validity flag
    bool isValidBaro;            // Barometric sensor validity flag
    bool useEstimation;          // Estimation vs real sensors
    uint32_t lastUpdate;         // Last update timestamp
};

// Calculation Formula
boostPressureKpa = mapPressure - barometricPressure;
boostPressurePsi = boostPressureKpa * 0.145038;  // kPa to PSI conversion
```

## ⚡ **FreeRTOS Task Architecture**

### Task Configuration

```c
// BLE Task: Core 0, Priority 1, Stack 10KB
xTaskCreatePinnedToCore(bleTask, "BLE_Task", 10240, NULL, 1, &bleTaskHandle, 0);

// OBD Task: Core 1, Priority 2, Stack 12KB  
xTaskCreatePinnedToCore(obdTask, "OBD_Task", 12288, NULL, 2, &obdTaskHandle, 1);

// LVGL Task: Built-in with BSP, Core 1, Priority 4, Stack 8KB
bsp_disp_cfg.lvgl_port_cfg.task_priority = 4;
bsp_disp_cfg.lvgl_port_cfg.task_stack = 8192;
bsp_disp_cfg.lvgl_port_cfg.task_affinity = 1;
bsp_disp_cfg.lvgl_port_cfg.timer_period_ms = 5;
```

### Timing Parameters

```c
interCommandDelay = 25ms        // PID request interval (40Hz)
displayUpdateRate = 30ms        // GUI refresh rate (33Hz)
bleKeepaliveInterval = 500ms    // BLE connection monitoring
uiUpdateInterval = 100ms        // UI status update rate
```

### Synchronization Objects

```c
SemaphoreHandle_t dataMutex;        // Protects vehicle data
SemaphoreHandle_t uiMutex;          // Protects UI state
SemaphoreHandle_t responseMutex;    // Protects BLE responses
SemaphoreHandle_t connectionMutex;  // Protects connection state
```

## 🔧 **เทคนิคเพิ่มประสิทธิภาพ**

### Memory Management

```c
// LVGL Configuration
#define LV_MEM_CUSTOM 1
#define LV_COLOR_DEPTH 16
#define LV_COLOR_16_SWAP 1
#define LV_COLOR_SCREEN_TRANSP 1

// Buffer Configuration
buffer_size = EXAMPLE_LCD_QSPI_H_RES * EXAMPLE_LCD_QSPI_V_RES; // Full screen buffer
```

### Data Validation Strategy

```c
// Universal validation pattern
if (isfinite(value) && value >= min_threshold && value <= max_threshold) {
    // Accept and store data
    sensor_data = value;
    is_valid_flag = true;
    last_update = millis();
} else {
    // Reject invalid data, keep previous value
    Serial.println("Data rejected - out of range or invalid");
}
```

### Connection Management

```c
// Auto-reconnection logic
if (!deviceConnected) {
    if (savedAdapterAddress != "") {
        // Try saved adapter first
        connectToServer();
    } else {
        // Scan for new adapters
        doScan = true;
    }
}

// Connection state monitoring
void keepBLEAlive() {
    static unsigned long lastActivity = 0;
    if (millis() - lastActivity > BLE_KEEPALIVE_INTERVAL) {
        // Check connection health
        lastActivity = millis();
    }
}
```

## 📊 **Display System**

### LVGL Configuration

```c
// Color and rendering settings
#define LV_COLOR_DEPTH 16               // 16-bit color
#define LV_COLOR_16_SWAP 1              // Byte swapping for SPI
#define LV_COLOR_SCREEN_TRANSP 1        // Transparency support
#define LV_COLOR_MIX_ROUND_OFS 0        // Color mixing rounding

// Display buffer
bsp_display_cfg_t bsp_disp_cfg = {
    .lvgl_port_cfg = ESP_LVGL_PORT_INIT_CONFIG(),
    .buffer_size = EXAMPLE_LCD_QSPI_H_RES * EXAMPLE_LCD_QSPI_V_RES,
    .rotate = LV_DISP_ROT_NONE
};
```

### UI Update Strategy

```c
// Thread-safe display updates
if (bsp_display_lock(timeout_ms)) {
    // Update LVGL objects
    lv_label_set_text_fmt(label, "Value: %d", data);
    lv_obj_set_style_text_color(label, color, 0);
    bsp_display_unlock();
}

// Color-coded parameters
lv_obj_set_style_text_color(rpmLabel, lv_color_hex(0xFFFFFF), 0);      // White
lv_obj_set_style_text_color(coolantTempLabel, lv_color_hex(0x00FF00), 0); // Green
lv_obj_set_style_text_color(vehicleSpeedLabel, lv_color_hex(0x00FFFF), 0); // Cyan
lv_obj_set_style_text_color(engineLoadLabel, lv_color_hex(0xFFFF00), 0);  // Yellow
lv_obj_set_style_text_color(boostPsiLabel, lv_color_hex(0x80FF80), 0);    // Light Green
```

### Screen Layout

```c
// Data display positioning
int y_pos = 60;                 // Start position
int line_height = 35;           // Spacing between lines

// Labels positioned with consistent spacing
lv_obj_set_pos(label, 20, y_pos);
y_pos += line_height;
```

## 🚀 **Performance Optimizations**

### Communication Speed

- **BLE MTU:** 247 bytes for larger packet transfers
- **Command Interval:** 25ms (reduced from 50ms for 2x speed improvement)
- **Response Timeout:** Variable timeouts based on command type

### Memory Efficiency

- **Heap Monitoring:** `ESP.getFreeHeap()` and `ESP.getFreePsram()`
- **Stack Usage:** Optimized task stack sizes
- **Buffer Management:** Single full-screen LVGL buffer

### Real-time Performance

- **Task Priorities:** BLE (1) < OBD (2) < LVGL (4)
- **Core Affinity:** BLE on Core 0, OBD/LVGL on Core 1
- **Interrupt Handling:** BLE notifications with mutex protection

## 🔍 **Debugging และ Monitoring**

### Serial Debug Output

```c
// Comprehensive logging system
Serial.printf("CMD: %s | RSP: %s\n", command, response);
Serial.printf("RPM: hex=%s%s, a=%d, b=%d, calc=%d", hexA, hexB, a, b, rpm);
Serial.printf("Free heap: %d bytes\n", ESP.getFreeHeap());
```

### Status Monitoring

```c
// Real-time status updates
void updateConnectionStatus(const char* status);
void updateScanProgress(int progress);
void addLogMessage(const String& message);
```

## 📈 **Scalability และ Extension Points**

### Additional PIDs

- เพิ่ม PID ใหม่ได้ง่ายใน `parsePIDResponse()` function
- รองรับทั้ง Standard และ Manufacturer-specific PIDs

### Display Enhancements

- สามารถเพิ่ม screens ใหม่ด้วย LVGL
- รองรับ touch input และ interactive elements

### Communication Protocols

- ขยายรองรับ WiFi OBD adapters
- รองรับ direct CAN bus connection

---

**หมายเหตุ:** เอกสารนี้สรุปเทคนิคและรายละเอียดสำคัญที่สามารถนำไปใช้ศึกษาหรือพัฒนาต่อยอดโครงการ Ford OBD2 Smart Gauge ได้
