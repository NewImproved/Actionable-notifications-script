# Home Assistant Actionable Notifications
This script provides a flexible system for sending actionable notifications to users through Home Assistant. It allows you to send customized notifications with interactive buttons that can trigger different actions when pressed.
## Features

* **Multiple Recipients:** Send notifications to specific users or everyone at once  
* **Interactive Buttons:** Add confirm, dismiss, and snooze buttons with custom text  
* **Automatic Timeout:** Define fallback actions if no one responds in time  
* **Resend Capability:** Automatically resend notifications if no response is received  
* **Action Coordination:** Automatically notify other users when someone takes action  
* **User-Friendly Setup:** Easy to add new users and devices  
* **Customizable Messages:** Fully customize notification content and appearance  
* **Full Home Assistant Integration:** Trigger any Home Assistant service as a response action

## How It Works
The script sends mobile notifications with action buttons to selected users. When a user taps a button, Home Assistant executes the corresponding service call. Other users are notified that action has been taken, preventing duplicate responses.
If no one responds within the specified timeout period, an optional fallback action can be triggered automatically, and notifications can be resent at specified intervals.
## Setting Up

1. Copy the entire script to your Home Assistant configuration
2. Add your users to the input_select.notification_users options list
3. Update the sensor.mobile_device_map template to map users to their mobile devices

## Adding New Users
To add a new user:

1. Add their username to the options list under input_select.notification_users
2. Add a mapping for their device in the sensor.mobile_device_map attributes:
yamlCopyusername: "{{ state_attr('person.username','source').replace('device_tracker.','notify.mobile_app_') }}"


## Usage Examples
### Basic Alert with Confirmation
```python
service: script.actionable_notify
data:
  title: "Front Door Alert"
  message: "Someone is at the front door. Unlock the door?"
  show_confirm: true
  show_dismiss: true
  confirm_text: "Unlock"
  dismiss_text: "Ignore"
  action_confirm_action: "lock.unlock"
  action_confirm_entity_id: "lock.front_door"
```
### Critical Alert with Automatic Fallback
```python
service: script.actionable_notify
data:
  who: "all"
  title: "Water Leak Detected!"
  message: "Water leak detected in the basement. Turn off water main?"
  show_confirm: true
  show_dismiss: true
  confirm_text: "Turn Off Water"
  dismiss_text: "Ignore"
  action_confirm_action: "switch.turn_on"
  action_confirm_entity_id: "switch.water_shutoff_valve"
  timeout_minutes: 3
  timeout_action: "switch.turn_on"
  timeout_entity_id: "switch.water_shutoff_valve"
  resend_count: 2
  resend_interval: 1
```
## Parameters
The script supports numerous parameters that let you customize the notification experience:
### Recipients

* ```who```: Recipient selection - user name or 'all' (default: 'all')

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
* ```entity_id```: Entity ID to attach (optional)

## Compatibility
This script works with the official Home Assistant Companion App for iOS and Android.
