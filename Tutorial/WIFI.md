# WIFI

1. The wifi uses the NVS internally, so it must be initialized `nvs_flash_init()`
2. To simplify the prototypes or PoCs to connect to the internet use the `protocol_examples_common.h`. Copy from the examples and insert as component in the CMakeLists.txt, then use the `example_connect()` function setting the SSID and PSWD of the WIFI network in the project kconfig
3. When working with internet and other related stuff, init the tcp/ip stack with `esp_netif_init()`
4. The WIFI uses event loops, so that must be initialized as well `esp_event_loop_create_default()`
5. The WIFI must be initialized using the `wifi_config_init_t` structure. There are macros to prefill that structure, example `WIFI_CONFIG_INIT_DEFAULT()`
    - Then, use that structure in `esp_wifi_init()`
6. The mode (AP/STA/APSTA) can be changed using `esp_wifi_set_mode()`. being:
    - AP: Access Point
    - STA: Station (similar to a client), will look for an access point to connect
    - AP/STA: Both modes available
7. Finally, use `esp_wifi_start()` to init the modem

8. When using the STA mode, the networks around the ESP can be scanned. To do that:
    - Fill the `wifi_scan_config_t` structure with SSID, BSSID and PSWD with zeroes
    - Then use the `esp_wifi_scan_start()` passing the config structure described above. The scan function can be blocked/halt the CPU
    - The results will be saved in the `wifi_ap_record_t` structure. That may be an array representing the amount of networks scanned
    - To get the results use `esp_wifi_scan_get_ap_records()`
    - The results can be formatted printed as the example below:

    ```c
        static char *getAuthModeName(wifi_auth_mode_t auth_mode)
        {
        char *names[] = {"OPEN", "WEP", "WPA PSK", "WPA2 PSK", "WPA WPA2 PSK", "MAX"};
        return names[auth_mode];
        }

        ESP_LOGD(TAG, "Found %d access points:", maxRecods);
        ESP_LOGD(TAG, ("               SSID              | Channel | RSSI |   Auth Mode ");
        ESP_LOGD(TAG, ("----------------------------------------------------------------");
        for (int i = 0; i < maxRecods; i++)
            ESP_LOGD(TAG, ("%32s | %7d | %4d | %12s", (char *) <record_struct_name>[i].ssid, <record_struct_name>[i].primary, 
                                                               <record_struct_name>[i].rssi, getAuthModeName(<record_struct_name>[i].authmode));
        ESP_LOGD(TAG, ("----------------------------------------------------------------");
    ```

9. NTP: Network Time Protocol
    - Dependencies: `stdio.h` `stdlib.h` `time.h` `esp_sntp.h`
    - This will use the network/internet to setup the ESP internal clock
    - First setup the sync mode:
      - SNTP_SYNC_MODE_IMMED: Will sync the time immediately
      - SNTP_SYNC_MODE_SMOOTH: If the time is not synced, will sync the time slowly. If the clock is 30 min out, will slowly increment or decrement the time to the correct value
    - Set the server name `sntp_setservername()` (pool.ntp.org or br.pool.ntp.org)
    - Init the NTP service `sntp_init()`
    - The time sync event can be notified by a callback with `sntp_set_time_sync_notification_cb()`
    - The time can be formatted in order to be printed using the `strftime()` function. The are a number of different format options, check the docs
    - The timezone can be set with `setenv("TZ", <timezone>, 1)`, timezone being, for example `"UTC-3"` or a more complex definition as described [here](https://github.com/nayarsystems/posix_tz_db/blob/master/zones.csv). Finally set the variable with `tzset()`

10. Use the event loop callbacks to register and analyze the WIFI/TCPIP events
11. To use STA, the the netif stack must be initialized as STA: `esp_netif_create_default_wifi_sta()`
12. To use AP, the the netif stack must be initialized as AP: `esp_netif_create_default_wifi_ap()`
13. For STA and AP, there are a reason struct that correlate errors with enum types in `esp_wifi_types.h`
14. [Useful WIFI Library to STA and AP modes](https://github.com/Mair/esp32-course/tree/master/_13_Internet_Connection/v4.3/13_3_internet_connection)
