# Sleeping

1. Sleep States
    - Full Power: All peripherals, CPUs, RTC and devices powered
      - *There is also a modem sleep for the WiFi that consumes at maximum ~20mA
    - Light Sleep: Only peripherals, WiFi (maximum 260mA) and Bluetooth disabled (~0.8mA)
    - Deep Sleep: Similar to the light sleep but also disable the CPU cores, keeping only the coprocessor and the RTC
      - With the coprocessor enabled ~0.15mA
      - If monitoring the I2C bus ~25uA (ULP - ultra low-power coprocessor)
      - With the RTC tick and memory running ~10uA
    - Hibernation: Only count RTC ticks with an ~2.5uA of consumption

2. Wake Up Modes
    - Timers
    - Touch sensor/pad
    - External wakeup similar to a GPIO interrupt
    - ULP ADC or I2C
    - GPIO wakeu (only available for Light Sleep mode)
    - UART wakeup (only available for Light Sleep mode)
    - Library `sys/time.h` used to get how long the ESP is running for
    - Library `esp_sleep.h` used to get the ESP in the sleep states

3. Light Sleep
    - To setup the sleep period: `esp_sleep_enable_timer_wakeup(uint64_t time_us)`
    - To get how long the ESP is running in us: `esp_timer_get_time()`
    - To avoid start a sleep operation while printing something to the UART use `uart_tx_wait_idle(CONFIG_ESP_CONSOLE_UART_NUM)`. Note that this is a blocking call
    - To use GPIOs to wakeup the device, include `driver/rtc_io.h`
      - Then, configure the GPIO as described in [GPIO Docs](./GPIO.md)
      - Configure the GPIO to wakeup the ESP `gpio_wakeup_enable(<gpio_pin>, <low_level|high_level|both_levels>)`
      - Enable the GPIO wakeup operation `esp_sleep_gpio_enable_wakeup()`
      - The wakeup cause (timeout or GPIO) can be get with `esp_sleep_get_wakeup_cause()`, then be validated
      - GPIO values can be read while the ESP is in the light sleep mode with `rtc_gpio_get_level(<gpio_pin>)`

4. Deep Sleep
    - With deep sleep the ESP reboots everytime when wakeup
    - Static variables and other things are lost
    - To save these values, that variables must be stored in the RTC memory with the prefix RTC_DATA_ATTR attribute, example: `static RTC_DATA_ATTR int non_volatile_int`
    - The ESP can be woken up from timer calls or external interrupts
