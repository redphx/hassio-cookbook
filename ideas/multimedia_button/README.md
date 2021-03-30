# Multimedia Button
Use one switch/button to control multiple devices (control one device at a time).

### Demo
https://youtu.be/3HqkuVperA8

### My devices:
- Aqara Wireless Mini Switch with Gyro (WXKG12LM, Zigbee). This switch supports 4 actions: click, double click, hold & shake.
- ConBee II for connecting the switch to HA.
- Sony TV (running Android TV) and Chromecast with Google TV.
- Samsung HW-Q70T Soundbar with Spotify Connect support.
- Each app on Android TV/Chromcast has different set of commands.
- Device priority: Sony TV > CCwGTV > Soundbar (Spotify).
- **Note**: Android TV devices refresh states every 5 seconds so you need to wait a bit after switching app/device.

### Commands:
- Android TV/Chromecast:
  - Youtube:
    - Click: Play/Pause
    - Double Click: Like video
    - Shake: Skip ad
  - Twitch:
    - Click: Toggle chat
    - Hold: Mute
    - Shake: Move focus to the right side
- Soundbar:
  - Click: Play/Pause
  - Double Click: Previous track
  - Shake: Next track
  - Hold: Play Spotify on soundbar

## Setup
1. Create a custom sensor to detect which device to control:
```yaml
sensor:
  - platform: template
    sensors:
      multimedia_button_device:
        friendly_name: "Device for Multimedia Button"
        value_template: >
          {% if states('media_player.android_tv_sony') != 'off' and state_attr('media_player.android_tv_sony', 'app_id') not in [none, 'com.sony.dtv.tvx'] %}
            media_player.android_tv_sony
          {% elif states('media_player.android_tv_chromecast') != 'off' %}
            media_player.android_tv_chromecast
          {% else %}
            media_player.spotify_redphx
          {% endif %}
        attribute_templates:
          app_id: >
            {% set device = states('sensor.multimedia_button_device') %}
            {{ state_attr(device, 'app_id') }}
```
2. Control flow of the switch in HA's Automation or Node-RED. Source code for Node-RED implementation is in `node-red.json` file.
3. Scripts to control Android TV: [script.androidtv_press_buttons](/scripts/androidtv_press_buttons)
