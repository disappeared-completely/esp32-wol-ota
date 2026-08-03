# ESP32 WOL OTA Firmware

OTA firmware releases for the ESP32 USB Wi-Fi Configurator and Telegram
Wake-on-LAN project.

## Current safe OTA release

- Firmware: `ESP32_USB_WiFi_Configurator_v4.5-3h-maintenance-reboot.bin`
- Version: `v4.5-3h-maintenance-reboot`
- SHA256: `5B7B00A64C693996F842624031357AA78BB9BE2C0DA20955CF68D1A2A6C84577`

This release changes the automatic maintenance reboot interval from 12 hours
to 3 hours of ESP32 uptime. This is a normal software restart, not a firmware
write, so it should not wear out flash memory or harm the ESP32. If power is
lost, the ESP32 simply starts fresh when power returns, and the 3-hour timer
starts again from boot. The maintenance reboot is skipped while OTA is running
or while a newly installed OTA firmware is still in its five-minute verification
window.

It keeps the v4.3 Telegram maintenance behavior: periodic Telegram TLS client
recreation, client reset after slow or failed Telegram operations, `/diag`, and
a short delayed `/reboot PIN` so the Telegram reply can be sent before restart.
It also keeps the v4.2 safe OTA rollback behavior: OTA confirmation waits until
Wi-Fi and Telegram connectivity have been verified, and requests rollback if
verification does not succeed within five minutes.

## Safety

- This firmware only uses network Wake-on-LAN and the onboard ESP32 LED.
- It contains no relay, mains-voltage, or laptop power-button control.
- Do not place Wi-Fi passwords, Telegram tokens, command PINs, or OTA tokens
  in this repository.
- Use the SHA256 value above when starting a remote OTA update.
