The preloaded policy table located at [src/appMain](https://github.com/smartdevicelink/sdl_core/blob/master/src/appMain/sdl_preloaded_pt.json) can be configured before your first run of SDL to set permissions levels and urls.

!!! note

To configure SDL using the preloaded policy table after your first run, remove the `storage/` folder from `build/src/appMain`

!!!

Let's take a look at the values that can be configured.

## Module Config

The module config section contains some global defaults that can be set for SDL

### Exchange After X Ignition Cycles

An "Exchange" is when SDL sends a request to a connected application to retrieve a new policy table from the server. This value is the number of ignition cycles before SDL initiates an exchange.

### Exchange After X Kilometers

The distance traveled in the vehicle before SDL initiates an exchange

### Exchange After X Days

The number of days that has passed before SDL initiated an exchange

### Timeout After X Seconds

The amount of time SDL will wait for an exchange to complete before timing out and retrying

### Seconds Between Retries

A list of times in seconds to wait after a failed policy table exchange before trying again. The number of items in this list determines the number of policy table retries.

### Endpoints

This section is a list of urls that is used throughout SDL

#### 0x07

A list of urls that can be used for policy table exchanges

#### 0x04

A list of urls that can be used to retrieve software updates

#### queryAppsUrl

A list of urls that can be used to receive valid apps for querying on iOS devices

#### lock_screen_icon_url

A list of urls that host an image which can be displayed by the application on the driver's device during lockout. This url is sent in a request after each application is registered. The application proxy downloads the image and sends a notification to the application with the image to be displayed during lockout.

### Notification Per Minute

## Functional Groupings

The functional groupings are the different named groups of rpc permissions that an application can have. There can be any number of functional groups. The functional groups are used in the next section to define behavior for different applications.

## App Policies

The app policies are permissions that each application has on the system. This is where you would change the default permissions for an application, or add policies for a specific application.