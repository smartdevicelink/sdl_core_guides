# Vehicle Data

Vehicle data can be exposed to app developers by creating a `VehicleInfo` component within your HMI. To communicate with this component, you will first need to register it with the message broker and respond to the `VehicleInfo.IsReady` message from SDL (see [HMI Getting Started](https://smartdevicelink.com/en/guides/hmi/getting-started/) for more information).

## RPCs

The primary RPCs used by this component are:

### VehicleInfo.GetVehicleData

**Description:**

A request from Core to retrieve specific vehicle data items from the system.

**Example Request:**
```json
{
    "id": 123,
    "jsonrpc": "2.0",
    "method": "VehicleInfo.GetVehicleData",
    "params" : {
        "speed" : true
    }
}
```

**Example Response:**
```json
{
    "id": 123,
    "jsonrpc": "2.0",
    "result" : {
        "speed" : 100
    }
}
```

### VehicleInfo.SubscribeVehicleData

**Description:**

A request from Core to receive periodic updates for specific vehicle data items from the system.

**Example Request:**
```json
{
    "id": 123,
    "jsonrpc": "2.0",
    "method": "VehicleInfo.SubscribeVehicleData",
    "params" : {
        "speed" : true
    }
}
```

**Example Response:**
```json
{
    "id": 123,
    "jsonrpc": "2.0",
    "result" : {
        "speed" : {
            "dataType" : "VEHICLEDATA_SPEED",
            "resultCode" : "SUCCESS"
        }
    }
}
```

### VehicleInfo.UnsubscribeVehicleData

**Description:**

A request from Core to stop receiving periodic updates for specific vehicle data items from the system.

**Example Request:**
```json
{
    "id": 123,
    "jsonrpc": "2.0",
    "method": "VehicleInfo.UnsubscribeVehicleData",
    "params" : {
        "speed" : true
    }
}
```

**Example Response:**
```json
{
    "id": 123,
    "jsonrpc": "2.0",
    "result" : {
        "speed" : {
            "dataType" : "VEHICLEDATA_SPEED",
            "resultCode" : "SUCCESS"
        }
    }
}
```

### VehicleInfo.OnVehicleData

**Description:**

A notification from the HMI indicating that one or more of the subscribed vehicle data items were updated.

**Example Notification:**
```json
{
    "jsonrpc": "2.0",
    "method": "VehicleInfo.OnVehicleData",
    "result" : {
        "speed" : 100
    }
}
```

More information regarding this component is available in the VehicleInfo section of the [HMI Documentation](https://smartdevicelink.com/en/guides/hmi/overview/)

## Available Vehicle Data Items

Below is a list of all of the vehicle data items which are available via SDL as of [Release 6.1.0](https://github.com/smartdevicelink/sdl_core/releases/tag/6.1.0) of SDL Core. New vehicle data items are proposed regularly via the [SDL Evolution process](https://github.com/smartdevicelink/sdl_evolution).

|Name|Result Type|Description|
|:---|:----------|:----------|
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

Starting with [SDL Core version 6.0.0](https://github.com/smartdevicelink/sdl_core/releases/tag/6.0.0), custom vehicle data items can be defined via the policy table. See [SDL-0173](https://github.com/smartdevicelink/sdl_evolution/blob/master/proposals/0173-Read-Generic-Network-Signal-data.md) for the full proposal details. These items are structured in a similar manner to the [MOBILE API](https://github.com/smartdevicelink/sdl_core/blob/master/src/components/interfaces/MOBILE_API.xml) and contained in the `vehicle_data` section of the policy table.

In addition to custom items, this feature can be used to expose other vehicle data items that were introduced to the project in later versions. This can be useful when the software version on the head unit cannot be updated easily. If a vehicle data item is added into the project, the definition of this item will be included in the policy table by default. Any vehicle data items which are defined in Core's local Mobile API will be ignored from the policy table, but newer items will be interpreted as custom items. This allows apps to use these data items normally if they are exposed by the head unit, even when they were not initially supported.

### Example Entry

```json
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
                    "deprecated": true,
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

### Custom Data Fields
* _name_ : Is the vehicle data item in question. e.g. gps, speed etc. SDL core would use this as the vehicle data param for requests from the app and to validate policies permissions.
* _type_ : Is the return data type of the vehicle data item. It can either be a generic SDL data type (Integer, String, Float, Boolean, Struct) or an enumeration defined in Mobile API XML. For a vehicle data item that has sub-params, this would be Struct.
* _key_ : Is a reference for the OEM Network Mapping table which defines signal attributes for this vehicle data items. OEMs may use this table to differentiate between various vehicle and SW configurations. SDL core will pass along this reference to HMI, and then HMI would be responsible to resolve this reference using the Vehicle Data Mapping table (see [Vehicle Data Mapping File](#vehicle-data-mapping-file)).
* _array_ : A boolean value used to specify if the vehicle data item/param response is an array, rather than a single value of the given _type_.
* _mandatory_ : A boolean value used to specify if the vehicle data param is mandatory to be included in response for the overall vehicle data item.
* _params_ : A recursive list of sub-params for a vehicle data item, see example above (`customStruct`) for structure definition.
* _since_, _until_ : String values related to API versioning which are optional per vehicle data item. 
* _removed_, _deprecated_ : Boolean values related to API versioning which are optional per vehicle data item. 
* _minvalue_, _maxvalue_ : Integer/Float values which are used for controlling the bounds of number values (Integer, Float).
* _minsize_, _maxsize_ : Integer values which are used for controlling the bounds of array values (where _array_ is true).
* _minlength_, _maxlength_ : Integer values which are used for controlling the bounds of String values.

!!! NOTE
* _name_ is required for top level vehicle data items while _type_, _key_ & _mandatory_ are required fields for vehicle data & sub-params. However _array_ can be omitted, in which case _array_ defaults to *false*.
* _Custom/OEM Specific_ vehicle data parameters that are not a part of the rpc spec should not have any version related tags included (_since_, _until_, _removed_, _deprecated_). These vehicle data parameters would not be able to have the same versioning system as the rpc spec, since any version number supplied would not be the version associated with any known public rpc spec.
!!!

### Custom Vehicle Data Requests

Custom vehicle data requests have a separate structure to normal vehicle data requests. While normal vehicle data items are requested using the key structure of `"<item.name>: true"`, custom items are constructed using the `item.key` field and can have a nested structure (when requesting `Struct` items). For example, when requesting all of the vehicle data items which are defined above, the HMI would receive the following message:

```json
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

Since these keys may not be immediately known by the HMI, a vehicle data mapping file can be used to connect these keys to actual readable values from the vehicle. The HMI primarily uses this file to convert CAN data values into an SDL-compatible format. The location where this file is hosted can be specified in the policy table in the `module_config.endpoints.custom_vehicle_data_mapping` field (see [Policy Endpoints](https://smartdevicelink.com/en/guides/sdl-overview-guides/policies/policy-fields/#endpoints)). The format of this file is OEM-defined.

#### Example Format
```json
{
    "version":"0.0.1",
    "date":"01-01-2020",
    "vehicleDataTable": [
        {
            "CGEA1.3c":{
                "defaultPowertrain": {
                    "vehicleData": [
                    ]
                },
                "PHEV":{
                    "vehicleData":[
                        {
                            "key":"OEM_REF_FUELLEVEL",
                            "type":"Integer",
                            "minFrequency":200,
                            "maxLatency":10,
                            "messageName":"Cluster_Info3",
                            "messageID":"0x434",
                            "signalName":"FuelLvl_Pc_Dsply",
                            "transportChannel":"HS3",
                            "resolution":0.109,
                            "offset":-5.2174
                        }
                    ]
                }
            }
        }
    ]
}
```

!!! NOTE
In order for the HMI to determine when this file needs to be updated, this file can be assigned a version via the `module_config.endpoint_properties.custom_vehicle_data_mapping.version` field. The HMI can retrieve this field using the [SDL.GetPolicyConfigurationData](https://smartdevicelink.com/en/guides/hmi/sdl/getpolicyconfigurationdata/) RPC.
!!!

### Reading Raw CAN Data

In addition to complex vehicle data items, the vehicle data mapping file can also be used to make some CAN values directly readable via a String value:

#### Policy Definition
```json
{
    "name":"messageName",
    "type":"String",
    "key":"OEM_REF_MSG",
    "array":true,
    "mandatory":false,
    "since":"X.x",
    "maxsize":100,
    "params":[]
}
```

#### HMI Response
```json
{
    "messageName": "AB 04 D1 9E 84 5C B8 22"
}
```
