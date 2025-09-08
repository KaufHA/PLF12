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

***kauf-plf12-base.yaml*** - includes substitution changes required to modify the PLF10 yaml files for use with the PLF12 plug.

***kauf-plf12.yaml*** - The yaml file recommended to import a plug into your ESPHome dashboard and keep all custom KAUF functionality.  This is the yaml file incorporated automatically when the dashboard import feature is used.

***kauf-plf12-lite.yaml*** - A yaml file without any Kauf custom components but otherwise keeping as much functionality from kauf-plf12.yaml as possible.

***kauf-plf12-minimal.yaml*** - A yaml file to import a plug into your ESPHome dashboard with only basic power monitoring smart plug functionality using stock ESPHome.

***kauf-plf12-update.yaml*** - The yaml file to build the update bin file.  Generally not useful to end users.

***kauf-plf12-factory.yaml*** - The yaml file to build the factory bin file.  Generally not useful to end users.

## Control Entities

***Kauf Plug*** switch entity - The main switch that controls the plug's relay.  It will be named just Kauf Plug, or whatever you set the friendly_name substitution to in yaml.  All other entities will be prefixed with the name of this entity plus whatever is indicated below for the particular other entity.

***Blue LED*** switch entity - Controls the blue LED on and off.

***Red LED*** switch entity - Controls the red LED on and off.

## Configuration Entities
If using the precompiled binary or kauf-plf12.yaml as a package in the ESPHome dashboard, the following configuration entities are automatically created.  Entities listed as disabled by default can be enabled in Home Assistant, or simply modified through the web interface by clicking "Visit Device" in Home Assistant or typing the plug's IP address into a web browser.  Adding the substitution `disable_entities: "false"` in your yaml file will cause all entities to be automatically enabled in Home Assistant.

***Blue LED Brightness*** number entity - sets the brightness of the blue LED when on.

***Blue LED Config*** select entity - Configures the behavior of the blue LED.  Has the following options.  Defaults to *Power Status*.
- *Power Status*: LED follows relay state.
- *No Automation*: The plug will never automatically change the LED, but the LED can still be user-controlled via the light entity.
- *Invert Power Status*: LED follows the inverse of the relay's state (LED on when relay is off).
- *Error Status*: The LED will blink when an error is detected.  The most common errors are being unable to connect to Wi-Fi or Home Assistant.
- *Error and Power*: The LED will follow the relay state, but then also blink when an error is detected.
- *Error and Invert Power*: The LED will follow the inverse of the relay state, but then also blink when an error is detected.

***Red LED Brightness*** number entity - sets the brightness of the red LED when on.

***Red LED Config*** select entity - Same as the Blue LED select entity, but for the Red LED.  Defaults to *Error Status*.

***Button Config*** select entity - Defines when the button toggles the relay.  To disable the button toggling the relay, select the *Don't Toggle* option.  Otherwise, you can select the relay to toggle either on press or on release of the button.  Toggle on release might be desired in order to have a different action performed when the button is held for a certain amount of time.  For instance, the precompiled binary defaults to toggling on release so that the toggle can be blocked in case the button is held for 30 seconds to reenable the Wi-Fi AP.

***Early Publish*** switch entity - controls whether the plug will report power usage before the configured update interval if a change greater than configured thresholds is detected.  Defaults to on.  Default thresholds are +/- 3w or 25%.

***Scale Current*** number entity - Disabled by default.  Scales the current sensor.  Can be used to calibrate the plug.

***Scale Power*** number entity - Disabled by default.  Scales the power sensor.  Can be used to calibrate the plug.

***Scale Voltage*** number entity - Disabled by default.  Scales the voltage sensor.  Can be used to calibrate the plug.

## Configuration Entities - Precompiled Binaries Only

The precompiled update files add the following configuration entities that were removed in the yaml package to save space. Those using the yaml packages can configure all of these aspects using [substitutions](https://github.com/KaufHA/PLF10/blob/main/README.md#replacements-for-configuration-entities-that-only-exist-in-precompiled-binaries).  Alternatively, the yaml package can be changed to `github://KaufHA/PLF10/config/kauf-plug-plus.yaml` to get all of the config entities back.

***Debounce Time*** number entity - Disabled by default.  Defines an amount of time that the button needs to be held before toggling the relay.  Defaults to 100ms and has a minimum value of 50ms.  The PLF10's button needs at least a minimum of 40ms debounce, but the actual necessary value depends on the amount of EMI in the environment where the plug is being used.  Changing this value will cause the plug to automatically reboot to effect the change.

***Use Threshold*** number entity - Disabled by default.  Sets a threshold for the *Device in Use* binary sensor.  The binary sensor turns on if the power detected by the plug exceeds the threshold.

***Monitoring Update Interval*** select entity - Disabled by default.  Defines the update frequency for the power monitoring sensor entities.  Defaults to *10s*.  Hard coded options are *2s*, *5s*, *10s*, *30s*, and *60s*.  The *YAML Configured* option uses the value defined by the `sub_update_interval:` substitution in YAML.  For the precompiled binaries, this is set to 20s to provide an additional option.  Changing this value will cause the plug to automatically reboot to effect the change.

***Boot State*** select entity - Defines the relay's behavior on boot.
- *Restore Power Off State*: The relay will restore its state from the last time the plug was changed before power off or reboot.
- *Invert Power Off State*: The relay will toggle every time it is reboot or powered off and back on.
- *Always On*: The relay will always turn on when the plug boots.
- *Always Off*: The relay will always remain off when the plug boots.
- *YAML Configured*: The boot state is set using the `sub_restore_mode:` substitution to any valid value for an [ESPHome switch's restore mode]([https://esphome.io/components/switch/gpio.html?highlight=restore_mode#configuration-variables](https://esphome.io/components/switch/)).

***No Hass*** switch entity - Disabled by default.  Turn this switch on if not using Home Assistant with the plug.  Prevents the plug from flashing an error due to no API connection and rebooting every 15 minutes.


## Diagnostic Entities
If using the precompiled binary or kauf-plf12.yaml as a package in the ESPHome dashboard, the following diagnostic entities are automatically created.  All diagnostic entities are disabled by default.  Adding the substitution `disable_entities: "false"` in your yaml file will cause all entities to be automatically enabled in Home Assistant.

***Restart Firmware*** button entity - Press this button to reboot the plug.

***Button Press Duration*** sensor entity (Precompiled binary or *-plus.yaml only) - Indicates the amount of time in milliseconds that the button was last pressed for.  Reads zero while the button is being pressed.

***IP Address*** sensor entity - Gives the plug's IP address.

***Uptime*** number entity - Gives the plug's uptime in seconds.


## Advanced Settings (set via YAML substitutions)
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

***sub_default_scale_\**** - defines default values for the power, current, and voltage scaling.

***power monitoring calibration*** - all of the values used to calibrate the power monitoring calibration can be overwritten using substitutions to make the calibration more accurate (current_resistor_val, voltage_divider_val, sub_hlw_model, power_cal_\*, current_cal_\*, voltage_cal_\*).

***power monitoring update interval*** (`sub_update_interval:`) - Defines the time delay between updates of the power monitoring sensors.  The power monitoring update interval select entity needs to be set to *YAML Configured* for this substitution to take effect.

***sub_pm_initial_option*** - Defines the default option for the power monitoring update interval select entity.

***sub_early_publish_percent*** - Defines a percentage change, relative to the current power output, at which time the plug will ignore the configured update interval and immediately report a new power output to the sensor.  Defaults to 25%.

***sub_early_publish_percent_min_power*** - Defines a minimum power, under which the early publish percent configuration will be ignored.  The plug will never publish early based on percentage change if under this value.  Defaults to 0.5W.

***sub_early_publish_absolute*** - Defines an absolute power change after which the plug will ignore the configured update interval and update immediately.  The unit for this is **NOT WATTS** but rather a raw value used under the hood for processing.  To figure out what value you need here, you can enable verbose logging and the HLW component will output the needed raw value.  For the PLF12 plug, 1W translates to 1.35.  Defaults to 4.05, which is an approximately 3w change.

***sub_hlw_timeout*** - sets an amount of time to wait for a signal from the power monitoring chip before assuming the power being used is 0w.  Defaults to 3s.

### Replacements for Configuration Entities That Only Exist in Precompiled Binaries

The following substitutions are used to configure aspects that have configuration entities in the precompiled binaries but removed in the yaml package to save space. Use the following substitutions instead of configuration entities.

***sub_debounce*** - Requires an integer without units.  Defines the debounce time in milliseconds.

***sub_threshold*** - Requires an integer without units.  Defines the threshold in Watts for the in_use binary sensor.

***sub_update_interval*** - requires an integer with a time unit.  Defines the length of time between updating the power monitoring sensors.

**sub_restore_mode*** - Requires one of the options for [ESPHome's switch restore_mode option](https://esphome.io/components/switch/).  Defines the relay's behavior at boot.

***sub_reboot_timeout*** - Requires an integer with a time unit.  Defines the length of time after which the device will restart if not connected to Home Assistant.  Defaults to 0s, which disables the reboot.  ESPHome default is `15min`.


### Wi-Fi networks

Multiple Wi-Fi networks can now be configured with the [`networks:`](https://esphome.io/components/wifi.html#connecting-to-multiple-networks) key under `wifi:`.  By default, the configured `networks:` will be added in addition to the default initial_ap network.  If you want to only include the `networks:` you added in your local yaml without initial_ap as well, then you need to set `only_networks: true` under `wifi:`.  If you set your SSID/password without the `networks:` key, then that will automatically overwrite the default initial_ap network.

**Note:** With multiple networks configured, you will need to disable fast_connect, which is on by default, with `fast_connect: false`.

## Additional Automations (set via YAML substitutions)
The following substitutions can be set to the name of a script to execute automatically in certain circumstances.  You need to write the script in your yaml file.

***sub_on_press*** - executes right when button is initially pressed

***sub_on_release*** - executes right when button is released

***sub_on_hold_30s*** - executes right when button has been held for 30s, while button is still being held

***sub_on_turn_on*** - executes right when relay turns on

***sub_on_turn_off*** - executes right when relay turns off

## Factory Reset
Going to the plug's URL in a web browser and adding /reset will completely wipe all settings from flash memory.  Note that this will wipe the plug's memory of whether the relay was on or off, and therefore the plug may turn off.

## Clearing Wi-Fi Credentials, Getting Wi-Fi AP to Reconfigure Credentials
Holding the plug's button for 30 seconds will clear any programmed Wi-Fi credentials, including any hard coded via yaml configuration, and cause the plug to put its Wi-Fi AP back up.  Going to the plug's URL in a web browser and adding /clear will do the same thing.  No other settings or data will be lost.


## Troubleshooting

### Binary Size Error

ESPHome began adding API encryption by default, which may make the plug binary files too big to OTA update.  kauf-plf12.yaml v2.03 removed the light entities and replaced them with switch entities to toggle lights and number entities to control brightness.  This change reduced the binary size and OTA should now work with API encryption without a problem.

If you are still getting the message `ERROR Error binary size: Error: ESP does not have enough space to store OTA file.`, we recommend that you remove API encryption by commenting out or deleting the following lines:

```
# api:
#   encryption:
#     key: ...
```
If you want to keep API encryption, you can flash first with the kauf-plf12-minimal.yaml file each time you need to update or upgrade, and then revert back to the kauf-plf12.yaml.

### Additional Troubleshooting

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
