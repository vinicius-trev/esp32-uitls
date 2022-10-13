# Debugging with OpenOCD

1. Breakpoints with math expressions is possible
2. OpenOCD has some configuration files needed to work properly
3. Add variables to the watch tab to take a look at the values of them
4. The debug console tab in VSCode can receive as input variables to see their status/value
5. Keybinds:
    - F5: Start/Continue the debugger
    - F10: Step over a function
    - F11: Enter in a function
    - Shift F5: Stop the debugger
    - Control Shift F5: Restart the debugger
6. Wiring with FTDI2232 / esp-prog (check ESP datasheet):
    - GPIO15: TDO
    - GPIO12: TDI
    - GPIO13: TCK
    - GPIO14: TMS
    - GND: GND
    - 3V3: VCC
    - Caution: Do not power on the ESP via USB using the VCC from the FTDI2232
7. QEMU:
    - To ease things up, use a baremetal Linux or a Docker container (.devcontainer)
    - To get everything running in QEMU, you need to combine all the binaries according to the partition table of the project as seen in build/flasher_args.json
        - To do that use `esptool.py --chip <esp32_chip_model> merge_bin --output <output_file_name> --fill_flash_size <esp_flash_size> {<address> <binary_file>} --flash_mode <flash_mode> --flash_freq <flash_frequency> --flash_size <esp_flash_size>` where:
            - <esp32_chip_model> is the ESP32 chip model (esp32, esp32s2, esp32s3...)
            - <output_file_name> is the merged binary
            - <esp_flash_size> is the size of the device flash (4MB, 8MB, 16MB...)
            - <address> offset of the <binary_file>
            - <binary_file> path to the binary file inside build/
            - <flash_mode> ESP current flash mode (dio or qio)
            - <flash_frequency> ESP flash frequency configured in the sdkconfig file
        - The outputfile will be put by default in the project/workspace root
    - To execute the emulation run: `qemu-system-xtensa [-s] [-S] -nographic -machine esp32 -drive file=<binary_file>,if=mtd,format=raw` where:
        - [-s] flag to start in debug mode
        - [-S] flag to halt and wait for a breakpoint
        - <binary_file> path to the combined binary file created in the previous step
    - You can't debug the emulated environment with the launch.json (debug_adapter) directly, for that you need a VSCode extension called "Native Debug"
        - Edit the launch.json adding a new debug option: {} GDB: Connect to gdbserver
        - Edit the preferences as your choices
        - Add `gdbpath` option passing the path to xtensa-esp32-eld-gdb
        - Edit the `executable` passing the path to the .elf file inside the build folder
        - Edit the `target` pointing to the port ":1234"
