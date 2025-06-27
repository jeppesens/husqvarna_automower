# Home Assistant integration for Husqvarna Automower

![Maintenance](https://img.shields.io/maintenance/yes/2023.svg)
[![buy me a coffee](https://img.shields.io/badge/If%20you%20like%20it-Buy%20me%20a%20coffee-orange.svg)](https://www.buymeacoffee.com/Thomas55555)
[![downloads](https://img.shields.io/github/downloads/Thomas55555/husqvarna_automower_map/total.svg)](https://img.shields.io/github/downloads/Thomas55555/husqvarna_automower_map/total.svg)

Custom component to support Automower.


- [Home Assistant integration for Husqvarna Automower](#home-assistant-integration-for-husqvarna-automower)
  - [About](#about)
  - [Supported devices](#supported-devices)
  - [Installation](#installation)
    - [Installation through HACS](#installation-through-hacs)
    - [Manual installation](#manual-installation)
  - [Configuration](#configuration)
    - [Husqvarna API-Key](#husqvarna-api-key)
    - [Home Assistant](#home-assistant)
    - [Image Sensor](#cimage-sensor)
      - [Example of map image](#example-of-map-image)
      - [Example of map image with zones enabled](#example-of-map-image-with-zones-enabled)
    - [Zone Sensor](#zone-sensor)
  - [Usage](#usage)
    - [Services](#services)
  - [Debugging](#debugging)
  - [Troubleshooting](#troubleshooting)
    - [Remove Credentials](#remove-credentials)
    - [Error: The component is not configured...](#error-the-component-is-not-configured)

## About

This Home Assistant integration provides status and control of supported Husqvarna  Automowers.  The official Husqvarna  [API](https://developer.husqvarnagroup.cloud/) uses websocket connection for pushed updates, so no polling is performed.  Park and Start commands including schedule overrides are supported by the integration allowing for robust automations to be implemented in Home Assistant.  Diagnostic and statics provided by the API are included with the integration for monitoring mower usage and performance.

![Screenshot of the integration](/images/screenshot_husqvarna_automower.png)

## Supported devices

Husqvarna Automowers with built-in *Automower® Connect* or with the *Automower® Connect Module* are supported.  This integration does not support Bluetooth connectivity with Automowers.


## Installation

Requires Home Assistant 2023.7.0 or newer.

### Installation through HACS

Installation using Home Assistant Community Store (HACS) is recommended.

1. If HACS is not installed, follow HACS installation and configuration at https://hacs.xyz/.

2. In HACS, search under integrations for Husqvarna Automower and install.

3. Restart Home Assistant!

### Manual installation

1. Download the `husqvarna_automower_map.zip` file from the repository [release section](https://github.com/Thomas55555/husqvarna_automower_map/releases).

2. Extract and copy the content into the path `/config/custom_components/husqvarna_automower_map` of your HA installation.

   Do **not** download directly from the `main` branch.

3. Restart Home Assistant!

## Configuration


### Husqvarna API-Key

In order to use this integration you must properly configure OAuth2 credentials using your Husqvarna account.  Refer to [this guide](https://developer.husqvarnagroup.cloud/docs/get-started) for general overview of the process.  Username/password authentication for this integration is no longer supported as of version 2022.7.0.

Your Husqvarna account username/password used for the *Automower® Connect*  phone app is required.  Most users probably created a Husqvarna account during initial mower setup.

1. Make sure, that the external url on the page `Settings -> Network` in the field `Internet` is set to your external url.

2. Go to <https://developer.husqvarnagroup.cloud/> and sign in with Husqvarna account.  Sign in page has password recovery/reset using registered email address if needed.  Authorize *Developer Porthole* to access Husqvarna account when prompted.

3. After signing in you will be automatically redirected to "Your applications". (Otherwise go to: <https://developer.husqvarnagroup.cloud/applications>)

4. Create a new application:

   * Name is required but can be anything, for example "My Home Assistant"

   * Description is optional

   * Redirect URL:

     ```
     https://my.home-assistant.io/redirect/oauth
     ```

     Confirm no extra spaces were appended at end of URL from copy and paste.

   * Click **CREATE**.  *Application Key* and *Application Secret* will be generated and shown.  Protect these like a username and password.

5. Click on **CONNECT NEW API** and connect the **Authentication API**.

6. Click on **CONNECT NEW API** again and connect the **Husqvarna Automower API**.

7. Leave this tab open in browser and continue with Home Assistant configuration.

### Home Assistant

The My Home Assistant redirect feature needs to be setup to redirect to your home assistant installation.  See https://my.home-assistant.io/faq/ for additional information.

1. Add the integration to your home assistant installation and test the redirect feature by following below link:
   [![my_button](https://my.home-assistant.io/badges/config_flow_start.svg)](https://my.home-assistant.io/redirect/config_flow_start/?domain=husqvarna_automower_map)

2. Acknowledge prompts to open link, install Husqvarna Automower integration.

3. Acknowledge prompt to setup application credentials.

4. Enter the following from the Husqvrana developer tab:

   * The name of the application assigned in Step 3 above
   * Copy and paste the *Application Key* into the *OAuth Client ID* field
   * Copy and paste the *Application Secret* into the *OAuth Client Secret* field

5. Click **Create**

6. Browser will be redirected to Husqvarna Developer site.  Sign in and Authorize the integration to connect with your Husqvarna account

7. After authorizing the integration the browser will show the my Home Assistant redirect link to link this account.  Click on **Link Account**.

8. Confirm successful connection of mower and assign to an HA area if desired.

### Image Sensor

#### [Detailed Instruction for creating and configuring the map image](MAP_GUIDE.md)

#### Example of map image
![Example of image](/images/map_image.png)

#### Example of map image with zones enabled
![Example of image with zones enabled](/images/map_image_zone.png)

The image entity is disabled by default.  The image entity will plot the current coordinates and location history of the mower on a user provided image. To configure the entity you need to upload your desired map image and determine the coordinates of the top left corner and the bottom right corner of your selected image.

The image entity is configured via the configure option on the integration. To enter the coordinates, ensure that they are in Signed Degree format and separated by a comma for example `40.689209, -74.044661`

You can then provide the path to the image you would like to use for the map and mower.  This has been tested with the PNG format, other formats may work.  The `.../resources/map_image.png` default image is over written when the integration is updated, store the custom image in another location.

The path color can be changes by providing an RGB value such as (255,0,0).


### Zone Sensor

The optional zone sensor allows zones to be designated by coordinates, this sensor will then return the name of the zone the mower is currently located.

To create a Zone, select new then enter a name for the zone and the coordinates of the zone.  Coordinates are entered in Signed Degree format with latitude and longitude separated by a comma and each coordinate separated by a semi colon. You must enter at least three coordinates to define a zone. For example: ```40.689209, -74.044661; 40.689210, -74.044652; 40.689211, -74.044655``` You must select save and then submit, exiting the flow in another manner will cause any entered zones to be lost.

If display zone is selected the zone will be drawn as an overlay on the map image in the provided RGB color.  To change the color provide an RGB string such as (255,255,255).

If a Home Zone is set, the sensor will return Home and the image will display the mower at the home location, when the mower is charging or at the docking station.

## Usage

* `vacuum.start`
  The mower continues to mow, within the specified schedule

* `vacuum.pause`
  Pauses the mower until a new command

* `vacuum.stop`
  The mower returns to the base and parks there until the next schedule starts

* `vacuum.return_to_base`
  The mower returns to the base and parks there until it gets a new start command

* `number.automower_park_for`
  Override schedule to park mower for specified number of minutes.

* `number.automower_mow_for`
  Override schedule to mow for specified number of minutes.

* `number.automower_cutting_height`
  Set mower cutting height.

* `select.automower_headlight_mode`
  Set the mower headlight operating mode


### Services

* `husqvarna_automower_map.calendar`
  Allows mower schedule to be revised.  Supports single schedule per day, this will override existing schedule.

  ```
  service: husqvarna_automower_map.calendar
  data:
    start: '11:45:00'
    end: '21:30:00'
    monday: true
    tuesday: true
    wednesday: true
    thursday: true
    friday: true
    saturday: false
    sunday: false
  target:
    entity_id: vacuum.automower
  ```
  `start` must be less than `end`.  Seconds are ignored.

* `husqvarna_automower_map.custom_command`
  Allows custom JSON formatted commands to be sent.

  Example equivalent to `vacuum.start`
  ```
  service: husqvarna_automower_map.custom_command
  data:
    command_type: actions
    json_string: >-
      {
       "data": {"type": "ResumeSchedule"}
      }
  target:
    entity_id: vacuum.automower
  ```
  See Husqvarna [API reference](https://developer.husqvarnagroup.cloud/apis/Automower+Connect+API#/swagger) for additional details.

## Debugging

To enable debug logging for this integration and related libraries you can control this in your Home Assistant `configuration.yaml` file.

Example:

```
logger:
  default: info
  logs:
    custom_components.husqvarna_automower_map: debug
    aioautomower: debug
```

After a restart detailed log entries will appear in `/config/home-assistant.log`.

## Troubleshooting

### Remove Credentials

The OAuth2 credentials can be removed from the home assistant user interface.  Navigate to the Integrations tab under settings.  Access the *Application Credentials* menu  by clicking on the Kebab (3 vertical dot menu icon)  or [direct link](https://my.home-assistant.io/redirect/application_credentials/).

### Error: The component is not configured...

[Dialog box](https://user-images.githubusercontent.com/28894450/178799277-ac61b8c7-4f53-4371-9afe-7ee9ee7e9a49.png) stating the component is not configured.  Please follow the documentation.  This error is resolved by removing OAuth2 credentials
