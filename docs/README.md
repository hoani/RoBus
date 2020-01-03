# Purpose

The RoBus protocol is designed to:
* Provide intuitive protocol configuration
* Speed up development/debugging on the comms layer

# Protocol

Packets look like:
`S10C0:00:CF000000\n`

The main goal was to make raw packet dumps human interprettable and make packets easy to be copied.

The protocol is configured using a json file.

## Configuration

The json file has the following root schema:
* version: the current semver of the RoBus protocol
* category: a map of start characters used in packets
* separator: protocol separator character
* compound: protocol compound character
* end: protocol end character
* _data: the root of the applications data map



## Packets

