# I2C & SPI & UART

1. Bluetooth Logging
    - This can be done using an external JDY-31 bluetooth serial logger
    - To use it, the JDY-31 baud rate should be changed from 9600 (default) to 115200 (ESP-IDF default), or the project baud rate must be changed to 9600 using `idf.py menuconfig -> Serial Flasher Config -> 'idf.py monitor' baud rate`
    - Then update the `Common ESP Related` component in `idf.py menuconfig` changing the `UART to console output` to `custom` using the `UART1`
    - Set the GPIOs correctly and the `UART console baud rate` to 9600
    - That way, the default UART0 will be used to flash the firmware and the UART1 for logging
    - Since the logger is now been sent to the JDY-31 bluetooth adapter, the IDF monitor should also be used there
    - To monitor using the new COM port (or /dev/tty) use: `idf.py -p </dev/ttyNEWPORT> monitor` where /dev/ttyNEWPORT is the serial output port created by the JDY-31

2. UART
    - To use the uart functions include `driver/uart.h`
    - The RX and TX pins can be specified inside the code, configuring the serial ports
    - There are 3 UARTS that can be used (typically) and the UART0 is commonly used to Flashing and Monitoring the device
    - All the UART stuff resumes to a boiler plate to:
      - Configure the UART with `uart_config_t` and `uart_param_config()`
      - Set the GPIOs `uart_set_pin()`
      - Install the driver `uart_driver_install()`
      - Then write with `uart_write_bytes()` and read witj `uart_read_bytes()`. Note the the reading operation is a timed function call
    - FreeRTOS Queues can be used to control the UART messages
      - Patterns can generate interrupts with `uart_enable_patter_det_intr()`
      - Queues can be use to generate a lot of events about the UART data transmission
      - To use that approach a task that will receive these queue events must exist

3. I2C
    - To use the I2C include `driver/i2c.h`
    - ESP32 has two I2C buses
    - The SDA and SCL pins are configurable through the menuconfig
    - I2C examples can be found in the ESP-IDF folder, in the peripherals subdirectory
    - There is an example that brings up a serial console to manipulate the I2C devices `i2c_tools`
    - The I2C SDA and SCL lines needs pullup resistors that can be used when configuring the lines in the `i2c_config_t` structure

4. SPI
    - Requires 4 data lines
    - Is much faster than I2C
    - It needs one extra GPIO for each slave device to act as the chip selector (or slave selector). Normally the slave selector is always high, when talking to a device that pins goes low
    - The SPI is mostly used for SD Cards and Displays
    - Recommendations for display libraries GUI: u8g2, lgvl
