# Eufy Security Integration for Home Assistant

A complete guide to integrate Eufy Security devices (cameras, doorbells, sensors) with Home Assistant using the eufy-security-ws addon.

## 📋 Table of Contents
- [Hardware Compatibility](#hardware-compatibility)
- [Prerequisites](#prerequisites)
- [Installation](#installation)
- [Configuration](#configuration)
- [Dashboard Cards](#dashboard-cards)
- [Automations](#automations)
- [Troubleshooting](#troubleshooting)
- [FAQ](#faq)

## 🔧 Hardware Compatibility

### Tested Hardware
This guide has been successfully tested with:
- **Eufy Video Doorbell 2 Pro** (firmware 0.1.9.2)
- **Eufy HomeBase 2** (firmware 3.4.0.4h)
- **Eufy Solo Cam S340** (firmware 3.3.0.0)
- **Eufy Door Sensors**
- **Eufy Motion Sensors**

### Expected to Work
Most Eufy Security devices should work with this integration, including:
- Video Doorbells (2K, Dual, etc.)
- Indoor/Outdoor Cameras
- HomeBase 1/2/3
- Entry Sensors
- Motion Sensors

## ✅ Prerequisites

### Home Assistant Setup
- **Home Assistant OS** (tested on version 16.3)
- **Core**: 2025.12.3 or later
- **Supervisor**: 2025.12.3 or later
- **HACS** installed ([Installation Guide](https://hacs.xyz/docs/setup/download))

### Eufy Account Requirements
- Active Eufy Security account
- **2FA disabled** (the integration currently doesn't support 2FA)
- Devices added and working in the Eufy Security app
- Know your account region (EU, US, etc.)

### Network Requirements
- Eufy devices and Home Assistant on the same network (recommended)
- Internet connection for initial setup and cloud features
- Port 3000 available for the addon

## 📦 Installation

### Step 1: Install eufy-security-ws Addon

1. Navigate to **Settings** → **Add-ons** → **Add-on Store**
2. Click the **⋮** menu (top right) → **Repositories**
3. Add this repository URL:
   ```
   https://github.com/bropat/eufy-security-ws
   ```
4. Click **Add** and close the dialog
5. Find **eufy-security-ws** in the add-on store
6. Click on it and press **Install**

### Step 2: Configure the Addon

1. After installation, go to the **Configuration** tab
2. Enter your settings:

```yaml
username: your-eufy-email@example.com
password: your-eufy-password
country: NL  # Use your country code (NL, US, DE, etc.)
trusted_device_name: HomeAssistant
port: 3000
polling_interval: 10
```

3. Click **Save**
4. Go to the **Info** tab
5. Click **Start** and enable **Start on boot**
6. Check the **Log** tab to verify it's running correctly

### Step 3: Install the Integration

1. Open HACS → **Integrations**
2. Click **⋮** (top right) → **Custom repositories**
3. Add:
   - URL: `https://github.com/fuatakgun/eufy_security`
   - Category: **Integration**
4. Click **Add**
5. Search for **Eufy Security** in HACS
6. Click **Download** and restart Home Assistant

### Step 4: Add the Integration

1. Go to **Settings** → **Devices & Services**
2. Click **+ Add Integration**
3. Search for **Eufy Security**
4. Enter the following:
   - **Host**: `localhost` (or your HA IP)
   - **Port**: `3000`
5. Click **Submit**

Your Eufy devices should now appear in Home Assistant!

## **For References:**
I called my Homebase 2 "Eve" and i called my S340 solocame "eagle eye" 


## 🎨 Dashboard Cards

### Security Mode Control Card

This custom card allows you to control your Eufy security modes (Home, Away, Schedule) with a beautiful interface.

**Requirements:**
- [button-card](https://github.com/custom-cards/button-card) (install via HACS)
- [layout-card](https://github.com/thomasloven/lovelace-layout-card) (install via HACS)

**Card Code:**
```yaml
type: custom:layout-card
layout_type: grid
layout:
  grid-template-columns: repeat(9, 1fr)
  grid-auto-rows: minmax(64px, auto)
  grid-gap: 8px
cards:
  - type: custom:button-card
    entity: select.eve_guard_mode
    name: Eufy Security
    icon: mdi:shield-home
    show_state: true
    tap_action:
      action: none
    layout: icon_name_state
    view_layout:
      grid-column: span 9
    styles:
      card:
        - background: var(--card-background-color)
        - border-radius: 12px
        - padding: 8px 12px
        - height: 88px
        - display: grid
        - align-items: center
      grid:
        - grid-template-areas: '"i n" "i s"'
        - grid-template-columns: 72px 1fr
        - grid-template-rows: auto auto
        - column-gap: 12px
        - row-gap: 2px
        - align-items: center
      icon:
        - width: 72px
        - height: 72px
        - justify-self: start
        - color: |
            [[[
              const mode = states['select._guard_mode'].state;
              if (mode === 'Home') return '#4CAF50';
              if (mode === 'Away') return '#F44336';
              if (mode === 'Schedule') return '#2196F3';
              return '#1E3A8A';
            ]]]
      name:
        - font-size: 16px
        - font-weight: 600
        - justify-self: start
        - white-space: normal
      state:
        - font-size: 14px
        - color: var(--secondary-text-color)
        - justify-self: start
        - white-space: normal
  - type: custom:button-card
    name: Home
    icon: mdi:home
    tap_action:
      action: call-service
      service: select.select_option
      service_data:
        entity_id: select.eve_guard_mode
        option: Home
    layout: icon_name
    view_layout:
      grid-column: span 3
    styles:
      card:
        - background: rgba(255,255,255,0.7)
        - border-radius: 10px
        - padding: 6px
        - height: 86px
      grid:
        - grid-template-areas: '"i" "n"'
        - grid-template-columns: 1fr
        - row-gap: 6px
        - justify-items: center
      icon:
        - width: 36px
        - height: 36px
        - color: |
            [[[
              const mode = states['select.eve_guard_mode'].state;
              return mode === 'Home' ? '#4CAF50' : '#1E3A8A';
            ]]]
      name:
        - font-size: 14px
  - type: custom:button-card
    name: Away
    icon: mdi:lock
    tap_action:
      action: call-service
      service: select.select_option
      service_data:
        entity_id: select.eve_guard_mode
        option: Away
    layout: icon_name
    view_layout:
      grid-column: span 3
    styles:
      card:
        - background: rgba(255,255,255,0.7)
        - border-radius: 10px
        - padding: 6px
        - height: 86px
      grid:
        - grid-template-areas: '"i" "n"'
        - grid-template-columns: 1fr
        - row-gap: 6px
        - justify-items: center
      icon:
        - width: 36px
        - height: 36px
        - color: |
            [[[
              const mode = states['select.eve_guard_mode'].state;
              return mode === 'Away' ? '#F44336' : '#1E3A8A';
            ]]]
      name:
        - font-size: 14px
  - type: custom:button-card
    name: Schedule
    icon: mdi:calendar-clock
    tap_action:
      action: call-service
      service: select.select_option
      service_data:
        entity_id: select.eve_guard_mode
        option: Schedule
    layout: icon_name
    view_layout:
      grid-column: span 3
    styles:
      card:
        - background: rgba(255,255,255,0.7)
        - border-radius: 10px
        - padding: 6px
        - height: 86px
      grid:
        - grid-template-areas: '"i" "n"'
        - grid-template-columns: 1fr
        - row-gap: 6px
        - justify-items: center
      icon:
        - width: 36px
        - height: 36px
        - color: |
            [[[
              const mode = states['select.eve_guard_mode'].state;
              return mode === 'Schedule' ? '#2196F3' : '#1E3A8A';
            ]]]
      name:
        - font-size: 14px
grid_options:
  columns: 9
  rows: 3
```

**Note:** Replace `select.eve_guard_mode` with your HomeBase entity name (in this example, the HomeBase is named "Eve").

**Entity naming in this guide:**
- HomeBase: `Eve` → entities like `select.eve_guard_mode`
- Solo Cam S340: `Eagle eye` → entities like `select.eagle_eye_guard_mode`
- Doorbell: `Doorbell` → entities like `camera.doorbell`

Replace these names with your own device names throughout the examples.

### Camera View Card

Display live camera feeds with this simple card:

```yaml
type: vertical-stack
cards:
  - type: picture-entity
    entity: camera.voordeur
    camera_view: live
    refresh_interval: 10
    show_name: true
    show_state: false
    title: S340 Camera
    tap_action:
      action: none
  - type: picture-entity
    entity: camera.doorbell
    camera_view: live
    refresh_interval: 10
    show_name: true
    show_state: false
    title: Doorbell
    tap_action:
      action: none
```

**Note:** Replace entity names with your camera entities.

## 🤖 Automations

### Sync Solo Camera with HomeBase Mode

If you have a Solo camera (without HomeBase), you'll need to sync its guard mode with your HomeBase manually:

```yaml
alias: Sync Eufy S340 with HomeBase Mode
description: "Keeps Solo camera mode in sync with HomeBase"
triggers:
  - entity_id: select.eve_guard_mode
    trigger: state
actions:
  - target:
      entity_id: select.eagle_eye_guard_mode
    data:
      option: "{{ states('select.eve_guard_mode') }}"
    action: select.select_option
mode: restart
```

**Why is this needed?**
Solo cameras operate independently without a HomeBase. This automation ensures your Solo camera's guard mode matches your HomeBase settings.

**Replace entity names:**
- `select.eve_guard_mode` → Your HomeBase guard mode entity
- `select.eagle_eye_guard_mode` → Your Solo camera guard mode entity

### Doorbell Notification (Example)

```yaml
alias: Eufy Doorbell Pressed
triggers:
  - entity_id: binary_sensor.doorbell_person_detected
    to: "on"
    trigger: state
actions:
  - service: notify.mobile_app
    data:
      title: "🔔 Doorbell"
      message: "Someone is at the door!"
      data:
        image: /api/camera_proxy/camera.doorbell
```

### Motion Detection Alert (Example)

```yaml
alias: Eufy Motion Detected Away Mode
triggers:
  - entity_id: binary_sensor.voordeur_motion_detected
    to: "on"
    trigger: state
conditions:
  - condition: state
    entity_id: select.eve_guard_mode
    state: "Away"
actions:
  - service: notify.mobile_app
    data:
      title: "⚠️ Motion Detected"
      message: "Motion detected at front door while in Away mode"
      data:
        image: /api/camera_proxy/camera.voordeur
```

## 🔍 Available Entities

After successful integration, you'll see these entity types:

### HomeBase Entities
- `select.[homebase_name]_guard_mode` - Security mode control (Home/Away/Schedule)
- `select.[homebase_name]_alarm_volume` - Alarm volume control
- `select.[homebase_name]_prompt_volume` - Prompt volume control
- `sensor.[homebase_name]_current_mode` - Current security mode
- `binary_sensor.[homebase_name]` - HomeBase online status

### Camera Entities
- `camera.[camera_name]` - Live camera feed
- `binary_sensor.[camera_name]_motion_detected` - Motion detection
- `binary_sensor.[camera_name]_person_detected` - Person detection (if supported)
- `select.[camera_name]_guard_mode` - Individual camera mode (Solo cameras only)
- `switch.[camera_name]_rtsp_stream` - RTSP stream toggle (if supported)

### Sensor Entities
- `binary_sensor.[sensor_name]_sensor_open` - Door/window sensor status
- `binary_sensor.[sensor_name]_motion_detected` - Motion sensor status
- `binary_sensor.[sensor_name]_battery_low` - Low battery warning

### Debug Entities
- `[device_name]_debug_device` - Device debug information
- `[homebase_name]_debug_station` - Station debug information

## 🐛 Troubleshooting

### Addon Won't Start

**Check the logs:**
1. Go to Settings → Add-ons → eufy-security-ws
2. Click the **Log** tab
3. Look for error messages

**Common issues:**
- Incorrect username/password
- 2FA enabled (must be disabled)
- Wrong country code
- Port 3000 already in use

**Solution:**
- Verify credentials in Eufy Security app
- Disable 2FA in Eufy account settings
- Check country code matches your account
- Change port in configuration if needed

### Integration Not Found

**Symptoms:** Can't find Eufy Security in Settings → Devices & Services

**Solution:**
1. Make sure addon is running (green status)
2. Verify addon logs show "Server listening on port 3000"
3. Restart Home Assistant
4. Try adding integration again with `localhost:3000`

### Cameras Show "Unavailable"

**Possible causes:**
- Network connectivity issues
- Cameras offline in Eufy app
- Cloud polling interval too high

**Solution:**
1. Check if cameras work in Eufy Security app
2. Verify network connectivity
3. Lower polling interval in addon config (try 5 seconds)
4. Restart the addon

### Live Stream Not Working

**Common issue:** Live streams may not work immediately or show delays

**Solutions:**
- Set `refresh_interval` to 10 seconds or higher in camera cards
- Some camera models have limited streaming capabilities
- RTSP streams (if available) work better than cloud streams
- Check your internet upload speed (cloud streaming requires good upload)

### Events Not Showing in Camera Card

**Known limitation:** Event snapshots are not fully integrated yet

**Current behavior:**
- Motion detection triggers work in automations
- Event history may not show all events
- Snapshots might not appear consistently

**Workaround:**
- Use automations to capture snapshots when motion is detected
- Check Eufy Security app for full event history

### Solo Camera Not Syncing

**If your Solo camera mode doesn't match HomeBase:**

Make sure you've added the sync automation (see [Automations](#automations) section).

Verify entity names match your setup:
```bash
# Check your entity names in Developer Tools → States
# Search for "guard_mode"
```

### Connection Drops Frequently

**Symptoms:** Devices go unavailable randomly

**Solutions:**
1. Increase `polling_interval` in addon config (try 15-30 seconds)
2. Check WiFi signal strength of Eufy devices
3. Ensure HomeBase has stable network connection
4. Consider using ethernet for HomeBase if possible

## ❓ FAQ

### Do I need to keep the Eufy Security app?

Yes, for initial device setup and firmware updates. Once configured, Home Assistant can control most features.

### Can I use 2FA with this integration?

No, currently 2FA must be disabled for the integration to work.

### Does this work without internet?

Partial functionality. Local features work, but cloud features (notifications, remote access) require internet.

### Can I view recordings?

Limited support. Live viewing works, but accessing recorded events from Home Assistant is not fully implemented yet.

### Will this affect my Eufy app?

No, both can be used simultaneously. Changes in one will reflect in the other.

### Do I need HACS?

Yes, HACS is required to install the custom Eufy Security integration and recommended for dashboard cards.

### What's the difference between polling interval and refresh interval?

- **Polling interval** (addon config): How often addon checks Eufy cloud for updates
- **Refresh interval** (camera card): How often camera preview image updates

### Can I use multiple HomeBases?

Yes, all HomeBases on your account will be discovered automatically.

### Does this drain camera batteries faster?

Minimal impact. The integration uses the same API as the Eufy app. Lower polling intervals may have slight impact.

### Can I integrate with Alexa/Google Home through HA?

Yes! Once in Home Assistant, you can expose entities to Alexa or Google Home using their respective integrations.

## 🤝 Contributing

Found an issue or have a suggestion? Feel free to open an issue or submit a pull request!

## 🙏 Credits

- [eufy-security-ws](https://github.com/bropat/eufy-security-ws) by bropat - The addon that makes this possible
- [Eufy Security HA Integration](https://github.com/fuatakgun/eufy_security) by fuatakgun - Home Assistant integration
- Community contributors and testers

## 📚 Additional Resources

- [Eufy Security WS Documentation](https://github.com/bropat/eufy-security-ws)
- [Home Assistant Community Discussion](https://community.home-assistant.io/)
- [HACS Installation](https://hacs.xyz/)

---

**Note:** This is a community project and is not affiliated with or endorsed by Anker/Eufy.
