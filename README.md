# ESP32 WOL OTA Firmware

OTA firmware releases for the ESP32 USB Wi-Fi Configurator and Telegram
Wake-on-LAN project.

## Current safe OTA release

- Firmware: `ESP32_USB_WiFi_Configurator_v4.2-telegram-safe-ota.bin`
- Version: `v4.2-telegram-safe-ota`
- SHA256: `525607A53B80A2A6BD33C46046F909E07FAF627DC4FC98B5EBE29738B0EB9B90`

This release improves Wi-Fi and Telegram recovery after an internet outage,
ignores duplicate Telegram updates, and delays OTA confirmation until Wi-Fi
and Telegram connectivity have been verified. If verification does not
succeed within five minutes, ESP32 requests rollback to the previous firmware.

## Safety

- This firmware only uses network Wake-on-LAN and the onboard ESP32 LED.
- It contains no relay, mains-voltage, or laptop power-button control.
- Do not place Wi-Fi passwords, Telegram tokens, command PINs, or OTA tokens
  in this repository.
- Use the SHA256 value above when starting a remote OTA update.
