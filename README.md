[![hacs_badge](https://img.shields.io/badge/HACS-Custom-orange.svg?style=for-the-badge)](https://github.com/custom-components/hacs)

# custom_components/smart_thermostat

## PID controller thermostat

### Installation:
1. Go to <conf-dir> default /homeassistant/.homeassistant/ (it's where your configuration.yaml is)
2. Create <conf-dir>/custom_components/ directory if it does not already exist
3. Clone this repository content into <conf-dir>/custom_components/
4. Set up the smart_thermostat and have fun

### Usage:
pid controller will be called periodically.
If no pwm interval is defined, it will set the state of "heater" from 0 to "difference" value. Else, it will turn off and on the heater proportionally.

#### Autotune:
You can use the autotune feature to set the PID parameters.

Issue! I'couldn't yet save the settings to the configuration.yaml file.
The PID parmaters set by the autotune won't be stored and the process restarts everytime hassio is restarted.
To save the parameters read log, it will be shown like this:
LOGGER.info("Set Kp, Ki, Kd. Smart thermostat now runs on PID Controller." self.kp , self.ki, self.kd)

### Parameters:

* name (Required): Name of thermostat.
* heater (Required): entity_id for heater switch, must be a toggle device. Becomes air conditioning switch when ac_mode is set to True.
* target_sensor (Required): entity_id for a temperature sensor, target_sensor.state must be temperature.
* min_temp (Optional): Set minimum set point available (default: 7).
* max_temp (Optional): Set maximum set point available (default: 35).
* target_temp (Optional): Set initial target temperature. Failure to set this variable will result in target temperature being set to null on startup. As of version 0.59, it will retain the target temperature set before restart if available.
* ac_mode (Optional): Set the switch specified in the heater option to be treated as a cooling device instead of a heating device.
* initial_hvac_mode (Optional): Set the initial operation mode. Valid values are off or auto.
* away_temp (Optional): Set the temperature used by “away_mode”. If this is not specified, away_mode feature will not get activated. 
* keep_alive (Required): Set a keep-alive interval. Interval pid controller will be updated.
* kp (Optional): Set PID parameter, p control value.
* ki (Optional): Set PID parameter, i control value.
* kd (Optional): Set PID parameter, d control value.
* pwm (Optional): Set period time for pwm signal in seconds. If it's not set, pwm is disabled.
* autotune (Optional): Choose a string for autotune settings.  If it's not set autotune is disabled.

tuning_rules | Kp_divisor, Ki_divisor, Kd_divisor
------------ | -------------
"ziegler-nichols" | 34, 40, 160
"tyreus-luyben" | 44,  9, 126
"ciancone-marlin" | 66, 88, 162
"pessen-integral" | 28, 50, 133
"some-overshoot" | 60, 40,  60
"no-overshoot" | 100, 40,  60
"brewing" | 2.5, 6, 380

* difference (Optional): Set analog output offset to 0 (default 100). If it's 500 the output Value can be everything between 0 and 500.
* noiseband (Optional): (default 0.5) Set noiseband (float).Determines by how much the input value must overshoot/undershoot the setpoint before the state changes during autotune.
* autotune_lookback (Optional): (default 60s). The reference period in seconds for local minima/maxima.
* autotune_control_type (Optional): (default none). Disables the
tuning rules and sets the Ziegler-Nichols control type     according to: https://en.wikipedia.org/wiki/Ziegler%E2%80%93Nichols_method
  
  Possible values: p, pi, pd, classic_pid, pessen_integral_rule, 
                    some_overshoot, no_overshoot 
* heat_meter (Optional): Sets a simple heat meter in the specified entity_id. It enables to track heating consumption via the utility meter integration.

#### configuration.yaml
```
climate:
  - platform: smart_thermostat
    name: Termostato PID
    heater: switch.tradfri_outlet
    target_sensor: sensor.esp32_temperature
    min_temp: 15
    max_temp: 50
    ac_mode: False
    target_temp: 28
    keep_alive:
      seconds: 1
    initial_hvac_mode: "off"
    away_temp: 16
    kp: 152.28114156300958
    ki: 0.5104552705380692
    kd: 11357.286041580755
    pwm: 20
    autotune: ziegler-nichols
    autotune_lookback: 60
    autotune_control_type: "classic_pid"
    difference: 100
    noiseband: 0.5
    heat_meter: variable.mean_heating_value
```
### Help

The python PID module:
[https://github.com/hirschmann/pid-autotune](https://github.com/hirschmann/pid-autotune)

PID controller explained. Would recommoned to read some of it:
[https://controlguru.com/table-of-contents/](https://controlguru.com/table-of-contents/)

PID controller and Ziegler-Nichols method:
[https://electronics.stackexchange.com/questions/118174/pid-controller-and-ziegler-nichols-method-how-to-get-oscillation-period](https://electronics.stackexchange.com/questions/118174/pid-controller-and-ziegler-nichols-method-how-to-get-oscillation-period)

Ziegler–Nichols Tuning:
[https://www.allaboutcircuits.com/projects/embedded-pid-temperature-control-part-6-zieglernichols-tuning/](https://www.allaboutcircuits.com/projects/embedded-pid-temperature-control-part-6-zieglernichols-tuning/)
