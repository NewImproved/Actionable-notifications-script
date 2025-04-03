# Home Assistant Actionable Notifications Script
This script provides a flexible system for sending actionable notifications to users through Home Assistant. It allows you to send customized notifications with interactive buttons that can trigger different actions when pressed.
## Features

* **Multiple Recipients:** Send notifications to specific users or everyone at once
* **Customizable Messages:** Fully customize notification content  
* **Interactive Buttons:** Add confirm, dismiss, and snooze buttons with custom text
* **Configurable Actions:** Trigger any Home Assistant service as a response action  
* **Fallback action:** Define fallback actions if no one responds before timeout  
* **Resend Capability:** Automatically resend notifications if no response is received  
* **Action Coordination:** Automatically notify other users when someone takes action  
* **Expandable setup:** To add new users and devices, follow instruction in code  

## How It Works
The script sends mobile notifications with action buttons to selected users. When a user taps a button, Home Assistant executes the corresponding service call. The notifications for the other users are replaced by a new notification with information that action has been taken, preventing duplicate responses.  
If no one responds within the specified timeout period, an optional fallback action can be triggered automatically and original notification is removed, preventing duplicate responses. Notifications can also be resent at specified intervals, if no answer is received.
## Setting Up

1. Copy the entire custom_component-folder to your Home Assistant configuration (or however you store your packages) and modify you configuration-file accordingly
2. Modify users and set up new receptiants as descibed in notify.yaml


## Usage Examples
### Basic Alert with Confirmation
```python
service: script.actionable_notify
data:
  title: Front Door Alert
  message: Someone is at the front door. Unlock the door?
  show_confirm: true
  show_dismiss: true
  confirm_text: Unlock
  dismiss_text: Ignore
  action_confirm_action: lock.unlock
  action_confirm_entity_id: lock.front_door
```
### Critical Alert with Automatic Fallback
```python
service: script.actionable_notify
data:
  who: all
  title: Water Leak Detected!
  message: Water leak detected in the basement. Turn off water main?
  show_confirm: true
  show_dismiss: true
  confirm_text: Turn Off Water
  dismiss_text: Ignore
  action_confirm_action: switch.turn_on
  action_confirm_entity_id: switch.water_shutoff_valve
  timeout_minutes: 3
  timeout_action: switch.turn_on
  timeout_entity_id: switch.water_shutoff_valve
  resend_count: 2
  resend_interval: 1
```
 ### Send notification with picture from camera
 ```python
 service: script.actionable_notify
 data:
   who: all
   title: Camera
   message: Movement detected in the living room.
   url: /lovelace/Camera
   image: /local/tmp/snapshot-camera.jpg
   entity_id: camera.tapo_c210_466f_hd_stream
   enable_timeout: false
   group: camera
```

## Parameters
The script supports numerous parameters that let you customize the notification experience:
### Recipients

* ```who```: Recipient selection - configured user name or 'all' (default: 'all')

### Notification Content

* ```title```: Notification title
* ```message```: Notification message content
* ```url```: URL to open when notification is tapped (optional)

### Action Buttons

* ```show_confirm```: Show confirm button - 'true' or 'false' (default: false)
* ```show_dismiss```: Show dismiss button - 'true' or 'false' (default: false)
* ```show_snooze```: Show snooze button - 'true' or 'false' (default: false)
* ```confirm_text```: Text for confirm button (default: 'Yes')
* ```dismiss_text```: Text for dismiss button (default: 'No')
* ```snooze_text```: Text for snooze button (default: 'Snooze')

### Button Actions

* ```action_confirm_action```: Service to call when 'confirm' is tapped
* ```action_confirm_entity_id```: Entity ID for confirm action
* ```action_dismiss_action```: Service to call when 'dismiss' is tapped
* ```action_dismiss_entity_id```: Entity ID for dismiss action
* ```action_snooze_action```: Service to call when 'snooze' is tapped
* ```action_snooze_entity_id```: Entity ID for snooze action

### Timeout Settings

* ```timeout_minutes```: Minutes to wait for response (default: 5)
* ```enable_timeout```: Enable timeout - 'true' or 'false' (default: true)
* ```timeout_action```: Service to call on timeout
* ```timeout_entity_id```: Entity ID for timeout action

### Resend Settings

* ```resend_count```: Number of times to resend after timeout (default: 0)
* ```resend_interval```: Minutes between resends (default: 2)

### Additional Options

* ```group```: Notification group identifier (default: 'actionable_notify')
* ```image```: URL to an image (optional)
* ```entity_id```: Entity ID for camera to send stream to notification on iOS (optional)

## Compatibility
This script works with the official Home Assistant Companion App for iOS and Android.
