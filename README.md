# AWTRIX NG thermostat dial for the Ulanzi TC002

Turns the knob of an Ulanzi TC002 pixel clock running [AWTRIX NG](https://github.com/Blueforcer/awtrix-ng) into a thermostat dial for Home Assistant. A small Berry app shows the target temperature, the current temperature and the mode. Turning the knob changes the target, pressing it switches the mode.

The full write-up, including installing AWTRIX NG, the four display pages and the local voice assistant, is on raspberry.tips: **[How to Set Up the Ulanzi TC002 with AWTRIX NG and Home Assistant](https://raspberry.tips/en/raspberrypi-tutorials/ulanzi-tc002-awtrix-ng-home-assistant-setup)** (German version: [Ulanzi TC002 mit AWTRIX NG und Home Assistant](https://raspberry.tips/raspberrypi-tutorials/ulanzi-tc002-awtrix-ng-home-assistant)).

## What it does

- Each click of the knob changes the target temperature by 0.5 °C, within the thermostat's own minimum and maximum.
- Pressing the knob cycles through `auto`, manual (`heat`) and `off`. The flame is orange in auto mode, red in manual and blue when off.
- The new target is only sent once the knob has rested for a second, so several clicks in a row become one command. That spares wireless radiator thermostats, which can only transmit for a limited time per hour.
- The app stays on screen for 20 seconds, and every turn of the knob extends that. As soon as another app is showing, the knob goes back to brightness and volume.

## Files

| File | Purpose |
|---|---|
| [`app/heating.ax`](app/heating.ax) | The Berry app for the clock |
| [`automations/heating_status.yaml`](automations/heating_status.yaml) | Sends target, current temperature and mode to the clock whenever the thermostat changes |
| [`automations/heating_commands.yaml`](automations/heating_commands.yaml) | Applies the new target or mode when the knob is turned or pressed |
| [`blueprints/automation/raspberry-tips/awtrix_ng_thermostat_dial.yaml`](blueprints/automation/raspberry-tips/awtrix_ng_thermostat_dial.yaml) | Both automations as one blueprint |
| [`examples/pages.yaml`](examples/pages.yaml) | Bonus: four display pages (indoor, outdoor, energy, home battery) with animated icons |
| [`icons/sofa.gif`](icons/sofa.gif) | 8 × 8 sofa icon used by the indoor page |

## Requirements

- Ulanzi TC002 with AWTRIX NG, connected to the MQTT broker (Mosquitto) of Home Assistant
- A thermostat in Home Assistant as a `climate` entity, ideally with the modes `auto`, `heat` and `off`

Tested with AWTRIX NG 1.2.3 and Home Assistant 2026.11.

## Installation

1. **App:** In the clock's web interface, open **Scripts**, click the upload arrow at the top right and select `heating.ax`. Then click the orange save icon. The app appears in the list as *Heating* with the status *Running*.
2. **Automations:** Either import the blueprint and create an automation from it,

   [![Import blueprint](https://my.home-assistant.io/badges/blueprint_import.svg)](https://my.home-assistant.io/redirect/blueprint_import/?blueprint_url=https%3A%2F%2Fgithub.com%2Fraspberry-tips%2Fawtrix-ng-thermostat-dial%2Fblob%2Fmain%2Fblueprints%2Fautomation%2Fraspberry-tips%2Fawtrix_ng_thermostat_dial.yaml)

   or create the two automations from `automations/` in YAML mode and replace every `climate.office_thermostat` with your thermostat.
3. **Test:** Switch to the Heating app on the clock. After a few seconds it shows your thermostat's values. Turn the knob one click and let go: about a second later the new target shows up in Home Assistant.

**No auto mode?** If your thermostat only knows `heat` and `off`, change the line `var nxt = …` in the app to `var nxt = {"heat": "off", "off": "heat"}` before uploading.

**Several clocks?** Change the default value in the app's `@config topic` line before uploading and use the same topic in the blueprint (or in both automations).

## The display pages

`examples/pages.yaml` sends four pages to the clock over MQTT: temperature and humidity indoors and outdoors, solar output with household consumption, and the home battery. Replace the device ID (`ccc4b2c7e38f` in the example) with your clock's MQTT prefix and the sensors with your own. The icons come from the LaMetric gallery and have to be uploaded to the clock first; the article lists every icon ID and explains how.

## Feedback

Found a bug or have an idea? Open an issue. Pull requests are welcome.

## License

[MIT](LICENSE)
