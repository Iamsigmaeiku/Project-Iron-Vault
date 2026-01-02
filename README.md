# Cold wallet side project
My first STM32 Cold Wallet
## 1. Hardware Layer
### 1.1 MCU
- **Model**: STM32F411CEU6 (Black Pill)
- **Architecture**: ARM Cortex-M4 with FPU
- **Clock Tree**
    - **HSE**: 25MHz External Crystal (System Reference)
    - **LSI**: 32kHz (For Independent Watchdog - IWDG)
    - **SYSCLK**: 96MHz (via PLL Multiplication)
- **Debug Interface**: SWD (Serial Wire Debug)
    - **SWCLK**: PA14
    - **SWDIO**: PA13
- **Energy Management**
    - **Input**: USB 5V
    - **Logic Voltage**: 3.3V (LDO)

### 1.2 Display Module
- **Hardware**: SSD1306 OLED (0.96 inch)
- **Communication Protocol**: I2C (Inter-Integrated Circuit)
    - **Interface**: I2C1
    - **SCL**: PB6
    - **SDA**: PB7
- **Resolution**: 128 x 64 pixels

### 1.3 User Input
- **Button A (Select)**: GPIO Input (e.g., PA0)
- **Button B (Confirm)**: GPIO Input (e.g., PA1)
- **Circuit Design**: Pull-down Resistor or Internal Pull-up Input

### 1.4 Connectivity
- **Physical Interface**: USB Type-C
- **USB Protocol**: USB 2.0 Full Speed (12Mbps)
- **Role**: Device (Slave)

## 2. Firmware Layer
### 2.1 Low-Level Drivers (HAL)
- **GPIO**: Digital Input/Output Control
- **I2C**: Block Transfer (DMA or Polling)
- **RNG**: Hardware Random Number Generator
- **FLASH**: Internal Flash Read/Write (EEPROM Emulation)
- **USB_OTG_FS**: USB Hardware Layer Driver
- **TIM/SysTick**: System Timer

### 2.2 Middleware
- **Graphics Library**
    - Fonts (5x7, 8x16)
    - Frame Buffer
    - Primitives (Pixel, Line, Rect, Bitmap)
- **Input Manager**
    - Debouncing Logic (Software, ~20ms)
    - Event Triggering (Single Click, Long Press)
- **USB Class**
    - HID (Human Interface Device)
- **Cryptography Library**
    - SHA-256 / SHA-512
    - BigNum Math
    - secp256k1 (Elliptic Curve)

### 2.3 Application Logic (FSM)
- **State: BOOT**
    - System Initialization (HAL_Init)
    - Peripherals Self-Test (POST)
- **State: IDLE / MENU**
    - UI Rendering
    - Wait for Interrupts (Button Presses)
- **State: WALLET_GEN**
    - Entropy Gathering
    - BIP-39 Mnemonic Generation
- **State: KEY_DERIVATION**
    - Seed Calculation (PBKDF2)
    - Private/Public Key Derivation (BIP-32/44)
- **State: ADDRESS_DISP**
    - Base58 Encoding
    - Address & QR Code Display (Bitmap)

## 3. Cryptography Layer
### 3.1 Entropy Sources
- **Source A**: TRNG (True Random Number Generator)
- **Source B**: Floating Analog Pin Noise
- **Source C**: User Input Timing Jitter
- **Processing**: Entropy Pool Mixing (via SHA-256)

### 3.2 Standards
- **BIP-39**: Mnemonic Codes
    - 2048 Wordlist
    - Checksum Calculation
- **BIP-32**: Hierarchical Deterministic (HD) Wallet
    - Master Extended Key (xprv/xpub)
- **BIP-44**: Multi-Account Hierarchy
    - Path: m / 44' / 0' / 0' / 0

### 3.3 Signing & Hashing
- **Hash Functions**: SHA-256, RIPEMD-160
- **Signature**: ECDSA (secp256k1 curve)

## 4. Data Storage
### 4.1 Volatile Memory (RAM)
- **Stack**: Function Calls & Local Variables
- **Secure Buffer**: Temporary Private Key Storage (Immediate Zero-fill Overwrite after use)

### 4.2 Non-Volatile Memory (Flash)
- **Code Segment**: Firmware Code (Read-only)
- **Data Segment**:
    - Encrypted Seed
    - PIN Code Hash
    - Device Settings

## 5. Development Environment & Tools (DevOps)
### 5.1 Software Tools
- **IDE**: STM32CubeIDE
- **Configurator**: STM32CubeMX (.ioc)
- **Debugger**: OpenOCD / ST-Link Server
- **Version Control**: Git

### 5.2 Hardware Tools
- **Programmer**: ST-Link V2
- **Connections**: Breadboard & Jumper Wires
- **Measurement**: Multimeter
