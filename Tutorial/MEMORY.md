# Memory Layout

- 512KB memory
- DRAM (Data RAM): Hold variables, allocate memory from heap from malloc and used as stacl
- IRAM (Instruction RAM): Hold instruction code to run in runtime, normally used in interruptions
- Both DRAM and IRAM can be splitted in two segments: Compile (variables) and Dynamic (heap)
- To get DRAM size: `heap_caps_get_free_size(MALLOC_CAP_8BIT)`
- To the the IRAM size: `heap_caps_get_free_size(MALLOC_CAP_32BIT) - heap_caps_get_free_size(MALLOC_CAP_8BIT)`
- To the the entire RAM size: `heap_caps_get_free_size(MALLOC_CAP_32BIT)`
- The available DRAM can also be obtained using the following FreeRTOS call: `xPortFreeHeapSize()`
- Memory infos (`idf.py size`):
  - DRAM = DRAM .data + DRAM .bss
    - DRAM .data: Memory that is initialized, static or global and has an initialized value. Example: static int x = 3;
    - DRAM .bss: Memory that is not initialized. Example: static int x;
  - CPU Cache: 64KB
  - Reserved Data: 8KB
- To get memory details for an application the command `idf.py size-files` can be used to peak each compiled file size
  - The `rodata` shown here is the read-only data used by that specific file
- The heap can be (and probably is) fragmented, so you probably can not allocated the maximum free size as described aboce. To view the maximum DRAM heap block size available use: `esp_caps_get_largest_free_block(MALLOC_CAP_8BIT)`
- Each FreeRTOS task have allocated stack limits used for functions and local variables, allocation buffers or other structures bigger than that limit, will result in errors and restarts
  - To view how much memory is available for a particular task, `uxTaskGetStackHighWatermark(TaskHandle_t task_handle)` can be used. If used inside the task, the parameter can be NULL
  - The task stack size is defined during the task creation **IN BYTES**
  - Every time that a task is allocated, heap space is used
- PSRAM (pseudo-ram):
  - It is the ability to convert extra flash into RAM (as the WROVER/long modules does)
  - PSRAM can't be used to allocate task stasks
  - To force something to be allocated in a malloc call to the PSRAM use `esp_caps_malloc(data_size, MALLOC_CAP_SPIRAM)`
- Flash Partition:
  - The binary size can be seen with the `idf.py size` command "Used Flash size"
  - The partitions.csv can be reverse generated based on the `gen_esp32part.py <partition_table.bin> <output.csv>` script
  - The partition table consists of entries:
    1. Name: Can be any value
    2. Type: Can be `app` or `data` (and subtype)
        - app: Has the `factory` and `ota` (0 to 15 over-the-air partitions) as subtypes
        - data: Has `ota` (stores the current OTA boot partition and statues), `phy` (use by the Wi-Fi Driver), `nvs` (non-volatile-storage, persist information), `nvs_keys` (encryption keys for nvs) and `spiffs` subtypes
    3. Offset: Normally filled automatically based on the size of each partition
    4. Size: Can be inserted as bytes, kilobytes (K) and megabytes (M)
    5. Flags: Enable the encryption in the partition (when flash-encryption is enabled)
