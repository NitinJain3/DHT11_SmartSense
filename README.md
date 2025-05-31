# STM32 DHT11 Sensor Threshold Alert System

This is a microcontroller-based project developed using **STM32** (bare-metal or HAL-based), interfacing with the **DHT11 Temperature & Humidity sensor**. 
It includes threshold-based alert generation and UART output for serial monitoring and data logging.

---
![License](https://img.shields.io/badge/License-MIT-brightgreen)
![Microcontroller](https://img.shields.io/badge/Microcontroller-STM32-blue)
![Board](https://img.shields.io/badge/Board-NUCLEOF44RE-blue)
![Sensor](https://img.shields.io/badge/Sensor-DHT11-orange)


![Status](https://img.shields.io/badge/Status-working-brightgreen)
![Testing](https://img.shields.io/badge/Tested-brightgreen)
![Project_status](https://img.shields.io/badge/Project-complete-Green)


---
# Setup
NuceloF446RE Board : ![IMG_20250531_150242964~2](https://github.com/user-attachments/assets/1247d097-9ce1-4f69-a4d8-fadf0909075d)

DHT11 Sensor Module : ![IMG_20250531_145828472~2](https://github.com/user-attachments/assets/3ae75542-c56b-4513-b218-d26b803c9ede)

---

## Features

- Read temperature (°C) and humidity (% RH) from DHT11
- Display real-time sensor readings over UART
- Validates sensor data using checksum
- Configure threshold values for both temperature and humidity
- Outputs data to UART using redirected `printf`
- Microsecond-level delay using DWT (Data Watchpoint and Trace Unit)
- LED toggle to indicate successful data reception
- UART output format: `Temp,Humidity,Status`
- Compatible with external Python GUI tools for PC-based monitoring

---

##  DHT11 Timing Overview

DHT11 uses a single-wire protocol with **strict timing requirements**:

### MCU Start Signal:
- LOW for ≥18 ms  
- HIGH for 20–40 µs  

### Sensor Response:
- LOW 80 µs  
- HIGH 80 µs  

### Data Transmission (40 bits total):
- Each bit starts with a 50 µs LOW pulse  
- `0` bit: 26–28 µs HIGH  
- `1` bit: ~70 µs HIGH  

**Accurate microsecond delays are mandatory** for:
- Differentiating between `0` and `1`
- Properly receiving the 5-byte data frame
- Handling the initial sensor response

This project uses **DWT-based delay** (`DWT->CYCCNT`) to achieve accurate timing.

---

##  Hardware Requirements

1.  STM32 Microcontroller (e.g., STM32F446RE)
2.  NUCLEO-F446RE development board
3.  DHT11 temperature and humidity sensor module
4.  Docklight / PuTTY / Arduino Serial Monitor (for viewing UART logs)
5.  Optional: OLED/LED/Buzzer/Fan for on-device alertS  

---

## ⚙️ Software Setup

1.  STM32CubeIDE (any preferred IDE)
2.  STM32CubeMX (for peripheral config)
3.  STM32 HAL Drivers (DWT delay, GPIO, UART)
4.  C Language (bare-metal logic)

---

##  Project Folder Structure
<pre lang="markdown"><code>``` 
  STM32_F446Re/ 
  └── dht11_reader/ 
  ├── Core/
  │ ├── Inc/ 
  │ │ ├── dht11.h 
  │ │ └── ... │ 
  ├── Src/  
  │ │ ├── dht11.c 
  │ │ ├── main.c │
  │ └── ... 
  ├── Drivers/ 
  │ └── ... 
  ├── .ioc (CubeMX project file) 
  └── README.md ``` </code></pre>

---

## Fan control logic based on thresholds
```
uint8_t current_exceeded = is_threshold_exceeded(t_int, rh_int);

if (current_exceeded != last_exceeded_state) {
  if (current_exceeded) {
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_SET);
    printf(
        "Alert! Fan ON: Temp/Humidity exceeds threshold\r\n");
  } else {
    HAL_GPIO_WritePin(GPIOA, GPIO_PIN_5, GPIO_PIN_RESET);
    printf("Alert! Fan OFF: Temp/Humidity back to normal\r\n");
  }
  last_exceeded_state = current_exceeded; // update state
}
```

---

## Debug Logging – `debug.h`

This project includes a lightweight debug interface through debug.h, which uses macro-based logging levels:

```
#define DEBUG_INFO(fmt, ...)   printf("INFO: " fmt, ##__VA_ARGS__)
#define DEBUG_PRINT(fmt, ...)  printf("DEBUG: " fmt, ##__VA_ARGS__)
#define DEBUG_ERROR(fmt, ...)  printf("ERROR: " fmt, ##__VA_ARGS__)
```

These macros help to:
1. Clearly separate **INFO**, **DEBUG**, and **ERROR** messages  
2. Keep source files clean and maintainable  
3. Easily enable or disable logs by commenting/uncommenting the macros

## 🔄 UART Output Format
<temperature>,<humidity>,<status>

Example:
25.1,60.2,Temp/Humidity under Threshold
30.5,80.0,Temp/Humidity exceeds Threshold

- `Alert Fan OFF !` = within threshold (Switch off Fan/Any active device)
- `Alert Fan ON !`  = Exceeds threshold (Switch ON Fan/Any active device)

---

## Use Case

- Ideal for real-time validation of environment conditions in embedded systems
- Can be integrated into production line testing
- Validates sensor accuracy, data consistency, and alert response

---

# Alert Output 
![image](https://github.com/user-attachments/assets/001a36b8-93df-499f-a4b6-aa4cee11c1c1)
)

# Results with Reference Temperature Meter
RH = ±10% observed(Callibration maybe required)  
Temp: ±1 Observed ( Okay)  

![IMG_20250524_012640089~2](https://github.com/user-attachments/assets/05d69988-db3c-4950-81e3-2f0995392540)

# License
This project is licensed under the MIT License. See the LICENSE file for details.

# Author
Nitin R
[LinkedIn](https://www.linkedin.com/in/nitin-r-9733n)
