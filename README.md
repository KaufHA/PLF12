# KAUF Power Monitoring Smart Plug (PLF12)


The recommended way to import a plug into your ESPHome dashboard is through the dashboard import feature. The plug should show up in the ESPHome dashboard as being available to adopt.  Below is the minimum necessary yaml, which can be used to manually add your device instead.  Change the name and friendly_name substitutions to fit your use case.  Adding a [`use_address:`](https://esphome.io/components/wifi.html?highlight=use_address#configuration-variables) under `wifi:` will allow you to point the ESPHome dashboard to the device on your network for flashing OTA.

The friendly_name substitution is recommended and will not be automatically created by the ESPHome dashboard import.  If you use the ESPHome dashboard import feature, we recommend that you add a friendly_name substitution to rename all of the entities in Home Assistant in one line of yaml.

```
substitutions:
  name: bedroom-lamp
  friendly_name: Bedroom Lamp

packages:
  kauf.plf12: github://KaufHA/PLF12/kauf-plf12.yaml

wifi:
  ssid: !secret wifi_ssid
  password: !secret wifi_password
```

## Repo Contents

This repo contains files for the PLF12 Power Monitoring Smart Plug.

***kauf-plf12.yaml*** - The yaml file recommended to import a plug into your ESPHome dashboard and keep all custom KAUF functionality.  This is the yaml file incorporated automatically when the dashboard import feature is used.

***kauf-plug-lite.yaml*** - A yaml file without any Kauf custom components but otherwise keeping as much functionality from kauf-plf12.yaml as possible.

***kauf-plug-minimal.yaml*** - A yaml file to import a plug into your ESPHome dashboard with only basic power monitoring smart plug functionality using stock ESPHome.

***kauf-plug-update.yaml*** - The yaml file to build the update bin file.  Generally not useful to end users.

***kauf-plug-factory.yaml*** - The yaml file to build the factory bin file.  Generally not useful to end users.


## Configuration Entities
If using the precompiled binary or kauf-plf12.yaml as a package in the ESPHome dashboard, the following configuration entities are automatically created.  Entities listed as disabled by default can be enabled in Home Assistant, or simply modified through the web interface by clicking "Visit Device" in Home Assistant or typing the plug's IP address into a web browser.  Adding the substitution `disable_entities: "false"` in your yaml file will cause all entities to be automatically enabled in Home Assistant.

***Blue LED*** light entity - This is in the configuration section because the main intent is to control the blue LED's brightness while the plug itself automatically turns the light on and off.  However, you can also directly control the blue LED in automations or manually using this light entity.

***Blue LED Config*** select entity - Configures the behavior of the blue LED.  Has the following options.  Defaults to *Power Status*.
- *Power Status*: LED follows relay state.
- *No Automation*: The plug will never automatically change the LED, but the LED can still be user-controlled via the light entity.
- *Invert Power Status*: LED follows the inverse of the relay's state (LED on when relay is off).
- *Error Status*: The LED will blink when an error is detected.  The most common errors are being unable to connect to Wi-Fi or Home Assistant.
- *Error and Power*: The LED will follow the relay state, but then also blink when an error is detected.
- *Error and Invert Power*: The LED will follow the inverse of the relay state, but then also blink when an error is detected.

***Red LED*** light entity - Same as the Blue LED light entity, but for the Red LED.

***Red LED Config*** select entity - Same as the Blue LED select entity, but for the Red LED.  Defaults to *Error Status*.

***Button Config*** select entity - Defines when the button toggles the relay.  To disable the button toggling the relay, select the *Don't Toggle* option.  Otherwise, you can select the relay to toggle either on press or on release of the button.  Toggle on release might be desired in order to have a different action performed when the button is held for a certain amount of time.  For instance, the precompiled binary defaults to toggling on release so that the toggle can be blocked in case the button is held for 30 seconds to reenable the Wi-Fi AP.

***Boot State*** select entity - Defines the relay's behavior on boot.
- *Restore Power Off State*: The relay will restore its state from the last time the plug was changed before power off or reboot.
- *Invert Power Off State*: The relay will toggle every time it is reboot or powered off and back on.
- *Always On*: The relay will always turn on when the plug boots.
- *Always Off*: The relay will always remain off when the plug boots.
- *YAML Configured*: The boot state is set using the `sub_restore_mode:` substitution to any valid value for a [GPIO switch's restore mode](https://esphome.io/components/switch/gpio.html?highlight=restore_mode#configuration-variables).

***Monitoring Update Interval*** select entity - Disabled by default.  Defines the update frequency for the power monitoring sensor entities.  Defaults to *10s*.  Hard coded options are *2s*, *5s*, *10s*, *30s*, and *60s*.  The *YAML Configured* option uses the value defined by the `sub_update_interval:` substitution in YAML.  For the precompiled binaries, this is set to 20s to provide an additional option.  Changing this value will cause the plug to automatically reboot to effect the change.

***Debounce Time*** number entity - Disabled by default.  Defines an amount of time that the button needs to be held before toggling the relay.  Defaults to 20ms and has a minimum value of 10ms.

***No Hass*** switch entity - Disabled by default.  Turn this switch on if not using Home Assistant with the plug.  Prevents the plug from flashing an error due to no API connection and rebooting every 15 minutes.

***Scale Current*** number entity - Disabled by default.  Scales the current sensor.  Can be used to calibrate the plug.

***Scale Power*** number entity - Disabled by default.  Scales the power sensor.  Can be used to calibrate the plug.

***Scale Voltage*** number entity - Disabled by default.  Scales the voltage sensor.  Can be used to calibrate the plug.

***Use Threshold*** number entity - Disabled by default.  Sets a threshold for the *Device in Use* binary sensor.  The binary sensor turns on if the power detected by the plug exceeds the threshold.


## Diagnostic Entities
If using the precompiled binary or kauf-plf12.yaml as a package in the ESPHome dashboard, the following diagnostic entities are automatically created.  All diagnostic entities are disabled by default.  Adding the substitution `disable_entities: "false"` in your yaml file will cause all entities to be automatically enabled in Home Assistant.

***Restart Firmware*** button entity - Press this button to reboot the plug.

***Button Press Duration*** sensor entity - Indicates the amount of time in milliseconds that the button was last pressed for.  Reads zero while the button is being pressed.

***IP Address*** sensor entity - Gives the plug's IP address.

***Uptime*** number entity - Gives the plug's uptime in seconds.


## Advanced Settings (via YAML Substitutions)
When using kauf-plf12.yaml as a package in the ESPHome dashboard, you can configure the following aspects by adding substitutions.  The substitutions section of kauf-plf12.yaml has comments with more explanation as well.  Adding one of these in your local yaml will overwrite the corresponding value in kauf-plf12.yaml.

***name*** - Defines the plug's name in the ESPHome dashboard, the device name in Home Assistant, mDNS URL, and other various aspects.  The name should use only lower-case letters and dashes.  Do not use spaces or underlines.

***friendly_name*** - The friendly name will be used to name every entity in Home Assistant.  Add a substitution to change this to something descriptive for each device.  Capital letters and spaces are good for the friendly name.

***sub_restore_mode*** - defines the state of the relay on boot-up.  See more about the options under restore_mode here: [GPIO Switch](https://esphome.io/components/switch/gpio.html).  The boot mode select entity must remain in the *YAML Configured* option for this substitution to be effective. 

***disable_entities*** - Adding a substitution to redefine this to "false" will result in all entities being automatically enabled in Home Assistant.

***disable_webserver*** - Adding a substitution to redefine this to "true" will result in the plug's webserver not listening on port 80.

***button actions*** - The *sub_on_press* and *sub_on_release* substitutions can be used to define scripts that will run based on button press and release if you want something different than the default actions to happen.

***plug state actions*** - The *sub_on_turn_on* and *sub_on_turn_off* substitutions can be used to define scripts that will run when the plug's relay turns on and off, respectively.

***sub_toggle_check*** - defines a script that, if running, will stop the button from toggling on release.  Can be used to stop a button press action from occurring when a hold action executed instead.

***default_button_config*** - defines the default option for the button config select entity.

***power monitoring calibration*** - all of the values used to calibrate the power monitoring calibration can be overwritten using substitutions to make the calibration more accurate.

***power monitoring update interval*** (`sub_update_interval:`) - Defines the time delay between updates of the power monitoring sensors.  The power monitoring update interval select entity needs to be set to *YAML Configured* for this substitution to take effect.

***sub_pm_initial_option*** - Defines the default option for the power monitoring update interval select entity.


## Factory Reset
Going to the plug's URL in a web browser and adding /reset will completely wipe all settings from flash memory.  Note that this will wipe the plug's memory of whether the relay was on or off, and therefore the plug may turn off.

## Clearing Wi-Fi Credentials, Getting Wi-Fi AP to Reconfigure Credentials
Holding the plug's button for 30 seconds will clear any programmed Wi-Fi credentials, including any hard coded via yaml configuration, and cause the plug to put its Wi-Fi AP back up.  Going to the plug's URL in a web browser and adding /clear will do the same thing.  No other settings or data will be lost.


## Troubleshooting

General troubleshooting ideas applicable to all products are located in the [Common repo's readme](https://github.com/KaufHA/common/blob/main/README.md#troubleshooting).

## Recommended Tasmota Template

***Important Note:***  Do not have anything plugged into the plug when first flashing Tasmota.  The default Tasmota template uses the relay for an indicator LED, so the relay will turn on and off every second or so until you change the template or get the plug connected to Wi-Fi.

```
{"NAME":"Kauf Plug","GPIO":[0,320,0,32,2720,2656,0,0,321,224,2624,0,0,0],"FLAG":0,"BASE":18}
```

This page explains how to use the template if you need help: https://templates.blakadder.com/howto.html


## Links
- [Purchase plugs on Amazon](https://www.amazon.com/dp/B0BJLGNPPX)
- [KAUF YouTube Channel](https://www.youtube.com/channel/UCjgziIA-lXmcqcMIm8HDnYg)
- [KaufHA Website](https://kaufha.com/plf12)
