# Katana Slice Information Model - Generic Slice Template (GST)

## Roles in network slicing

Multiple roles related to network slicing are specified in 3GPP TS 28.530. In this document the following roles are used:

- Communication Service Customer (CSC): Uses communication services, e.g. end user, tenant, vertical
- Communication Service Provider (CSP): Provides communication services. Designs, builds and operates its communication services. The CSP provided communication service can be built with or without network slice
- Network Operator (NOP): Provides network services. Designs, builds and operates its networks to offer such services
- Network Slice Customer (NSC): The Communication Service Provider (CSP) or Communication Service Customer (CSC) who uses Network Slice as a Service
- Network Slice Provider (NSP): The Communication Service Provider (CSP) or Network Operator (NOP) who provides Network Slice as a Service

![Slicing Roles](uploads/SlicingRoles.png)

## GST and NEST

The Generic Slice Template (GST) is a set of attributes that can characterise a type of network slice/service. GST is generic and is not tied to any specific network deployment. The NEtwork Slice Type (NEST) is a GST filled with values. The attributes and their values are assigned to fulfil a given set of requirements derived from a network slice customer use case. The NEST is an input to the network slice (instance) preparation performed by the Network Slice Manager. One or more NSIs (Network Slice Instance as defined in 3GPP TS 23.501) can be created out of the same NEST, but also existing NSI(s) may be reused

A NEST is sent to Katana Slice Manager with a slice creation request. It is then parsed by the Slice Mapping process, which, in combination with the supported network functions by the underlying infrastructure (see [Supported Network Functions](func)), creates the slice.

![GST and NEST](uploads/GST_NEST.png)

## How to use

### Structure

Katana Slice GST is used for the creation of new slices. It has three sections:

- Slice Descriptor (slice_descriptor)
- Vertical Services Descriptor (service_descriptor)
- Test Descriptor (test_descriptor)

> Slice Descriptor is mandatory object while the other two are optional.
These descriptors define parameters that will be used for the creation of the new slice.

### On-board descriptors

Descriptors for each sections can be on-boarded to Katana Slice Manager before the slice creation phase. Katana returns lists with all the onboarded descriptors. The on-boarded descriptors can be referenced in a GST during the creation of a slice, instead of defining a new descriptor on the GST.

### Overwrite parameter values of a referenced descriptor

You can reference a previously on-boarded descriptor on a GST, and also define some parameter values for that sector. This will use the referenced descriptor as a base, replacing the parameters with the ones defined on the GST.

## Sources

The GST is based on the [GSMA GST v1.0](https://www.gsma.com/newsroom/wp-content/uploads//NG.116-v1.0.pdf)
and [GSMA GST v2.0](https://www.gsma.com/newsroom/wp-content/uploads//NG.116-v2.0.pdf)

Katana Slice Information Model follows the JSON Schema model, on which OpenAPIs are based:

- [Source](http://json-schema.org/)
- [Understanding JSON Schema](http://json-schema.org/understanding-json-schema/UnderstandingJSONSchema.pdf)

## Useful Tools

- [JSON Validator](https://jsonlint.com/)
- [YAML Validator](http://www.yamllint.com/)
- [JSON Schema Validator](https://json-schema-validator.herokuapp.com/)
- [JSON Schema Generator](https://jsonschema.net/)

## GST JSON Model

```JSON
{
  "$schema": "http://json-schema.org/draft-07/schema#",

  "definitions": {
    "base_slice_descriptor":{
      "type": "object",
      "description": "This is the schema for the core part of the new slice",
      "properties": {
        "base_slice_des_id": {
          "type": "string",
          "description": "Id of the slice descriptor which will be used as base for the gst"
        },
        "base_slice_des_ref": {
          "type": "string",
          "description": "Optional - Reference to an added slice descriptor which will be used as base for the gst"
        },
        "coverage": {
          "type": "array",
          "description": "A list with all the locations that are part of the slice",
          "items":{
            "type": "string",
            "description": " The location name for each site in the slice"
          }
        },
        "delay_tolerance": {
          "type": "boolean",
          "description": "Supported or not supported"
        },
        "deterministic_communication":{
          "type": "object",
          "description": "This attribute defines if the network slice supports deterministic communication for periodic user traffic. Periodic traffic refers to the type of traffic with periodic transmissions.",
          "properties":{
            "availability": {
              "type": "boolean",
              "description": "This parameter describes if the network slice supports deterministic communication."
            },
            "periodicity": {
              "type": "array",
              "description": "This parameter provides a list of periodicities supported by the network slice.",
              "items": {
                "type": "number",
                "description": "Seconds"
              }
            }
          }
        },
        "network_DL_throughput": {
          "type": "object",
          "description": "The achievable data rate in downlink for the whole network slice (and not per user).",
          "properties": {
            "guaranteed": {
              "type": "number",
              "description": "kbps"
            },
            "maximum": {
              "type": "number",
              "description": "kbps"
            }
          }
        },
        "ue_DL_throughput": {
          "type": "object",
          "description": "This attribute describes the guaranteed data rate supported by the network slice per UE in downlink",
          "properties": {
            "guaranteed": {
              "type": "number",
              "description": "kbps"
            },
            "maximum": {
              "type": "number",
              "description": "kbps"
            }
          }
        },
        "network_UL_throughput": {
          "type": "object",
          "description": "The achievable data rate in uplink for the whole network slice (and not per user).",
          "properties": {
            "guaranteed": {
              "type": "number",
              "description": "kbps"
            },
            "maximum": {
              "type": "number",
              "description": "kbps"
            }
          }
        },
        "ue_UL_throughput": {
          "type": "object",
          "description": "This attribute describes the guaranteed data rate supported by the network slice per UE in uplink",
          "properties": {
            "guaranteed": {
              "type": "number",
              "description": "kbps"
            },
            "maximum": {
              "type": "number",
              "description": "kbps"
            }
          }
        },
        "group_communication_support": {
          "type": "number",
          "enum": [0, 1, 2, 3],
          "description": "0: not available 1: Single Cell Point to Multipoint (SCPTM) 2: Broadcast/Multicast 3: Broadcast/Multicast + SC-PTM"
        },
        "isolation_level": {
          "type": "object",
          "description": " A network slice instance may be fully or partly, logically and/or physically, isolated from another network slice instance",
          "properties": {
            "isolation": {
              "type": "number",
              "enum": [0, 1, 2, 3],
              "description": "0: No Isolation 1: Physical Isolation 2: Logical Isolation 3: Both Isolation"
            }
          }
        },
        "mtu": {
          "type": "number",
          "description": "Bytes"
        },
        "mission_critical_support": {
          "type": "object",
          "description": "Mission-critical (MC) leads to a priority of the network slice relative to others, for C-plane and U-plane decisions.",
          "properties": {
            "availability": {
              "type": "boolean"
            },
            "mc_service": {
              "type": "array",
              "description": "This attribute specifies whether or not the network slice supports MC push-to-talk, MC data, MC video, Isolated E-UTRAN Operation for Public Safety or MC interworking.",
              "items": {
                "type": "number",
                "enum": [1, 2, 3, 4, 5],
                "description": "1: MCPTT 2: MCData 3: MCVideo 4: IOPS 5: MC interworking"
              }
            }
          }
        },
        "mmtel_support": {
          "type": "boolean",
          "description": "This attribute describes whether the network slice supports IP Multimedia Subsystem (IMS) and Multimedia Telephony Service MMTel."
        },
        "nb_iot": {
          "type": "boolean",
          "description": "This parameter describes whether NB-IoT is supported in the network slice."
        },
        "number_of_connections": {
          "type": "number",
          "description": "This attribute describes the maximum number of concurrent sessions supported by the network slice."
        },
        "number_of_terminals": {
          "type": "number",
          "description": "This attribute describes the maximum number of concurrent terminals supported by the network slice."
        },
        "positional_support": {
          "type": "object",
          "description": "This attribute describes if the network slice provides geo-localization methods or supporting methods.",
          "properties": {
            "availability": {
              "type": "array",
              "description": "Describes if this attribute is provided by the network slice and contains a list of positioning methods provided by the slice.",
              "items": {
                "type": "number",
                "enum": [1, 2, 3, 4, 5, 6, 7],
                "description": "1: CID 2: E-CID (LTE and NR) 3: OTDOA (LTE and NR) 4: RF fingerprinting 5: AECID 6: Hybrid positioning 7: NET-RTK"
              }
            },
            "frequency": {
              "type": "number",
              "description": "Seconds"
            },
            "accuracy": {
              "type": "number",
              "description": "Meters"
            }
          }
        },
        "radio_spectrum": {
          "type": "array",
          "description": "Defines the radio spectrum supported by the network slice.",
          "items": {
            "type": "string",
            "description": "This attribute simply tells which frequencies can be used to access the network slice. Example: n1, n77, n38"
          }
        },
        "simultaneous_nsi": {
          "type": "number",
          "enum": [0, 1, 2, 3, 4, 5],
          "description": "0: Can be used with any network slice 1: Can be used with network slices with same SST value 2: Can be used with any network slice with same SD value 3: Cannot be used with another network slice 4-15: operator defined class"
        },
        "qos": {
          "type": "array",
          "description": "This attribute defines all the QoS relevant parameters supported by the network slice, based on 3GPP defined standard values (5QIs)",
          "items": {
            "type": "object",
            "description": "Refers to 5QI defined in https://portal.3gpp.org/desktopmodules/Specifications/SpecificationDetails.aspx?specificationId=3144",
            "properties": {
              "qi": {
                "type": "number",
                "description": "Based on the table of defined 5QI by 3GPP"
              },
              "resource_type": {
                "type": "number",
                "enum": [0, 1, 2],
                "description": "0: GBR (Mission Critical Video user plane) 1: Delay critical GBR (Intelligent Transport Systems) 2: Non-GBR (Voice, AR)"
              },
              "priority_level": {
                "type": "number",
                "description": "Associated with 5G QoS characteristics indicates a priority in scheduling resources among QoS Flows."
              },
              "packet_delay_budget": {
                "type": "number",
                "description": "The Packet Delay Budget (PDB) defines an upper bound for the time that a packet may be delayed between the UE and the UPF [Seconds]."
              },
              "packet_error_rate": {
                "type": "number",
                "description": "The Packet Error Rate (PER) defines an upper bound for the rate of packets that are not successfully delivered by the corresponding receiver [percentage]."
              },
              "jitter": {
                "type": "number",
                "description": "Jitter is defined as a variation in the delay of received packets [Seconds]."
              },
              "max_packet_loss_rate": {
                "type": "number",
                "description": " the maximum rate for lost packets of the QoS flow that can be tolerated in the uplink (UL) and downlink (DL) direction [percentage."
              }
            }
          }
        },
        "nonIP_traffic": {
          "type": "boolean"
        },
        "device_velocity": {
          "type": "number",
          "enum": [1, 2, 3, 4],
          "description": "1: Stationary: 0 km/h 2: Pedestrian: 0 km/h to 10 km/h 3: Vehicular: 10 km/h to 120 km/h 4: High speed vehicular: 120 km/h to 500 km/h"
        },
        "terminal_density": {
          "type": "number",
          "description": "maximum number of connected and/or accessible devices per unit area (per km2) supported by the network slice [Number/km^2]"
        }
      }
    },
    "service_descriptor":{
      "type": "object",
      "description": "This is the schema for the Service Descriptor part of slice IM",
      "properties": {
        "ns_list": {
          "type": "array",
          "description": "List of the NSD to be instantiated alongside the slice",
          "items": {
            "type": "object",
            "description": "A NS",
            "properties":{
              "nfvo-id":{
                "type": "string",
                "description": "The NFVO that will manage the life cycle of the NS"
              },
              "nsd-id": {
                "type": "string",
                "description": "The NSD id as defined on the NFVO"
              },
              "ns-name": {
                "type": "string",
                "description": "The name of the NS"
              },
              "placement": {
                "type": "number",
                "enum": [0, 1],
                "description": "1: Core, 2: Edge"
              }
            }
          }
        }
      }
    },
    "test_descriptor":{
      "type": "object",
      "description": "This is the schema for the Test Descriptor part of slice IM",
      "properties": {
        "probe_list": {
          "type": "array",
          "description": "A list of probe ids to be included in the slice",
          "items": {
            "type": "string"
          }
        },
        "performance_monitoring": {
          "type": "object",
          "description": "This attribute provides the capability to monitor KQIs and KPIs.",
          "properties": {
            "availability": {
              "type": "array",
              "description": "List of KQIs and KPIs available for monitoring",
              "items": {
                "type": "number",
                "enum": [1, 2, 3],
                "description": "1: Throughput 2: Latency 3: Service Request Success Rate"
              }
            },
            "frequency": {
              "type": "number",
              "description": "Seconds"
            }
          }
        },
        "performance_prediction": {
          "type": "object",
          "description": "This attribute provides the capability to predict KQIs and KPIs.",
          "properties": {
            "availability": {
              "type": "array",
              "description": "List of KQIs and KPIs available for monitoring",
              "items": {
                "type": "number",
                "enum": [1, 2, 3],
                "description": "1: Throughput 2: Latency 3: Service Request Success Rate"
              }
            },
            "frequency": {
              "type": "number",
              "description": "Seconds"
            }
          }
        }
      }
    }
  },


  "type": "object",
  "properties": {
    "base_slice_descriptor": { "$ref": "#/definitions/base_slice_descriptor" },
    "service_descriptor": { "$ref": "#/definitions/service_descriptor" },
    "test_descriptor": { "$ref": "#/definitions/test_descriptor" }
  },
  "required": ["base_slice_descriptor"]
}
```
