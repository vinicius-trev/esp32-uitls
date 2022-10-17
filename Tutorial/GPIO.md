# GPIO

1. All ESP32 pins should not exceed 20mA each
2. The sum of currents for all GPIOs pins should not exceed 40mA
3. MOSFET recommendation for ESP32: LML2502 (Vgs 1.2V and 1.3A maximum current)
4. When using a GPIO, it is a good practice to select the desired pin `gpio_pad_select_gpio(GPIO_PIN)` and set its direction `gpio_set_direction(GPIO_PIN, GPIO_MODE)`
    - When using a switch, if the ground pad isn't connected to anything (only a GPIO for example) it will be an open-circuit, floating its value
    - To fix it, there's the need of a pull-down resistor, that can be set as an internal resistor for the ESP with `gpio_pulldown_en(GPIO_PIN)`, pushing the line do LOW when the circuit is opened
    - The same can be applied with  pull-up resistor `gpio_pullup_en(GPIO_PIN)` pushing the line to HIGH when the circuit is opened
    - To disable a pull-up `gpio_pullup_dis(GPIO_PIN)` and for the pull-down `gpio_pulldown_dis(GPIO_PIN)`

    Note: When using a pull-up or pull-down resistor, it is always a good practice, to avoid miss-behaviors, to enable pull-down and disable pull-up for example, or vice-versa
5. The most simpler use-case is to loop (poolling) through a GPIO pin to check its value and perform an action, but this is not an efficient method, since the CPU is consumed looping doing nothing
6. To do a more efficient method interrupts can be used to perform an action:
    - To set a GPIO to perform an action with interrupt, the interrupt type needs to be setup with `gpio_set_inter_type(GPIO_PIN, GPIO_INTER_XXXX)`
    - Then the interrupt servcice must be enabled `gpio_install_isr_service(<flags>)`
    - Finally, register the interrupt routine `gpio_isr_handler_add(GPIO_PIN, <isr_routine>, <isr_parameters>)`
    - The interrupt functions has a quirky signature: `static void IRAM_ATTR isr_routine(void * parameters)`
      - The IRAM_ATTR tells the compiler to run the code from the DRAM (dynamic RAM) and not SRAM (standard RAM)
      - The interrupt must be simple and fast
      - Try to get out as fast as you can, without doing many logic inside the interrupt handler
      - For example, the interrupt could put the received values inside a queue, to avoid perform logic inside it, that will the treated inside a task
      - When using FreeRTOS calls inside interrupts, the FromISR must be used because the code is being executed in the DRAM
7. Debounce:
    - When working with switchs, a lot os miss-actions can be executed because of a phenomenal called bouncing
    - Based on the previous example, the bouncing ocurrence will be calling the interrupt routine erroneuss many times, to fix that:
      - Disable the interrupts when performing an action with `gpio_isr_handler_remove(GPIO_PIN)`
      - Wait some time to wait the button to be release (~20ms to ~50ms)
      - Perform the desired action
      - Enable the interrupts back again `gpio_isr_handler_add(GPIO_PIN, <isr_routine>, <isr_parameters>)`
8. GPIO Configuration Structure:
    - Used to configure many GPIOs at the same time using a bitmask
    - `gpio_config_t` structure type, and `gpio_config(<*gpio_config_structure>)` to set the values
9. DAC
    - The ESP32 has two 8-bits DAC at pins GPIO25 (Channel 1) and GPIO26 (Channel 2)
    - Allow to change the voltage level through the GPIO pin
    - Need to include the `driver/dac.h` header file
    - To enable the DAC `dac_output_enable(DAC_CHANNEL_1/2)`
    - To setup the DAC maxmium value `dac_output_voltage(DAC_CHANNEL_1/2, <0-255>)`. A [DAC online calculator](https://www.rfwireless-world.com/calculators/n-bit-DAC-resolution-calculator.html) can be used to calculate the DAC voltage steps
10. ADC
    - Convert analog signals do digital values
    - There are two ADC that can be used. For the ESP32 the first one has 8-channels and the other one 10-channels
    - The ADC 2 is used by the WiFi Driver
    - The ADC 1 channels 0 and 3 are used by the internal hall sensor
    - The maximum input voltage is 1.1v
    - To use the ADC the driver must be included `driver/adc.h`
    - To set the resolution of the ADC `adc1_config_width(ADC_WIDTH_BIT8/12)`
    - The ESP32 ADC resolution is in maximum 12 bits
    - To set the ADC attenuation `adc1_config_channel_atten(<adc1_channel>, <adc_channel_attenuation>)`
    - To read the ADC value `adc1_get_raw(<adc1_channel>)`
11. LEDC and PWM:
    - To use the LEDC include it at `driver/ledc.h`
    - The ESP32 has 8 timers, which 4 are fast and 4 are slow: `LEDC_HIGH_SPEED_MODE` and `LEDC_LOW_SPEED_MODE`
      - Fast timers work on hardware
      - Slow timers work on software
    - Each timer has 8 channels numbered from 0 to 7
    - Used to fade a GPIO in a blocking or unblocking way
12. Hall Sensor:
    - The ESP32 has an internal magnetic field detector
    - On certain versions of the ESP32 the sensor only works in one pole of the magnet and not on both poles
13. Touch Sensor:
    - To use it include `driver/touch_pad.h`
    - Smaller the voltage range in the capacitive pad, smaller the power consumption but it can be more influence by external noises
    - The touch pad GPIO needs to be configured with `touch_pad_set_voltage()` and `touch_pad_config()`. It also needs to be initialized with `touch_pad_init()`
    - To read the raw values use `touch_pad_read()`
    - There are also filters to be enable depending on the use-case
