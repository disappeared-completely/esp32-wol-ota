# ESP32 WOL OTA Firmware

OTA firmware releases for the ESP32 USB Wi-Fi Configurator and Telegram
Wake-on-LAN project.

## Current safe OTA release

- Firmware: `ESP32_USB_WiFi_Configurator_v4.6-power-on-wol.bin`
- Version: `v4.6-power-on-wol`
- SHA256: `4939E3B14B9DDECB14E574347C336F1A2EE1D4154BD5CCF826F0BB59B4A21951`

After power-on or brownout, ESP32 waits for a saved Wi-Fi network and an IP
address, then sends WOL to the saved target. Internet access and Telegram are
not required for this local broadcast. The existing sender repeats the packet
three times on UDP ports 9 and 7 and calculates the broadcast address from the
current IP and subnet.

An outstanding wake is saved in NVS until a packet is sent, so a maintenance
reboot while the router is offline does not discard it. Failed sends are retried
at most once every 10 seconds. After a successful send, the pending flag is
cleared. Wi-Fi reconnects, normal 3-hour maintenance reboots, Telegram reboots,
and OTA restarts do not create new wake requests. They can still complete a
pending request from an earlier power event.

`/status` and `/diag` show the power-on WOL state. `/lastwake` records source
`power_on`, target, and broadcast after an automatic send; this history is in
RAM and resets on reboot. The OTA restart itself will not trigger a new wake.
Automatic wake is active on the next power cycle. If ESP32 remains powered by
a UPS, a Wi-Fi reconnect alone will not trigger automatic wake. Sending a WOL
packet does not guarantee that the target computer wakes after a power outage.

The 3-hour maintenance reboot and existing OTA connectivity verification remain
unchanged. Stored Wi-Fi, Telegram, PIN, OTA, and target settings are retained.

It keeps the v4.3 Telegram maintenance behavior: periodic Telegram TLS client
recreation, client reset after slow or failed Telegram operations, `/diag`, and
a short delayed `/reboot PIN` so the Telegram reply can be sent before restart.
It also keeps the v4.2 safe OTA rollback behavior: OTA confirmation waits until
Wi-Fi and Telegram connectivity have been verified, and requests rollback if
verification does not succeed within five minutes.

Validation: firmware compile passed; the application image is 1,215,536 bytes
and fits the existing 1,310,720-byte OTA slot. Image checksum and validation hash
passed. Existing v4.5 source was compared to verify that changes are limited to
the new power-on WOL feature, its diagnostics, and version. Physical power-cut
testing on the remote board was not available.

## Safety

- This firmware only uses network Wake-on-LAN and the onboard ESP32 LED.
- It contains no relay, mains-voltage, or laptop power-button control.
- Do not place Wi-Fi passwords, Telegram tokens, command PINs, or OTA tokens
  in this repository.
- Use the SHA256 value above when starting a remote OTA update.
