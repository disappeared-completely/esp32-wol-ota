# ESP32 WOL OTA Firmware

OTA firmware releases for the ESP32 USB Wi-Fi Configurator and Telegram
Wake-on-LAN project.

## Current recovery release

- Firmware: `ESP32_USB_WiFi_Configurator_v4.1-telegram-recovery.bin`
- Version: `v4.1-telegram-recovery`
- SHA256: `AE52031354BDBBE32857699A0945E5C3A3B896439F7FCBF08D2FA4605B5B1ED8`

The recovery release improves Wi-Fi and Telegram recovery after an internet
outage and ignores duplicate Telegram updates.

## Safety

- This firmware only uses network Wake-on-LAN and the onboard ESP32 LED.
- It contains no relay, mains-voltage, or laptop power-button control.
- Do not place Wi-Fi passwords, Telegram tokens, command PINs, or OTA tokens
  in this repository.
- Use the SHA256 value above when starting a remote OTA update.

