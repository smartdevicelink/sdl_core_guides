# Vehicle Data

Vehicle data can be exposed to app developers by creating a `VehicleInfo` component within your HMI. The primary RPCs used by this component are:

* VehicleInfo.GetVehicleData - A request from Core to retrieve specific vehicle data items from the system.
* VehicleInfo.SubscribeVehicleData - A request from Core to receive periodic updates for specific vehicle data items from the system.
* VehicleInfo.UnsubscribeVehicleData - A request from Core to stop receiving periodic updates for specific vehicle data items from the system.
* VehicleInfo.OnVehicleData - A notification from the HMI indicating that one or more of the subscribed vehicle data items were updated.

More information regarding this component is available in the VehicleInfo section of the [HMI Documentation](https://smartdevicelink.com/en/guides/hmi/overview/)

## Available Vehicle Data Items

Below is a list of all of the vehicle data items which are available via SDL as of [Release 6.1.0](https://github.com/smartdevicelink/sdl_core/releases/tag/6.1.0) of SDL Core. New vehicle data items are proposed regularly via the [SDL Evolution process](https://github.com/smartdevicelink/sdl_evolution).

|Name|Result Type|Description|
|accPedalPosition|Float|Accelerator pedal position (as a number from 0 to 100 representing percentage depressed)|
|beltStatus|[Common.BeltStatus](https://smartdevicelink.com/en/guides/hmi/common/structs/#beltstatus)|The status of each of the seat belts in the vehicle|
|bodyInformation|[Common.BodyInformation](https://smartdevicelink.com/en/guides/hmi/common/structs/#bodyinformation)|The body information for the vehicle, including information such as ignition status and door status|
|cloudAppVehicleID|String|Parameter used by cloud apps to identify a head unit|
|deviceStatus|[Common.DeviceStatus](https://smartdevicelink.com/en/guides/hmi/common/structs/#devicestatus)|The device status, including information such as signal and battery strength|
|driverBraking|[Common.VehicleDataEventStatus](https://smartdevicelink.com/en/guides/hmi/common/enums/#vehicledataeventstatus)|The status of the brake pedal|
|electronicParkBrakeStatus|[Common.ElectronicParkBrakeStatus](https://smartdevicelink.com/en/guides/hmi/common/enums/#electronicparkbrakestatus)|The status of the park brake as provided by Electric Park Brake (EPB) system|
|engineOilLife|Float|The estimated percentage of remaining oil life of the engine|
|engineTorque|Float|Torque value for the engine (in N*m) on non-diesel variants|
|externalTemperature|Float|The external temperature in degrees celsius|
|fuelLevel|Float|The fuel level in the tank (as a percentage value)|
|fuelLevel_State|[Common.ComponentVolumeStatus](https://smartdevicelink.com/en/guides/hmi/common/enums/#componentvolumestatus)|The status value corresponding to the general fuel level in the tank|
|fuelRange|[Common.FuelRange](https://smartdevicelink.com/en/guides/hmi/common/structs/#fuelrange)|The estimate range in KM the vehicle can travel based on fuel level and consumption. Contains information on all fuel sources available to the vehicle (eg. GASOLINE and BATTERY for hybrid vehicles).|
|gps|[Common.GPSData](https://smartdevicelink.com/en/guides/hmi/common/structs/#gpsdata)|Location data from the onboard GPS in the vehicle|
|headLampStatus|[Common.HeadLampStatus](https://smartdevicelink.com/en/guides/hmi/common/structs/#headlampstatus)|The current status of each of the head lamps|
|instantFuelConsumption|Float|The instantaneous fuel consumption of the vehicle in microlitres|
|odometer|Integer|The odometer value in kilometers|
|prndl|[Common.PRNDL](https://smartdevicelink.com/en/guides/hmi/common/enums/#prndl)|The current status of the gear shifter|
|rpm|Integer|The number of revolutions per minute of the engine|
|speed|Float|The vehicle speed in kilometers per hour|
|steeringWheelAngle|Float|The current angle of the steering wheel (in degrees)|
|tirePressure|[Common.TireStatus](https://smartdevicelink.com/en/guides/hmi/common/structs/#tirestatus)|Status information for each of the vehicle's tires|
|turnSignal|[Common.TurnSignal](https://smartdevicelink.com/en/guides/hmi/common/enums/#turnsignal)|The current state of the turn signal indicator|
|vin|String|Vehicle identification number|
|wiperStatus|[Common.WiperStatus](https://smartdevicelink.com/en/guides/hmi/common/enums/#wiperstatus)|The current status of the wipers|

## Custom Vehicle Data Items

Starting with [SDL Core version 6.0.0](https://github.com/smartdevicelink/sdl_core/releases/tag/6.0.0), custom vehicle data items can be defined via the policy table. These items are structured in a similar manner to the [MOBILE API](https://github.com/smartdevicelink/sdl_core/blob/master/src/components/interfaces/MOBILE_API.xml) and contained in the `vehicle_data` section of the policy table. For example:

```
"vehicle_data": {
    "schema_version": "1.0.0",
    "schema_items": [
        ...
        {
            "name": "customString",
            "key": "KEY_CUSTOM_STRING",
            "minlength": 0,
            "maxlength": 100,
            "type": "String",
            "mandatory": false
        },
        {
            "name": "customInt",
            "key": "KEY_CUSTOM_INT",
            "minvalue": 0,
            "maxvalue": 100,
            "type": "Integer",
            "mandatory": false
        },
        {
            "name": "customFloat",
            "key": "KEY_CUSTOM_FLOAT",
            "minvalue": 0.0,
            "maxvalue": 100.0,
            "type": "Float",
            "mandatory": false
        },
        {
            "name": "customBool",
            "key": "KEY_CUSTOM_BOOL",
            "type": "Boolean",
            "mandatory": false
        },
        {
            "name": "customArray",
            "key": "KEY_CUSTOM_ARRAY",
            "type": "String",
            "array": true,
            "minsize": 0,
            "maxsize": 100,
            "mandatory": false
        },
        {
            "name": "customStruct",
            "params": [
                {
                    "name": "customStructVal",
                    "key": "KEY_CUSTOM_STRUCT_VAL",
                    "type": "String",
                    "mandatory": true
                },
                {
                    "name": "customStructVal2",
                    "key": "KEY_CUSTOM_STRUCT_VAL2",
                    "minvalue": 0,
                    "maxvalue": 100,
                    "type": "Integer",
                    "mandatory": true
                },
                {
                    "name": "customDeprecatedVal",
                    "key": "KEY_CUSTOM_DEPRECATED_VAL",
                    "minvalue": 0,
                    "maxvalue": 100,
                    "type": "Integer",
                    "mandatory": true,
                    "until": "7.0"
                },
                {
                    "name": "customDeprecatedVal",
                    "key": "KEY_CUSTOM_DEPRECATED_VAL",
                    "minvalue": 0,
                    "maxvalue": 100,
                    "type": "Integer",
                    "mandatory": true,
                    "deprecated": true
                    "since": "7.0"
                }
            ],
            "key": "KEY_CUSTOM_STRUCT",
            "type": "Struct",
            "mandatory": false
        }
    ]
}
```

In addition to custom items, this feature can be used to expose other vehicle data items that were introduced to the project in later versions. This can be useful when the software version on the head unit cannot be updated easily. If a vehicle data item is added into the project, the definition of this item will be included in the policy table by default. Any vehicle data items which are defined in Core's local Mobile API will be ignored from the policy table, but newer items will be interpreted as custom items. This allows apps to use these data items normally if they are exposed by the head unit, even when they were not initially supported.

### Custom Vehicle Data Requests

Custom vehicle data requests have a separate structure to normal vehicle data requests. While normal vehicle data items are requested using the key structure of `"<item.name>: true"`, custom items are constructed using the `item.key` field and can have a nested structure (when requesting `Struct` items). For example, when requesting all of the vehicle data items which are defined above, the HMI would receive the following message:

```
{
    "id" : 139,
    "jsonrpc" : "2.0",
    "method" : "VehicleInfo.GetVehicleData",
    "params" : {
        "KEY_CUSTOM_STRING": true,
        "KEY_CUSTOM_INT": true,
        "KEY_CUSTOM_FLOAT": true,
        "KEY_CUSTOM_BOOL": true,
        "KEY_CUSTOM_ARRAY": true,
        "KEY_CUSTOM_STRUCT": {
            "KEY_CUSTOM_STRUCT_VAL": true,
            "KEY_CUSTOM_STRUCT_VAL2": true,
            "KEY_CUSTOM_DEPRECATED_VAL": true
        }
    }
}
```

### Vehicle Data Mapping File

Since these keys may not be immediately known by the HMI, a vehicle data mapping file can be used to connect these keys to actual readable values from the vehicle. The location where this file is hosted can be specified in the policy table in the `module_config.endpoints.custom_vehicle_data_mapping` field (see [Policy Endpoints](https://smartdevicelink.com/en/guides/sdl-overview-guides/policies/policy-fields/#endpoints)). The format of this file is OEM-defined.

!!! NOTE
In order for the HMI to determine when this file needs to be updated, this file can be assigned a version via the `module_config.endpoint_properties.custom_vehicle_data_mapping.version` field. The HMI can retrieve this field using the [SDL.GetPolicyConfigurationData](https://smartdevicelink.com/en/guides/hmi/sdl/getpolicyconfigurationdata/) RPC.
!!!

