# TCL Google TV HDMI input switching via ADB and Home Assistant

Switch to a specific HDMI input on a tested TCL Google TV by launching its
`TvPassThroughService` input URI. This avoids navigating the on-screen input
menu with remote key events. It uses the TV's existing Android TV system
components; no root, custom firmware, or removal of system packages is needed.

## Tested on

**TCL 65Q7C-UK / Google TV / Android 14.** The following HDMI hardware IDs
were confirmed on this TV. They may differ on other TCL models or firmware
versions; discover and verify your TV's IDs before using them.

| HDMI input | Tested hardware ID |
| --- | --- |
| HDMI 1 | `HW15` |
| HDMI 2 | `HW16` |
| HDMI 3 | `HW17` |
| HDMI 4 | `HW18` |

## ADB command

The URI below is the same TCL `TvPassThroughService` intent used by the
original guide. Replace `[ID]` with a hardware ID confirmed on your TV:

```bash
am start -a android.intent.action.VIEW -d content://android.media.tv/passthrough/com.tcl.tvinput%2F.TvPassThroughService%2FHW[ID]
```

For example, the tested HDMI 4 command is:

```bash
am start -a android.intent.action.VIEW -d content://android.media.tv/passthrough/com.tcl.tvinput%2F.TvPassThroughService%2FHW18
```

## Find and verify your TV inputs

Run these read-only commands through an authorized ADB shell or Home
Assistant's `androidtv.adb_command` action:

```bash
dumpsys tv_input
getprop sys.tcl.inputid
```

Use `dumpsys tv_input` to identify the TV input services and hardware IDs
exposed by your firmware. After selecting an input, use
`getprop sys.tcl.inputid` to check the TV's reported current input. Confirm
the result against the actual HDMI port; do not assume the mapping above is
universal.

## Home Assistant example

With the Home Assistant Android Debug Bridge integration connected to the TV,
use `androidtv.adb_command` in a script or automation. This example selects
HDMI 4 on the tested TV; replace the entity ID and verified hardware ID for
your setup.

```yaml
action: androidtv.adb_command
target:
  entity_id: media_player.your_tcl_tv
data:
  command: "am start -a android.intent.action.VIEW -d content://android.media.tv/passthrough/com.tcl.tvinput%2F.TvPassThroughService%2FHW18"
```

This is an input-selection command, not physical feedback. Check the reported
input and the TV screen if your automation needs confirmation.

## Troubleshooting

- **ADB cannot connect:** Check that ADB debugging is enabled on the TV, the
  pairing/authorization prompt was accepted, and the TV address and ADB port
  configured in Home Assistant are correct. Recheck connectivity after a TV
  firmware update.
- **Wrong input or no switch:** Inspect `dumpsys tv_input` for the IDs on your
  firmware. Verify the selected input with `getprop sys.tcl.inputid` and the
  physical HDMI port. Do not copy the `HW15`-`HW18` mapping to an untested TV.

## Support

[Support this work on Revolut](https://revolut.me/mariannud)

Contributed by MA-Linkestis (2026).
