# Bluetooth

## Bluetooth Classic

- High speed (~2MB)
- High range (up to 1000m)
- Considerable power consumption
- Useful for high speed applications (audio for example)

## Bluetooth Low Energy

- Low speed (~100KB)
- Low range (up to 250m)
- Low power consumption
- Useful for critical applications
- There are two stacks in the ESP-IDF
  - Bluedroid that supports:
    - Bluetooth classic and BLE
    - Audio streamming
    - Bluetooth HID devices
    - Serial UART
  - Nimble that supports:
    - Only BLE (with smaller memory footprint, runtime and simpler function calls)
    - Sensors, actuators, HID, UART
- Communication sequence:
    1. Discovery (scan/advertise)
    2. Connecting (connection request/response)
    3. Connected (read/write/notify data)

## Bluetooth Stack

- GAP (General Access Profile): Answer the question on "how to connect?"
- GATT (Generic ATTribute): Ansewer the question on "how to transport data? how data is flowing?"
- L2CAP (Logic Link Control & Adaption Protocol): assemble and disassemble packets

1. For Bluetooth using the Bluedroid API, the docs can be easily found in the ESP-IDF documentation
2. For Bluetooth using the Nimble API, the docs can be found [clicking in this url](https://mynewt.apache.org/latest/network/index.html)
