# Webserver

1. There are 3 ways do host content in the ESP32
    1. Host the ESP32 only as the web service, without hosting the website, doing only requests to it
    2. ESP32 host only the index.html, other assets are hosted in other servers/CDN
    3. The ESP32 hosts everything (images, javascript, css, html, etc)
2. The webserver is defined in `esp_http_server.h` (There is also a https variant)
3. Setup the server with `HTTPD_DEFAULT_CONFIG()` macro
4. Init the server with `httpd_start()`
5. Register the URIs with the `httpd_uri_t` struct passing it to `httpd_register_uri_handler()`
6. In the endpoint callback, use the `http_resp_send*()` functions to reply to the client

7. mDNS:
    - Used to assign a nameserver to the webserver/ESP32 IP
    - `mdns_init()` to start the mDSN server
    - `mdns_hostname_set()` to set the ESP32 hostname that will be assigned to the ESP IP
    - `mdns_instance_name_set()` friendly name of the device
    - The DNS URI of the service will always terminate with `<hostname>.local`

8. When creating a POST endpoint, remember to check everything since the user is responsible for the input

9. WebSockets
    - Enable `WebSocket server support` in menuconfig
    - Each client has a client session id, can be created with `httpd_req_to_sockfd()`
    - Use `httpd_ws_frame_t` struct to configurate the websocket frame
    - Use `httpd_ws_recv_frame()` to receive the websocket frame from the client/server
    - Use `httpd_ws_send_frame()` to send the websocket frame to the client/server
    - The websocket connection MUST be initialized before any attempt to send or receive data
