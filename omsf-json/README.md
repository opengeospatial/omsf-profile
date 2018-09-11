# OGC Observations and Measurements - GeoJSON Encoding

In addition to the [GML implementation](../omsf-gml), a GeoJSON encoding of the same data model is provided with data content and structure matching the GML profile as closely as possible.

Example of a omsf:MeasureObservation encoded in GeoJSON:
```json
{
  "type": "Feature",
  "id": "f-1",
  "geometry": {
    "type": "Point",
    "coordinates": [ 24.96131, 60.20307 ]
  },
  "properties": {
    "observationType": "MeasureObservation",
    "phenomenonTime": "2017-08-17T12:00:00Z",
    "resultTime": "2017-08-17T12:01:25Z",
    "usedProcedureName": "Meteorological surface observations",
    "usedProcedureReference": "http://xml.fmi.fi/process/met-surface-observations",
    "observedPropertyName": "air_temperature",
    "observedPropertyReference": "http://vocab.nerc.ac.uk/collection/P07/current/CFSN0023/",
    "samplingFeatureName": "Helsinki Kumpula weather observation station",
    "ultimateFeatureOfInterestName": "Helsinki Kumpula",
    "ultimateFeatureOfInterestReference": "http://sws.geonames.org/843429/about.rdf",
    "result": 12.5,
    "unitOfMeasureName": "Degree Celsius",
    "unitOfMeasureReference": "http://www.opengis.net/def/uom/UCUM/degC"
  }
}
```

## Design considerations

The [GeoJSON Feature model](http://geojson.org/schema/Feature.json) is a very simple one to start with: a GeoJSON Feature is JSON object with property 'type' with a fixed value 'Feature', a 'geometry' property with one of the
GeoJSON geometry types (Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon or GeometryCollection), and property called 'properties', containing any JSON object
(see [RFC 7946](https://tools.ietf.org/html/rfc7946)).

The specification explicitly forbids 'extending' the GeoJSON types, including the Feature, but at the same time it is allowed to
use additional properties (called foreign members) in
GeoJSON documents. The way this requirement is interpreted in this project, is that you cannot require additional foreign members
to be included in a GeoJSON Feature, or change any aspect of the Feature properties defined in the specification.
So to be compliant with the GeoJSON specification and thus improve interoperability with existing client software, the decision
has been made to use the GeoJSON Feature type as-is, and to leverage the
'properties' JSON object for encoding the O&M properties. Requiring the use of particular JSON content inside the 'properties'
is not interpreted as extending the Feature type, as this 'properties' is alloed to contain any JSON object.
In addition to the OMSF properties, any additional properties are also allowed inside the 'properties' of OMSF GeoJSON Features.
Additionally, attention has been paid to ensure that the OMSF properties are simple enough to be interpreted correctly by
typical existing client software.

The GeoJSON encoding defines a property set for the same types of O&M features as the [GML profile](../omsf-gml) does: GenericObservation,
MeasureObservation, CategoryObservation, TruthObservation, CountObservation and MeasureTimeseriesObservation.

The work versions of validation rules for each observation type have been defined using JSON Schema in [feature.json](./feature.json).

## Properties
The geometry property of the OMSF GeoJSON Feature is the geometry of the sampling feature of the observation, or if
no sampling feature was used, a representative geometry of the ultimate feature of interest.
Other OMSF properties expected to be used in each of the observation type are given in the tables below.

### Simplified mapping of O&M Observation properties
The implementation model of the OMSF GeoJSON encoding is a simplified version of the Observation class
as defined in the OGC and ISO 19156 standard. The following table summarises the simplification decisions applied:

O&M attribute/association | O&M type | O&M Multiplicity  |OMSF property | OMSF type |
--------------------------|----------|-------------------|--------------|-----------|
featureOfInterest | association with GFI_Feature | 1 | properties/samplingFeatureName | string |
featureOfInterest | association with GFI_Feature | 1 | properties/samplingFeatureReference | string (uri) |
featureOfInterest | association with GFI_Feature | 1 | properties/ultimateFeatureOfInterestName | string |
featureOfInterest | association with GFI_Feature | 1 | geometry | GeoJSON geometry |
featureOfInterest | association with GFI_Feature | 1 | properties/ultimateFeatureOfInterestReference | string (uri) |
metadata | association with MD_Metadata | 0..1 | properties/metadataReference | string (uri) |
observedProperty | association with GF_PropertyType | 1 | properties/observedPropertyName |
observedProperty | association with GF_PropertyType | 1 | properties/observedPropertyReference | string (uri) |
parameter | NamedValue | 0..n | n/a | n/a |
phenomenonTime | TM_Object | 1  | properties/phenomenonTime | string (date-time) |
phenomenonTime | TM_Object | 1  | properties/phenomenonTimeStart | string (date-time) |
phenomenonTime | TM_Object | 1  | properties/phenomenonTimeEnd | string (date-time) |
procedure | association with OM_Process | 1 | properties/usedProcedureName | string |
procedure | association with OM_Process | 1 | properties/usedProcedureReference | string (uri) |
procedure | association with OM_Process | 1 | properties/madeBySensorName | string |
procedure | association with OM_Process | 1 | properties/madeBySensorReference | string (uri) |
relatedObservation | association with self | 0..n |  n/a | n/a |
result | Any | 1 | properties/result | varied |
result | Any | 1 | properties/timeSteps | array of string (date-time) |
result | Any | 1 | properties/unitOfMeasureName | string |
result | Any | 1 | properties/unitOfMeasureReference | string (uri) |
resultQuality | DQ_Element | 0..n | n/a | n/a |
resultTime | TM_Instant | 1 | properties/resultTime | string (date-time) |
validTime | TM_Period | 0..1 | properties/validTimeStart | string (date-time) |
validTime | TM_Period | 0..1 | properties/validTimeEnd | string (date-time) |

Rationale for the not included properties:

* **parameter**: 0..n multiplicity of name-value pairs would require two array properties (name & value). Trade-off between completeness and simplicity.
* **relatedObservation**: not a problem to include technically (as a uri string), but rarely used in practice. Would require additional information about the relation.
* **resultQuality**: not clear how to encode this in a generic way using a flat, but machine-readable property value. Rarely used in practice,  trade-off between completeness and simplicity.

### Common

These properties are defined for all the Observation types:

Property name | Condition | Type | Value
--------------|--------------|-----------|------
metadataReference | optional | string (URI) | external reference
resultTime | required | string (date-time) | |
phenomenonTime | required if the phenomenon time is a time instant | string (date-time) | |
phenomenonTimeStart | required if the phenomenon time is a time range | string (date-time) | |
phenomenonTimeEnd | required if the phenomenon time is a time range | string (date-time) | |
validTimeStart | optional | string (date-time) | |
validTimeEnd | optional | string (date-time) | |
madeBySensorName | optional | string | |
madeBySensorReference | optional | string (URI) | external reference |
usedProcedureName | optional | string | |
usedProcedureReference | optional | string (URI) | external reference |
observedPropertyName | optional | string | |
observedPropertyReference | required | string (URI) | external reference |
samplingFeatureName | optional | string | |
samplingFeatureReference | optional | string (URI) | external reference |
ultimateFeatureOfInterestName | optional | string | |
ultimateFeatureOfInterestReference | optional | string (URI) | external reference |

### GenericObservation

In addition to the common properties listed above, GenericObservation has the following properties:

Property name | Condition | Type | Value
--------------|--------------|-----------|------
observationType | required | string | fixed 'GenericObservation'
result | required | string (URI) | extenal reference


### MeasureObservation

In addition to the common properties listed above, MeasureObservation has the following properties:

Property name | Condition | Type | Value
--------------|--------------|-----------|------
observationType | required | string | fixed 'MeasureObservation'
result | required | number | |
unitOfMeasureName | optional | string | Name of the UoM
unitOfMeasureReference | optional | string (uri) | an URI pointing to the remote definition of the UoM


### CategoryObservation

In addition to the common properties listed above, CategoryObservation has the following properties:

Property name |  Condition | Type | Value
--------------|--------------|-----------|------
observationType | required | string | fixed 'CategoryObservation'
result | required | string | URI reference recommended indicating both the codelist and the value |

### TruthObservation

In addition to the common properties listed above, TruthObservation has the following properties:

Property name | Condition | Type | Value
--------------|--------------|-----------|------
observationType | required | string | fixed 'TruthObservation'
result | required | boolean | |


### CountObservation

In addition to the common properties listed above, CountObservation has the following properties:

Property name | Condition | Type | Value
--------------|--------------|-----------|------
observationType | required | string | fixed 'CountObservation'
result | required | integer | |



### MeasureTimeseriesObservation

In addition to the common properties listed above, MeasureTimeseriesObservation has the following properties:

Property name | Condition | Type | Value
--------------|--------------|-----------|------
observationType | required | string | fixed 'MeasureTimeseriesObservation'
timeSteps | required | array of string (date-time) | |
unitOfMeasureName| optional | string | Name of the UoM
unitOfMeasureReference | optional | string (uri) | an URI pointing to the remote definition of the UoM
result | required | array of number | |
