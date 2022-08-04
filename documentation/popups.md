
## Anatomy of a popup

```yaml
service: browser_mod.popup
data:
  title: The title
  content: The content
  right_button: Right button
  left_button: Left button
```

![Dialog Anatomy](https://user-images.githubusercontent.com/1299821/182708739-f89e3b2b-199f-43e0-bf04-e1dfc7075b2a.png)

## Size

The `size` parameter can be set to `normal`, `wide` and `fullscreen` with results as below (background blur has been exagerated for clarity):

![Normal Size](https://user-images.githubusercontent.com/1299821/182709146-439814f1-d479-4fc7-aab1-e28f5c9a13c7.png)

![Wide Size](https://user-images.githubusercontent.com/1299821/182709172-c98a9c23-5e58-4564-bcb7-1d187842948f.png)

![Fullscreen Size](https://user-images.githubusercontent.com/1299821/182709224-fb2e7b92-8a23-4422-95a0-f0f2835909e0.png)


## HTML content

```yaml
service: browser_mod.popup
data:
  title: HTML content
  content: |
    An <b>HTML</b> string.
    <p> Pretty much any HTML works: <ha-icon icon="mdi:lamp" style="color: red;"></ha-icon>
```

![HTML content](https://user-images.githubusercontent.com/1299821/182710044-6fea3ba3-5262-4361-a131-691770340518.png)

## Dashboard card content

```yaml
service: browser_mod.popup
data:
  title: HTML content
  content:
    type: entities
    entities:
      - light.bed_light
      - light.ceiling_lights
      - light.kitchen_lights
```

![Card content](https://user-images.githubusercontent.com/1299821/182710445-f09b74b8-dd53-4d65-8eba-0945fc1d418e.png)

## Form content
`content` can be a list of ha-form schemas and the popup will then contain a form for user input:

```
<ha-form schema>:
  name: <string>
  [label: <string>]
  [default: <any>]
  selector: <Home Assistant Selector>
```

| | |
|-|-|
| `name` | A unique parameter name |
| `label` | A description of the parameter |
| `default` | The default value for the parameter |
| `selector` | A [Home Assistant selector](https://www.home-assistant.io/docs/blueprint/selectors) |

The data from the form will be forwarded as data for any `right_button_action` or `left_button_action` of the popup.

```yaml
service: browser_mod.popup
data:
  title: Form content
  content:
    - name: parameter_name
      label: Descriptive name
      selector:
        text: null
    - name: another_parameter
      label: A number
      default: 5
      selector:
        number:
          min: 0
          max: 10
          slider: true
```

![Form content](https://user-images.githubusercontent.com/1299821/182712670-f3b4fdb7-84a9-49d1-a26f-2cdaa450fa0e.png)

## Actionable popups

Example of a popup with actions opening more popups or calling Home Assistant services:

```yaml
service: browser_mod.popup
data:
  content: Do you want to turn the light on?
  right_button: "Yes"
  left_button: "No"
  right_button_action:
    service: light.turn_on
    data:
      entity_id: light.bed_light
  left_button_action:
    service: browser_mod.popup
    data:
      title: Really?
      content: Are you sure?
      right_button: "Yes"
      left_button: "No"
      right_button_action:
        service: browser_mod.popup
        data:
          content: Fine, live in darkness.
          dismissable: false
          title: Ok
          timeout: 3000
      left_button_action:
        service: light.turn_on
        data:
          entity_id: light.bed_light
```

![Multi-level popup](https://user-images.githubusercontent.com/1299821/182713421-708d0026-bcfa-4ba6-bbcd-3b85b584162d.gif)

## Forward form data

The following popup would ask the user for a list of rooms to vacuum and then populate the `params` parameter of the `vacuum.send_command` service call from the result:

```yaml
service: browser_mod.popup
data:
  title: Where to vacuum?
  right_button: Go!
  right_button_action:
    service: vacuum.send_command
    data:
      entity_id: vacuum.xiaomi
      command: app_segment_clean
  content:
    - name: params
      label: Rooms to clean
      selector:
        select:
          multiple: true
          options:
            - label: Kitchen
              value: 11
            - label: Living room
              value: 13
            - label: Bedroom
              value: 12
```

![Vacuum popup](https://user-images.githubusercontent.com/1299821/182713714-ef4149b1-217a-4d41-9737-714f5320c25c.png)