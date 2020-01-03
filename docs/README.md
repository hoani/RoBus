# Purpose

The RoBus protocol is designed to:
* Provide intuitive protocol configuration
* Speed up development/debugging on the comms layer

# Protocol

Packets are human interprettable/enterable: `S10C0:00:CF000000\n`

The protocol is configured using a json file.

## Configuration

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

Each packet begins with a category character which determines how the receiving application should interpret the packet.

The RoBus protocol defines six categories by default, however, an application should define more categories as required.

See the packet category section for predefined category roles.

### Separator, Compound, End characters

These configuration items define the utf-8 characters used in encoded packets. They may be changed to suit a users application if they meet the following rules:
* Must be copiable (eg, a delete character is not allowed)
* Must not be hexadecimal [0-9a-f]
* Must not be a category character

### Data

The `_data` field is the meat of the application specific protocol.

## Packets

