# ESP-NOW

1. Communication protocol between multiple ESP32 (up to 20 devices)
2. Support encrypted messages
3. Packages has a maximum of 250 bytes
4. It is a lightweight protocol that helps to save energy. It has less overhead than HTTP, TCP, etc
5. ESP-NOW support encryption keys for transmitting data across devices
    - `esp_now_peer_info_t`: Key goes to lmk field
    - Peer `encrypt` flash must be true
