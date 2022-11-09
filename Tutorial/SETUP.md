# Environment Setup and First Look

1. Criação de ambientes utilizando vscode e container para simplificar a implantação e reprodutibilidade do ambiente em diversas máquinas
2. FreeRTOS v prefix -> return void
3. Build-in random number generator -> esp_system.h -> esp_random()
4. Kill serial monitor ctrl+]
5. Get chip info -> esp_system.h -> esp_chip_info()
6. Get flash info -> esp_spi_flash.h -> spi_flash_get_chip_size()
7. Get idf version -> esp_get_idf_version()
8. Get mac addr -> esp_efuse_mac_get_default()
9. VSCode F12 -> go to definition
10. Function Pointers -> return_type (* pointer_name)(function_args_with, comma)
11. CMakeLists -> Multiple source files, replace SRCS to SRC_DIRS
