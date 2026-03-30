# 🚀 TCL Google TV: Instant HDMI Input Switching via ADB (Home Assistant)

Tired of using slow, unreliable `input keyevent` commands (like D-pad arrows and Enter) to switch HDMI inputs on your TCL TV? After a deep dive into `dumpsys tv_input` logs, I've discovered the direct **Android Intent** commands to teleport your TV to any HDMI port instantly. No menus, no delays, no missed clicks.

## 🏁 Why this is better:
* **Lightning Fast:** Switches inputs in milliseconds.
* **100% Reliable:** Doesn't depend on the current menu state or UI focus.
* **Direct Hardware Call:** Triggers the specific hardware port mapping natively in Android TV.

## 🚀 The "Magic" Command
Instead of navigating menus, use the `am start` command to target the `TvPassThroughService` directly:

```bash
am start -a android.intent.action.VIEW -d content://android.media.tv/passthrough/com.tcl.tvinput%2F.TvPassThroughService%2FHW[ID]
```

## 📍 Verified Port Mapping (The Hardware IDs)
Based on my hardware analysis, here is the exact mapping for TCL TV models. The secret is knowing which `HW` ID corresponds to which physical HDMI port:

| Physical Port | Hardware ID | ADB Command String Segment |
| :--- | :--- | :--- |
| **HDMI 1** | HW15 | `.../.TvPassThroughService%2FHW15` |
| **HDMI 2** | HW16 | `.../.TvPassThroughService%2FHW16` |
| **HDMI 3** | HW17 | `.../.TvPassThroughService%2FHW17` |
| **HDMI 4** | **HW18** | `.../.TvPassThroughService%2FHW18` |

## 🏠 Home Assistant Integration Example
You can use this directly in your scripts or automations using the standard Android TV integration. 

*Note: For the best performance and zero delay, ensure your HA is connected directly to the TV via the internal Python ADB, bypassing any external ADB servers.*

```yaml
action: androidtv.adb_command
target:
  entity_id: media_player.your_tcl_tv
data:
  command: "am start -a android.intent.action.VIEW -d content://android.media.tv/passthrough/com.tcl.tvinput%2F.TvPassThroughService%2FHW18"
```

---

### ☕ Support my work!
This mapping took over **9898777 hours** :) of reverse engineering and dozens of Home Assistant restarts to perfect. If this guide saved you from the same frustration, feel free to support my work!
**Every small tip is appreciated!**

* **Revolut Me:** [revolut.me/mariannud](https://revolut.me/mariannud)

Thank you!

---
**Contributed by:** MA-Linkestis (2026)
