Vendor-specific DSDL definitoins
================================

[![Gitter](https://img.shields.io/badge/gitter-join%20chat-green.svg)](https://gitter.im/UAVCAN/general)

This repository contains the DSDL definitions contributed by hardware vendors and other third parties.
Standard DSDL definitions are located in a separate repository at <https://github.com/UAVCAN/dsdl>.

The root of this repository contains directories that are root namespaces for vendor-specific DSDL definitions.

* [**UAVCAN website**](http://uavcan.org)
* [**UAVCAN mailing list**](https://groups.google.com/forum/#!forum/uavcan)

## How to contribute

Fork this repository and send a pull request.
When in doubt, ask for help in the mailing list.

## Relevant excerpts from the specification

### File hierarchy

Each DSDL definition file specifies exactly one data structure that can be used for message broadcasting or a pair of structures that can be used for service invocation data exchange.

The DSDL source file must be named using the *data type name* and *default data type ID* (if needed) as shown below:

    [default data type ID.]<data type name>.uavcan

A defined data structure must be contained in a namespace, which may in turn be *nested* within another namespace. A namespace that is not nested in another namespace is called a *root namespace*. For example, all standard data types are contained in the root namespace `uavcan`, which contains nested namespaces: `equipment`, `protocol`, etc.

The namespace hierarchy is mapped directly to the file-system directory structure, as shown in the example below:

```
+ uavcan                        <-- Root namespace
    + equipment                 <-- Nested namespace
        + ...
    + protocol                  <-- Nested namespace
        + 341.NodeStatus.uavcan <-- Definition of data type "uavcan.protocol.NodeStatus" with default data type ID 341
        + ...
    + Timestamp.uavcan          <-- Definition of data type "uavcan.Timestamp", default data type ID is not assigned
```

Notes:

* It is not necessary to explicitly define a default data type ID for non-standard data types
(i.e., for vendor-specific or application-specific data types).
  * If the default data type ID is not defined by the DSDL definition, it will need to be assigned by the application at
run time.
  * All standard data types have default data type ID values defined.
* Data type names are case sensitive, i.e., names `foo.Bar` and `foo.bar` are considered different. Names that differ only in case should be avoided, because it may cause problems on file systems that are not case-sensitive.
* Data types may contain nested data structures.
  * Some data structures may be designed for such nesting only, in which case they are not required to have a dedicated
    data type ID.
* *Full data type name* is a unique identifier of a data type constructed from the root namespace,
  all nested namespaces (if any), and the data type name itself, joined via the dot symbol (`.`),
   e.g., `uavcan.protocol.file.Read`.
  * The length of the Full data type name must not exceed 80 characters.
  * Refer to the naming rules below for the limitations imposed on the character set.

### Vendor-specific data types

Vendors must define their specific data types in a separate namespace, which should typically be named to match their company name.
Separation of the vendor's definitions into a dedicated namespace ensures that no name conflicts will occur in
systems that utilize vendor-specific data types from different providers.
Note that, according to the naming requirements, the name of a DSDL namespace must start with an alphabetic character;
therefore, a company whose name starts with a digit will have to resort to a mangled name, e.g. by moving the
digits towards the end of the name, or by spelling the digits in English (e.g. 42 - `fortytwo`).

Defining vendor-specific data types within the standard namespace `uavcan` is explicitly prohibited.
The standard namespace will always be used only for standard definitions.

Generally speaking it is desirable for "generic" data types to be included into the standard set.
Vendors should strive to design their data types as generic and as independent of their specific use cases as possible.

### ID distribution

#### Message type ID

The valid range for *message type ID* is from 0 to 65535, inclusive.
The range is segmented as follows:

ID range        | Purpose
----------------|--------------------------------------------------------------
[0, 20000)      | Standard message types
**[20000, 21000)**|**Vendor-specific message types**
[21000, 65536)  | Reserved for future use

#### Service type ID

The valid range for *service type ID* is from 0 to 255, inclusive.
The range is segmented as follows:

ID range        | Purpose
----------------|--------------------------------------------------------------
[0, 100)        | Standard service types
[100, 200)      | Reserved for future use
**[200, 256)**  | **Vendor-specific service types**
