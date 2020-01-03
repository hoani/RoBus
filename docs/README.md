
Packets are human interprettable/enterable: `S10C0:00:CF000000\n`

The protocol is configured using a json file.

# Purpose

The RoBus protocol is designed to:
* Provide intuitive protocol configuration
* Speed up development/debugging on the comms layer

# Configuration

The json file has the following root schema:
* version: the current semver of the RoBus protocol
* category: a map of start characters used in packets
* separator: protocol separator character
* compound: protocol compound character
* end: protocol end character
* _data: the root of the applications data map

### Version

Version is a semvar version with major, minor and patch numbers. This version relates to the RoBus protocol itself. Its purpose is to determine compatability.

### Category

Category characters determine how the receiving application should interpret a packet.

The RoBus protocol defines six categories by default. However, an application may define more categories when needed.

See the packet category section for predefined category roles.

### Separator, Compound, End characters

These configuration items define the utf-8 characters used in encoded packets. They may be changed to suit a users application if they meet the following rules:
* Must be copyable (eg, a delete character is not allowed)
* Must not be hexadecimal `[0-9a-f]`
* Must not be a category character

### Data

The `_data` field is a heirachical structure of user defined data.
 
```json
"_data" : [
  { "sensor" : { "_addr": "8000", "_data": [
    { "imu": { "_addr": "00A0", "_data": [
      { "accel": { "_data": [
        { "x": { "_type": "float" } }, 
        { "y": { "_type": "float" } }, 
        { "z": { "_type": "float" } }
      ]}},
      { "gyros": { "_data": [
        { "x": { "_type": "float" } },
        { "y": { "_type": "float" } },
        { "z": { "_type": "float" } }
      ]}},
    ]}},
    { "temperature": { "_addr": "00C0", "_type": "float" } },
    { "barometer": { "_type": "float" } }
  ]}},
  { "timestamp_ms": { "_addr": "9000", "_type": "u64" }}
] 
```

Results in the following address mapping:

| path                  | 16-bit address  | type                    |
| ---:                  | :---            | ---                     |
| *sensor*              | *0x8000*        | -                       |
| *sensor/imu*          | *0x80A0*        | -                       | 
| *sensor/imu/accel*    | *0x80A0*        | -                       |
| *sensor/imu/accel/x*  | *0x80A0*        | float                   |
| *sensor/imu/accel/y*  | *0x80A1*        | float                   |
| *sensor/imu/accel/z*  | *0x80A2*        | float                   |
| *sensor/imu/gyros*    | *0x80A3*        | -                       |
| *sensor/imu/gyros/x*  | *0x80A3*        | float                   |
| *sensor/imu/gyros/y*  | *0x80A4*        | float                   |
| *sensor/imu/gyros/z*  | *0x80A5*        | float                   |
| *sensor/temperature*  | *0x80C0*        | float                   |
| *sensor/barometer*    | *0x80C1*        | float                   |
| *timestamp_ms*        | *0x9000*        | unsigned integer 64-bit |

Addresses:
* Addresses are always four character hexidecimal strings `/[0-9a-f]{4}/i`
* Items inherit the address of the parent object and then adds thier own.
* If an item does not define an address, it increments from the previous assigned address.
* Parent items share thier address with thier first child
* Addresses must always increment (eg/ an address of 8000 following and address of C000 is invalid).
* Addresses never exceed 16-bits.

Items
* An item must have either a `"_data"` array or `"_type"` string
* Items are placed in arrays to *guarantee* order (its a JSON thing)
* Item names must start with a character and can only contain characters, numbers, dashes and underscores `/^[a-z][\w\-_]*$/i`

Valid types:
* *unsigned integers*: "u8", "u16", "u32", "u64"
* *signed integers*: "i8", "i16", "i32", "i64"
* float 32-bit: "float"
* float 64-bit: "double"
* string: "string"
* boolean: "bool"
* enumeration: [ "item1", "item2", ...]

For enumerations, the item names are user defined - these get encoded as integers.



# Encoding

