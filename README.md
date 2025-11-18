# ha-laundry-cards
Home Assistant Laundry Cards

<img width=400px src="https://github.com/user-attachments/assets/83477a84-c60e-4524-a84a-b05ee8bc6888"/>  

</br>  
</br>  

Based on the amazing work by phrz [here](https://github.com/phrz/lg-washer-dryer-card).

> [!TIP]
> I'm using the LG Thinq integration plus a smart plug for my (dumb) Dryer  

1. Copy [files](https://github.com/jasonpstokes/ha-laundry-cards/tree/main/www) into /config/www 

2. Add **7segment** font  
a) Edit Dashboard  
b) Click three dots  
c) click **Manage Resources**  
d) click **Add Resource**  
URL: /local/7segment.css  
Resource type: Styleshet  
</br>

3. Create Template Helper for HH:MM remaining,

e.g. Dishwasher Time Remaining  
```yaml
{% set m = ((as_datetime(states('sensor.dishwasher_remaining_time'),now()) - now()).total_seconds() //60)|int(0) + ((as_datetime(states('sensor.dishwasher_delay_starts_in'),now()) - now()).total_seconds() //60)|int(0) %}
{{ -1 if m < 0 else ((m//60) ~ ':' ~ '{:0>2d}').format(m%60) }}
```
Availability template: 
```yaml
{{ states('sensor.dishwasher_remaining_time') not in ['unavailable', 'unknown', None] }}
```
</br>  

# Cards

## Dishwasher Card
```yaml
type: picture-elements
image: /local/dishwasher.svg
dark_mode_image: /local/dishwasher-dark.svg
elements:
  - type: image
    entity: sensor.dishwasher_current_status
    image: /local/lg-icons/wash-plate.png
    state_image:
      running: /local/lg-icons/wash-plate-on.png
    style:
      top: 17%
      left: 40%
      width: 25%
      image-rendering: crisp
  - type: image
    entity: sensor.dishwasher_current_status
    image: /local/lg-icons/rinse-plate.png
    state_image:
      rinsing: /local/lg-icons/rinse-plate-on.png
    style:
      top: 17%
      left: 61%
      width: 25%
      image-rendering: crisp
  - type: image
    entity: sensor.dishwasher_current_status
    image: /local/lg-icons/dry-plate.png
    state_image:
      drying: /local/lg-icons/dry-plate-on.png
    style:
      top: 17%
      left: 80%
      width: 25%
      image-rendering: crisp
  - type: image
    entity: sensor.dishwasher_current_status
    image: /local/lg-icons/delay-start.png
    state_image:
      reserved: /local/lg-icons/delay-start-on.png
      pause: /local/lg-icons/delay-start-on.png
    style:
      top: 33%
      left: 21%
      width: 25%
      image-rendering: crisp
  - type: image
    entity: binary_sensor.dishwasher
    image: /local/lg-icons/wifi.png
    state_image:
      "on": /local/lg-icons/wifi-on.png
    style:
      top: 37%
      left: 48%
      width: 10%
      image-rendering: crisp
  - type: state-label
    entity: sensor.blank
    prefix: "8:88"
    style:
      color: "#5555"
      font-family: segment7
      font-size: 2em
      top: 35%
      left: 72%
    tap_action:
      action: none
    hold_action:
      action: none
  - type: conditional
    conditions:
      - entity: sensor.dishwasher_remaining_time
        state_not:
          - unknown
          - unavailable
    elements:
      - type: state-label
        entity: sensor.dishwasher_time_remaining
        style:
          color: "#8df427"
          font-family: segment7
          font-size: 2em
          top: 35%
          left: 72%
        tap_action:
          action: none
        hold_action:
          action: none
grid_options:
  columns: 4
  rows: auto
```
</br>  

## Washing Machine Card
```yaml
type: picture-elements
image: /local/washing-machine.svg
dark_mode_image: /local/washing-machine-dark.svg
elements:
  - type: image
    entity: sensor.washing_machine_current_status
    image: /local/lg-icons/sensing.png
    state_image:
      detecting: /local/lg-icons/sensing-on.png
    style:
      top: 17%
      left: 20%
      width: 25%
      image-rendering: crisp
  - type: image
    entity: sensor.washing_machine_current_status
    image: /local/lg-icons/wash.png
    state_image:
      running: /local/lg-icons/wash-on.png
    style:
      top: 17%
      left: 40%
      width: 25%
      image-rendering: crisp
  - type: image
    entity: sensor.washing_machine_current_status
    image: /local/lg-icons/rinse.png
    state_image:
      rinsing: /local/lg-icons/rinse-on.png
    style:
      top: 17%
      left: 61%
      width: 25%
      image-rendering: crisp
  - type: image
    entity: sensor.washing_machine_current_status
    image: /local/lg-icons/spin.png
    state_image:
      spinning: /local/lg-icons/spin-on.png
    style:
      top: 17%
      left: 80%
      width: 25%
      image-rendering: crisp
  - type: image
    entity: sensor.washing_machine_current_status
    image: /local/lg-icons/delay-end.png
    style:
      top: 33%
      left: 21%
      width: 25%
      image-rendering: crisp
  - type: image
    entity: binary_sensor.washing_machine
    image: /local/lg-icons/wifi.png
    state_image:
      "on": /local/lg-icons/wifi-on.png
    style:
      top: 37%
      left: 48%
      width: 10%
      image-rendering: crisp
  - type: state-label
    entity: sensor.blank
    prefix: "8:88"
    style:
      color: "#5555"
      font-family: segment7
      font-size: 2em
      top: 35%
      left: 72%
    tap_action:
      action: none
    hold_action:
      action: none
  - type: conditional
    conditions:
      - entity: sensor.washing_machine_remaining_time
        state_not:
          - unknown
          - unavailable
      - entity: sensor.washing_machine_delay_ends_in
        state: unknown
    elements:
      - type: state-label
        entity: sensor.washing_machine_time_remaining
        style:
          color: "#8df427"
          font-family: segment7
          font-size: 2em
          top: 35%
          left: 72%
        tap_action:
          action: none
        hold_action:
          action: none
  - type: conditional
    conditions:
      - entity: sensor.washing_machine_delay_ends_in
        state_not:
          - unknown
          - unavailable
    elements:
      - type: image
        entity: sensor.washing_machine_current_status
        image: /local/lg-icons/delay-end-on.png
        style:
          top: 33%
          left: 21%
          width: 25%
          image-rendering: crisp
      - type: state-label
        entity: sensor.washing_machine_time_remaining_delay
        style:
          color: "#8df427"
          font-family: segment7
          font-size: 2em
          top: 35%
          left: 72%
        tap_action:
          action: none
        hold_action:
          action: none
grid_options:
  columns: 4
  rows: auto
```
</br>  

## Washing Machine Card
```yaml
type: picture-elements
image: /local/dryer.svg
dark_mode_image: /local/dryer-dark.svg
elements:
  - type: image
    entity: sensor.dryer
    image: /local/lg-icons/dry.png
    state_image:
      dry: /local/lg-icons/dry-on.png
    style:
      top: 17%
      left: 60%
      width: 25%
      image-rendering: crisp
  - type: image
    entity: sensor.dryer
    image: /local/lg-icons/cool.png
    state_image:
      cool: /local/lg-icons/cool-on.png
    style:
      top: 17%
      left: 80%
      width: 25%
      image-rendering: crisp
  - type: state-label
    entity: sensor.blank
    prefix: "88"
    style:
      color: "#5555"
      font-family: segment7
      font-size: 2em
      top: 35%
      left: 79%
    tap_action:
      action: none
    hold_action:
      action: none
  - type: conditional
    conditions:
      - entity: sensor.dryer
        state:
          - dry
          - cool
    elements:
      - type: state-label
        entity: sensor.blank
        prefix: "On"
        style:
          color: "#8df427"
          font-family: segment7
          font-size: 2em
          top: 35%
          left: 79%
        tap_action:
          action: none
        hold_action:
          action: none
grid_options:
  columns: 4
  rows: auto
```
