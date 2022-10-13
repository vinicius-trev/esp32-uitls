# Storage

1. To embed files inside a the flash:
    - The files are specified inside the `component.mk` file which is normally empty and `CMakeLists.txt`. The options are:
      - COMPONENT_EMBED_TXTFILES: For text files only. The compiler knows that these files are **NULL terminated**
      - COMPONENT_EMBED_FILES: For non-text files (images, binaries, etc)
      - Note: The first option need to be inserted with the **:=** assigned to a file, the next ones with **+=**, as the example

      ```kconfig
      COMPONENT_EMBED_TXTFILES := path_to_first_file.html
      COMPONENT_EMBED_TXTFILES += path_to_second_file.txt
      COMPONENT_EMBED_FILES := path_to_generic_file.bin
      COMPONENT_EMBED_FILES += path_to_picture.jpg
      ```

      - In addition, the CMakeLists will also be changed, as the example below:

      ```cmake
      (...)
      set(COMPONENT_EMBED_TXTFILES "path_to_first_file.html" "path_to_second_file.txt")

      set(COMPONENT_EMBED_FILES "path_to_generic_file.bin" "path_to_picture.jpg")
      (...)
      ```

2. To access the embedded files inside the flash:
    - Declare an `extern const unsigned char *` pointer variable followed by the assembly instruction `asm("_binary_<text_file_name>_start")`
    - Example: `extern const unsigned char *file1 asm("_binary_text_file1_start");`
    - For files that are not declared as TEXT, another pointer variable with the _end keyword need to be specified: `asm("_binary_<non_text_file>_end")`
    - The non-text file size can be determined subtracting the start address from the end address
    - Note: This method does not use the filesystem to store the file. The file is embedded in the firmware without the filesystem knowing its existence

3. NVS - Non-Volatile-Storage
    - Created in the partitions.csv with a name, data type and subtype nvs
    - To init a default nvs call `nvs_flash_init()`
    - To open the default partition (named as nvs) use `nvs_open(<nvs_namespace>, <open_mode | NVS_READWRITE>, <nvs_handler>)`
    - To init a custom nvs partition call `nvs_flash_init_partition(<nvs_partition_name>)`
    - To open a custom nvs partition use `nvs_open(<nvs_partition_name>, <nvs_namespace>, <open_mode | NVS_READWRITE>, <nvs_handler>)`
    - The NVS has default calls for int8, int16, int32... That can be used to store and read from that specific data type. There's also calls for string `nvs_get_str` and `nvs_set_str` for null-terminated values, and `nvs_set_blob` `nvs_get_blob` for structured data
    - When using strings, the size is automatically determined based on the null-terminated value. When using blobs, the size must be specified in the set parameters and passed as reference to the get call to avoid buffer overflow
    - The NVS partition has utility functions that can be used based on the `nvs_stats_t` data type via the `nvs_get_stats(nvs_stats_t stat_variable)` call
    - Each set/get operation in the NVS needs a key limited to 15 bytes in size (as per idf documentation)

4. SPIFFS (replaced by LittleFS with the same API):
    - To create a partition, first put the files in the desired folder
    - Then in the component CMakeLists set the command: `littlefs_create_partition_image(<storage_partition_name> <path_to_folder> FLASH_IN_PROJECT)`
    - The LittleFS API is the same as the SPIFFS, just replace **spiffs** to **littlefs**
    - To configure the storage use the `esp_vfs_littlefs_conf_t` structure
    - To register the storage use the `esp_vfs_littlefs_register(<config_parameters>)` function
    - There is a CRUD example in the ESP-IDF in `examples/storage/spiffs`
