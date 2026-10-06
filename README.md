# ESP32 WOL OTA Firmware

OTA firmware releases for the ESP32 USB Wi-Fi Configurator and Telegram
Wake-on-LAN project.

## Current OTA release

- Firmware: `ESP32_USB_WiFi_Configurator_v4.7-telegram-ota-recovery.bin`
- Version: `v4.7-telegram-ota-recovery`
- SHA256: `603E00A89EC46A5FBDD7395425A0A5624C8D0AD94DBC23D40BA96DB304E2B045`

This release fixes a Telegram message lifetime defect: a slow or failed reply
could delete the bot while its command handler still borrowed the message/chat
ID. The handler and remote OTA now own independent copies. A reset client is
restored for the final OTA result; `/status` and `/diag` also report the last
remote OTA error/result, held in RAM until reboot.

Telegram TLS handshakes have a separate 5-second timeout; remote OTA HTTPS
handshakes have a 15-second timeout. These are not total request deadlines.
This fixes a confirmed code defect but cannot establish the exact reason for
an earlier failed update without its Serial error log. It does not add an
independent watchdog or guarantee recovery from every network stall.

If the installed firmware responds slowly, use `/reboot YOUR_PIN`, wait for
it to return, then retry OTA. The old firmware must still receive the update
command; publishing this release cannot force an unresponsive ESP32 to install
it. `OTA started` only confirms acceptance. Check `/version` after restart for
`v4.7-telegram-ota-recovery` to confirm installation.

The automatic wake feature introduced in v4.6 is retained:

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

Validation: firmware compile passed; the application image is 1,216,432 bytes
and fits the existing 1,310,720-byte OTA slot. Image checksum and validation hash
passed. Existing v4.5 source was compared to verify that changes are limited to
power-on WOL, Telegram/OTA recovery, diagnostics, and version. Physical power-cut
and slow-network tests on the remote board were not available.

## Safety

- This firmware only uses network Wake-on-LAN and the onboard ESP32 LED.
- It contains no relay, mains-voltage, or laptop power-button control.
- Do not place Wi-Fi passwords, Telegram tokens, command PINs, or OTA tokens
  in this repository.
- Use the SHA256 value above when starting a remote OTA update.
