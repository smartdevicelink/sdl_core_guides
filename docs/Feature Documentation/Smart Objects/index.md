
# Smart Objects

Smart Objects are a recursive custom dynamic data structure within SDL Core which can be used to easily store and manipulate complex data. Developers can use Smart Objects to create containers for most primitive types, as well as arrays and maps.

## Usage

The current implementation of Smart Objects contains definitions that allow it to store the following data types: bool, int, long, double, char, string (both as char* and std::string), array, and map.

The `ns_smart_device_link::ns_smart_objects::CSmartObject` class also defines a set of methods that can be used to represent the stored object value as a desired type.

Example Usage:

```
ns_smart_device_link::ns_smart_objects::SmartObject obj;

obj[0] = 1;

obj[1] = true;

obj[2] = 'a';

obj[3] = 3.14;

int i = obj[0].asInt();

bool b = obj[1].asBool();

char c = obj[2].asChar();

double d = obj[3].asDouble();
```

## Validation

Smart Objects also include a validation/normalization mechanism called a Schema, which is similarly structured to the Mobile/HMI API XML schemas. This object allows the client to validate any existing Smart Object data structure. The process of validation includes both type and value validation for the Smart Object.

To validate a Smart Object, a Schema needs to be applied to it. To "apply" a Schema means that the Schema will modify the object to "normalize" its data. Applying the Schema can be done by using the `ns_smart_device_link::ns_smart_objects::CSmartSchema::applySchema` method. Internally, the apply method for a Schema triggers the apply method of every Schema Item within the object, and currently only modifies enum Schema Items. When this method is called on a enum Schema Item, it will try to convert the string representation of the object to one the item's predefined enum values.

The validation of a specific Smart Object can be triggered by using the `ns_smart_device_link::ns_smart_objects::CSmartSchema::validate` method. Internally, the validate method triggers the respective validate method for each Schema Item in the object in order to perform validation.

To "unapply" modifications done by the apply step, the Schema's `ns_smart_device_link::ns_smart_objects::CSmartSchema::unapplySchema` method can be used. This reverts all enum values back to their string representations.

### Schema Structure

Every Schema is constructed using objects called Schema Items. Each Schema Item defines the type of a specific data structure as well as any restrictions for that structure's values.

In order to create a new Schema (a new object of class `ns_smart_device_link::ns_smart_objects::CSmartSchema`), you must first must define all of the required Schema Items for this object. These Schema Items can have a recursive tree structure, and each node and leaf of that tree defines structural rules for some part of the Smart Object data structure.

Schema Items are represented as class hierarchy. The base class for all Schema Items is the `ns_smart_device_link::ns_smart_objects::ISchemaItem` class. This base class defines a generic validation interface for all Schema Items.

- To define special elements which always fail or suceed the validation step, there are two special Schema Items: `ns_smart_device_link::ns_smart_objects::CAlwaysTrueSchemaItem` or `ns_smart_device_link::ns_smart_objects::CAlwaysFalseSchemaItem`.

- `ns_smart_device_link::ns_smart_objects::CBoolSchemaItem` is used for boolean values and has no parameters, meaning that it only verifies that the Smart Object contains an actual boolean value.

- `ns_smart_device_link::ns_smart_objects::TNumberSchemaItem` is a template Schema Item that can be used for both integer and floating point values. In addition to simple type verification, it is possible to set an optional min and max value range for this item.

- `ns_smart_device_link::ns_smart_objects::TEnumSchemaItem` is used to verify any custom client-defined enum. It is constructed using a list of these custom enum values.

- `ns_smart_device_link::ns_smart_objects::CStringSchemaItem` is used to verify a string value. In addition to simple type verification, it is possible to set an optional min and max string length for this item.

- `ns_smart_device_link::ns_smart_objects::CArraySchemaItem` provides validation for an array containing values with another Schema Item. It can be used to verify an array with optional size bounds.

- `ns_smart_device_link::ns_smart_objects::CObjectSchemaItem` is used to verify a map structure. Each Schema Item of this type includes a list of child Schema Items with associated keys. All other Schema Item types make up the leaf nodes of the validation tree for this Schema Item.

After the creation of all required Schema Items, it is then possible to create a Schema.
A Schema can be initialized not only by raw root Schema Item, but also by special abstraction called a Member (defined by the `ns_smart_device_link::ns_smart_objects::SMember` class). So every root item (`ns_smart_device_link::ns_smart_objects::CObjectSchemaItem`) firstly should be wrapped as Member. This wrapping process is also used to set the "mandatory" property for each Member. After each Member has been constructed, the root Schema Item is then used to construct the final Schema.

Currently all Schemas are generated by the InterfaceGenerator tool. The Schema for an SDL mobile message has following structure:

```
message
 |
 -- params
 |       |
 |       -- function_id
 |       |
 |       -- message_type
 |       |
 |       -- correlation_id
 |       |
 |       -- protocol_version
 |       |
 |       -- protocol_type
 |
 -- msg_params
         |
         -- (function-specific leaf item)
        ...
```

### Schema Construction Example

```
namespace messageType {
/**
 * @brief Enumeration messageType.
 *
 * 
 *             Enumeration linking message types with function types in WiPro protocol.
 *             Assumes enumeration starts at value 0.
 *         
 */
enum eType {
  /**
   * @brief INVALID_ENUM.
   */
  INVALID_ENUM = -1,

  /**
   * @brief request.
   */
  request = 0,

  /**
   * @brief response.
   */
  response = 1,

  /**
   * @brief notification.
   */
  notification = 2
};
} // messageType
...
namespace FunctionID {
/**
 * @brief Enumeration FunctionID.
 *
 * Enumeration linking function names with function IDs in SmartDeviceLink protocol. Assumes enumeration starts at value 0.
 */
enum eType {
  /**
   * @brief INVALID_ENUM.
   */
  INVALID_ENUM = -1,

  /**
   * @brief RESERVED.
   */
  RESERVED = 0,

  /**
   * @brief RegisterAppInterfaceID.
   */
  RegisterAppInterfaceID = 1,
  ...
};
} // FunctionID
...
// Struct member success.
//
//  true if successful; false, if failed 
std::shared_ptr<ISchemaItem> success_SchemaItem = CBoolSchemaItem::create(TSchemaItemParameter<bool>());

// Struct member resultCode.
//
// See Result
std::shared_ptr<ISchemaItem> resultCode_SchemaItem = TEnumSchemaItem<Result::eType>::create(resultCode_allowed_enum_subset_values, TSchemaItemParameter<Result::eType>());

// Struct member info.
//
// Provides additional human readable info regarding the result.
std::shared_ptr<ISchemaItem> info_SchemaItem = CStringSchemaItem::create(TSchemaItemParameter<size_t>(1), TSchemaItemParameter<size_t>(1000), TSchemaItemParameter<std::string>());

Members schema_members;
schema_members["success"] = SMember(success_SchemaItem, true, "1.0.0", "", false, false);
schema_members["resultCode"] = SMember(resultCode_SchemaItem, true, "1.0.0", "", false, false);
schema_members["info"] = SMember(info_SchemaItem, false, "1.0.0", "", false, false);

Members params_members;
params_members[ns_smart_device_link::ns_json_handler::strings::S_FUNCTION_ID] = SMember(TEnumSchemaItem<FunctionID::eType>::create(function_id_items), true);
params_members[ns_smart_device_link::ns_json_handler::strings::S_MESSAGE_TYPE] = SMember(TEnumSchemaItem<messageType::eType>::create(message_type_items), true);
params_members[ns_smart_device_link::ns_json_handler::strings::S_PROTOCOL_VERSION] = SMember(TNumberSchemaItem<int>::create(), true);
params_members[ns_smart_device_link::ns_json_handler::strings::S_PROTOCOL_TYPE] = SMember(TNumberSchemaItem<int>::create(), true);
params_members[ns_smart_device_link::ns_json_handler::strings::S_CORRELATION_ID] = SMember(TNumberSchemaItem<int>::create(), true);

Members root_members_map;
root_members_map[ns_smart_device_link::ns_json_handler::strings::S_MSG_PARAMS] = SMember(CObjectSchemaItem::create(schema_members), true);
root_members_map[ns_smart_device_link::ns_json_handler::strings::S_PARAMS] = SMember(CObjectSchemaItem::create(params_members), true);
return CSmartSchema(CObjectSchemaItem::create(root_members_map));
```
