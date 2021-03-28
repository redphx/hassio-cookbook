# script.androidtv_press_buttons
Quickly send button command to Android TV devices.  
Demo: https://youtu.be/mrJS9Q_toQw  
  
  
## Requirements
- Android TV devices of course, like Sony TVs, Chromecast with Google TV...  
- Setup [Android TV integration](https://www.home-assistant.io/integrations/androidtv/).
  
## Why do you need this?

Normal method, painfully slow (there is a 1 second delay between each commands).  
You could use [`sendevent`](https://www.home-assistant.io/integrations/androidtv/#androidtvlearn_sendevent-for-faster-adb-commands) commands instead, but the code is still long and difficult to read.

```yaml
youtube_report_ads:
  alias: Report Youtube ads
  sequence:
    - service: androidtv.adb_command
      target:
        entity_id: media_player.android_tv
      data:
        command: PLAY_PAUSE
    - service: androidtv.adb_command
      target:
        entity_id: media_player.android_tv
      data:
        command: DOWN
    - service: androidtv.adb_command
      target:
        entity_id: media_player.android_tv
      data:
        command: "sendevent /dev/input/event0 1 103 1 && sendevent /dev/input/event0 1 103 0 && sendevent /dev/input/event0 0 0 0"
...
```
This is my solution: fast, simple, and easy to use.
  
```yaml
youtube_report_ads:
  alias: Report Youtube ads
  sequence:
    - service: script.android_tv_press_buttons
      data:
        entity_id: media_player.android_tv_living_room
        method: sendevent
        buttons:
          - PLAY_PAUSE

          # Press 4 buttons with minimal delay
          - - DOWN
            - UP
            - UP
            - CENTER

          # Wait for 1 second
          - SLEEP 1

          - - DOWN
            - DOWN
            - DOWN
            - CENTER

          - SLEEP 0.5

          - - UP
            - CENTER

          - PLAY_PAUSE
```

## How to use

- `entity_id`: target Android TV device
- `method`: optional
  * `default`: use `input keyevent` command, slow but can be used on any Android TVs. This is the default value.
  * `sendevent`: use `sendevent` command, faster but need to be configured. And this doesn't work on some devices like CCwGTV ("Permission denined" error).
  * It's recommended to use `sendevent` if your device supports it.
---

- Press one button
```yaml
data:
  ...
  buttons: UP
```
  
- Press multiple buttons (small delay between buttons)
```yaml
data:
  ...
  buttons:
    - UP
    - DOWN
    - DOWN
```

- Press multiple buttons (minimal delay between buttons)  
```yaml
data:
  ...
  buttons:
    - - LEFT
      - RIGHT
      - LEFT
      - RIGHT
```
  
- Add delay
```yaml
data:
  ...
  buttons:
    - LEFT
    - CENTER

    # Wait for 1.5s
    - SLEEP 1.5

    - RIGHT

    # Wait for 1s
    - SLEEP 1

    - CENTER
```

- Press button using its ID
```yaml
data:
  ...
  buttons:
    - 103
    - 108
```
