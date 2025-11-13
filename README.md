# ESP32-2432S028R Smart Home Display

ESPHome configuration for the Cheap Yellow Display (ESP32-2432S028R) with integrated controls for ceiling fan, HVAC system, and video camera.

## Features

### 🎨 Touch Display Interface
- **240x320 pixel ILI9342 display** with touch controls
- Clean, organized layout with status indicators
- Real-time status updates for all devices
- Color-coded status (Green=ON, Red=OFF)

### 🌀 Ceiling Fan Control
- Toggle fan on/off with touch
- Speed control (0-100% in 25% increments)
- Direction control (Forward/Reverse)
- Visual status display

### 🌡️ HVAC/Climate Control
- Toggle HVAC system on/off
- Temperature adjustment (+/- buttons)
- Mode selection (Off, Heat, Cool, Auto, Fan Only)
- Display target and current temperature
- Visual mode and status display

### 📹 Camera Integration
- Camera status display
- Quick toggle for camera viewing
- Motion detection control
- Camera recording switch

### ✨ Additional Features
- RGB LED on back of display (controllable)
- Adjustable backlight
- WiFi status indicator
- Time and date display
- "All On" / "All Off" quick actions
- Web server for debugging

## Hardware

**Device:** ESP32-2432S028R (Cheap Yellow Display)
- ESP32 microcontroller
- 2.8" ILI9342 TFT display (240x320)
- XPT2046 resistive touchscreen
- RGB LED (back of board)
- Backlight control

## Setup Instructions

### 1. Prerequisites
- [ESPHome](https://esphome.io/) installed
- Home Assistant with existing devices configured
- USB cable to connect the display to your computer

### 2. Configure Secrets

Edit `secrets.yaml` and replace all placeholder values:

```yaml
# WiFi credentials
wifi_ssid: "YourWiFiName"
wifi_password: "YourWiFiPassword"

# API and OTA passwords (generate random strings)
api_key: "generate-a-32-character-key-here"
ota_password: "your-secure-password"
ap_password: "fallback-ap-password"

# Your Home Assistant entity IDs
ceiling_fan_entity: "fan.bedroom_ceiling_fan"
ceiling_fan_direction_entity: "switch.bedroom_fan_direction"
hvac_entity: "climate.living_room_thermostat"
camera_entity: "camera.front_door"
```

### 3. Find Your Entity IDs

In Home Assistant:
1. Go to **Developer Tools** → **States**
2. Search for your devices
3. Copy the entity IDs (e.g., `fan.bedroom_fan`, `climate.thermostat`)

### 4. Flash the ESP32

#### First Time Setup:
```bash
# Connect the ESP32 via USB
# Run ESPHome compile and upload
esphome run cyd-smart-home.yaml
```

#### Over-The-Air Updates (after first flash):
```bash
# Update wirelessly
esphome run cyd-smart-home.yaml --device <IP_ADDRESS>
```

### 5. Add to Home Assistant

After flashing:
1. The device should appear in **Settings** → **Devices & Services** → **ESPHome**
2. Click **Configure** and enter your API key
3. All entities will be available in Home Assistant

## Touch Areas

The display is divided into interactive touch zones:

| Zone | Location | Action |
|------|----------|--------|
| **Ceiling Fan** | Top Left | Toggle fan on/off |
| **HVAC** | Top Right | Toggle HVAC on/off |
| **Camera** | Bottom Left | Activate camera view |
| **All Off** | Bottom Right (Left) | Turn off all devices |
| **All On** | Bottom Right (Right) | Turn on all devices |

## Available Controls in Home Assistant

Once connected, you'll have access to:

### Buttons
- Fan Speed Up / Down
- HVAC Temperature Up / Down
- Restart Display

### Numbers
- Fan Speed Percentage (0-100%)
- HVAC Temperature (60-85°F)

### Selects
- HVAC Mode (Off, Heat, Cool, Auto, Fan Only)

### Switches
- Camera Recording
- RGB LED
- Display Backlight

## Camera Notes

**Important:** The ESP32 cannot directly stream video due to memory limitations. The camera integration provides:
- Status monitoring
- On/off control
- Motion detection toggle
- Snapshot viewing (requires custom setup)

For live video streaming, you would need to:
1. Use a separate camera server (Home Assistant camera entity)
2. Display snapshots that refresh periodically
3. Consider using an ESP32-CAM for direct camera functionality

## Customization

### Adjust Touch Calibration

If touch is not accurate, modify the calibration values in `cyd-smart-home.yaml`:

```yaml
touchscreen:
  - platform: xpt2046
    calibration:
      x_min: 200    # Adjust these values
      x_max: 3850
      y_min: 240
      y_max: 3860
```

To find correct values, watch the logs when touching corners:
```bash
esphome logs cyd-smart-home.yaml
```

### Change Display Colors

Modify the `color:` section to customize the interface colors.

### Add More Controls

Add additional touch areas in the `touchscreen:` → `binary_sensor:` section.

## Troubleshooting

### Display is blank
- Check backlight is enabled
- Verify power supply (needs 5V 1A minimum)
- Try adjusting `invert_colors` setting

### Touch not responding
- Check calibration values
- Ensure interrupt_pin is correctly configured
- Watch logs for touch coordinates

### WiFi not connecting
- Verify credentials in secrets.yaml
- Check WiFi signal strength
- Look for fallback AP: "Smart Home Display Fallback"

### Entities not appearing in Home Assistant
- Verify entity IDs in secrets.yaml match your HA setup
- Check API key is correct
- Restart ESPHome device

## Home Assistant Scripts

Create these scripts in Home Assistant for the "All On/Off" buttons:

```yaml
# configuration.yaml or scripts.yaml

script:
  all_devices_on:
    alias: "All Devices On"
    sequence:
      - service: fan.turn_on
        target:
          entity_id: fan.your_ceiling_fan
      - service: climate.turn_on
        target:
          entity_id: climate.your_hvac_system
      - service: light.turn_on
        target:
          entity_id: all

  all_devices_off:
    alias: "All Devices Off"
    sequence:
      - service: fan.turn_off
        target:
          entity_id: fan.your_ceiling_fan
      - service: climate.turn_off
        target:
          entity_id: climate.your_hvac_system
      - service: light.turn_off
        target:
          entity_id: all
```

## Resources

- [ESPHome Documentation](https://esphome.io/)
- [Cheap Yellow Display GitHub](https://github.com/witnessmenow/ESP32-Cheap-Yellow-Display)
- [ILI9xxx Display Component](https://esphome.io/components/display/ili9xxx.html)
- [XPT2046 Touchscreen Component](https://esphome.io/components/touchscreen/xpt2046.html)

## License

This configuration is provided as-is for personal use. Modify as needed for your setup!
