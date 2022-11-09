# OTA

1. Requirements
    - Binary file must be secured with HTTPS
    - The certificate for the webserver must be embedded to the ESP
    - At least 2 OTA partitions and 1 OTA data partition in the partition_table.csv
    - A way to trigger the OTA service
    - Internet connection (obviously)

2. The original application will always be in the Factory partition
3. OTA DATA partition stores states about the OTA process and current used partition for the firmware
