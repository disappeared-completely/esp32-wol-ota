# ESP32 WOL OTA Firmware

OTA firmware releases for the ESP32 USB Wi-Fi Configurator and Telegram
Wake-on-LAN project.

## Current safe OTA release

- Firmware: `ESP32_USB_WiFi_Configurator_v4.3-telegram-maintenance.bin`
- Version: `v4.3-telegram-maintenance`
- SHA256: `28C0890612C6610617BC8C54E366C744EFA481A470B49CDC0A81EB62E39A37BA`

This release improves Wi-Fi and Telegram recovery after an internet outage,
ignores duplicate Telegram updates, and delays OTA confirmation until Wi-Fi
and Telegram connectivity have been verified. If verification does not
succeed within five minutes, ESP32 requests rollback to the previous firmware.

The v4.3 maintenance release also periodically recreates the Telegram TLS
client, resets it after slow or failed Telegram operations, adds `/diag`, and
schedules `/reboot PIN` with a short delay so the Telegram reply can be sent
before restart.

## Safety

- This firmware only uses network Wake-on-LAN and the onboard ESP32 LED.
- It contains no relay, mains-voltage, or laptop power-button control.
- Do not place Wi-Fi passwords, Telegram tokens, command PINs, or OTA tokens
  in this repository.
- Use the SHA256 value above when starting a remote OTA update.
