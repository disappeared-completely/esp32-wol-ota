# ESP32 WOL OTA Firmware

OTA firmware releases for the ESP32 USB Wi-Fi Configurator and Telegram
Wake-on-LAN project.

## Current OTA release

- Firmware: `ESP32_USB_WiFi_Configurator_v4.8-delayed-power-on-wol.bin`
- Version: `v4.8-delayed-power-on-wol`
- SHA256: `60604B578BE0B0F1CAF79A16826818132CEC67654CC5154AEEE9998E1C9060D0`

This release waits for 60 seconds of stable Wi-Fi after a power event, then
sends five WOL groups spaced 30 seconds apart. This gives the router's wired
LAN and the target Ethernet adapter more time to start after a power cut.

It retains the v4.7 fix for a Telegram message lifetime defect: a slow or failed reply
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
`v4.8-delayed-power-on-wol` to confirm installation.

Automatic wake after power returns:

After power-on or brownout, ESP32 waits for a saved Wi-Fi network and an IP
address, then waits for 60 seconds of continuously connected Wi-Fi. With stable
Wi-Fi, WOL groups are sent at approximately 60, 90, 120, 150, and 180 seconds
after connecting. These are minimum delays: busy network calls can postpone a
send. Timer waits do not block the main loop. Internet access and Telegram are
not required. Each group repeats the packet three times on UDP ports 9 and 7
and calculates the broadcast address from the current IP and subnet.

The pending flag and successful-group count are saved in NVS. A successful local
UDP send is not delivery or wake confirmation, so later groups are still sent.
After five successful groups the pending flag is cleared and the sequence stops.
Failed local sends retry after 10 seconds without consuming a group. Wi-Fi drops
restart the 60-second stability timer without erasing progress. Software/OTA
reboots resume only owed groups after the stability wait; they do not start a new
sequence. A new power event resets the count. Pending flags from v4.6/v4.7 work.

`/status` and `/diag` show the power-on WOL state, group count X/5, and next-send
countdown while pending and connected. `/lastwake` records source `power_on`,
target, and broadcast after the latest automatic group; this history is in RAM
and resets on reboot. The group count persists across software resets. The OTA
restart itself will not create a new wake request.
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

Validation: firmware compile passed; the application image is 1,217,584 bytes
and fits the existing 1,310,720-byte OTA slot. Image checksum and validation hash
passed. Seventy native checks exercise the exact timing header used by the
firmware: slow router startup, group limits/intervals, failed-send retry, Wi-Fi
drops, saved-progress resume, software versus power-on boot, corrupted counters,
and 32-bit timer wraparound. Source comparison confirmed all other v4.7 code is
unchanged. Physical power-cut and end-to-end wake tests on the remote board were
not available; UDP/NVS fault injection was not performed.

## Safety

- This firmware only uses network Wake-on-LAN and the onboard ESP32 LED.
- It contains no relay, mains-voltage, or laptop power-button control.
- Do not place Wi-Fi passwords, Telegram tokens, command PINs, or OTA tokens
  in this repository.
- Use the SHA256 value above when starting a remote OTA update.
