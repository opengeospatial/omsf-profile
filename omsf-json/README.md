# OGC Observations and Measurements - GeoJSON Encoding

This GML application schema defines a JSON encoding of the the [OMSF implementation model](https://github.com/opengeospatial/omsf-profile) compliant with the [GeoJSON Feature model](http://geojson.org/schema/Feature.json) defined in [RFC 7946](https://tools.ietf.org/html/rfc7946).

The GeoJSON specification explicitly forbids 'extending' the GeoJSON types, including the Feature, but at the same time it is allowed to
use additional properties (called foreign members) in
GeoJSON documents. The way this requirement is interpreted in this project, is that you cannot require additional foreign members
to be included in a GeoJSON Feature, or change any aspect of the Feature properties defined in the specification.
So to be compliant with the GeoJSON specification and thus improve interoperability with existing client software, the decision
has been made to use the GeoJSON Feature type as-is, and to leverage the
'properties' JSON object for encoding the O&M properties. Requiring the use of particular JSON content inside the 'properties'
is not interpreted as extending the Feature type, as this 'properties' is allowed to contain any JSON object.
In addition to the OMSF properties, any additional properties are also allowed inside the 'properties' of OMSF GeoJSON Features.
Also, attention has been paid to ensure that the OMSF properties are simple enough to be interpreted correctly by
typical existing client software.

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

MeasureTimeseriesObservation example:
```json
{
  "type": "Feature",
  "id": "f-1",
  "geometry": {
    "type": "Point",
    "coordinates": [ 24.96131, 60.20307 ]
  },
  "properties": {
    "observationType": "MeasureTimeseriesObservation",
    "phenomenonTimeStart": "2017-08-17T12:00:00Z",
    "phenomenonTimeEnd": "2017-08-17T18:00:00Z",
    "resultTime": "2017-08-17T12:11:20Z",
    "usedProcedureName": "Meteorological surface observations",
    "usedProcedureReference": "http://xml.fmi.fi/process/met-surface-observations",
    "observedPropertyName": "air_temperature",
    "observedPropertyReference": "http://vocab.nerc.ac.uk/collection/P07/current/CFSN0023/",
    "samplingFeatureName": "Helsinki Kumpula weather observation station",
    "ultimateFeatureOfInterestName": "Helsinki Kumpula",
    "ultimateFeatureOfInterestReference": "http://sws.geonames.org/843429/about.rdf",
    "timeStep": [
        "2017-08-17T12:00:00Z",
        "2017-08-17T13:00:00Z",
        "2017-08-17T14:00:00Z",
        "2017-08-17T15:00:00Z",
        "2017-08-17T16:00:00Z",
        "2017-08-17T17:00:00Z",
        "2017-08-17T18:00:00Z"
    ],
    "unitOfMeasureName": "Degree Celsius",
    "unitOfMeasureReference": "http://www.opengis.net/def/uom/UCUM/degC",
    "result": [12.5, 12.0, 11.0, 13.2, 13.5, 14.1, 14.1]
  }
}
```

The work versions of validation rules for each observation type have been defined using JSON Schema in [feature.json](./feature.json).

## JSON encoding details

The ```geometry``` property of the OMSF GeoJSON Feature is the geometry of the sampling feature of the observation, or if
no sampling feature was used, a representative geometry of the ultimate feature of interest.
Other OMSF properties expected to be used in each of the observation type are given in the tables below.

### Common properties

These properties are defined for all the Observation types:

OMSF Property name | JSON property name | Condition | Type | Value
--------------|--------------|-----------|------|-------
metadata | metadataReference | optional | string (URI) | external reference
resultTime | resultTime | required | string (date-time) | |
phenomenonTime | phenomenonTime | required if the phenomenon time is a time instant | string (date-time) | |
phenomenonTime | phenomenonTimeStart | required if the phenomenon time is a time range | string (date-time) | |
phenomenonTime | phenomenonTimeEnd | required if the phenomenon time is a time range | string (date-time) | |
validTime | validTimeStart | optional | string (date-time) | |
validTime | validTimeEnd | optional | string (date-time) | |
madeBySensor | madeBySensorName | optional | string | |
madeBySensor | madeBySensorReference | optional | string (URI) | external reference |
usedProcedure | usedProcedureName | optional | string | |
usedProcedure | usedProcedureReference | optional | string (URI) | external reference |
observedProperty | observedPropertyName | optional | string | |
observedProperty | observedPropertyReference | required | string (URI) | external reference |
samplingFeatureName | samplingFeatureName | optional | string | |
samplingFeature | samplingFeatureReference | optional | string (URI) | external reference |
ultimateFeatureOfInterestName | ultimateFeatureOfInterestName | optional | string | |
ultimateFeatureOfInterest | ultimateFeatureOfInterestReference | optional | string (URI) | external reference |

### GenericObservation

In addition to the common properties listed above, GenericObservation has the following properties:

OMSF Property name | JSON property name | Condition | Type | Value
--------------|--------------|-----------|------|-------
n/a (feature type) | observationType | required | string | fixed 'GenericObservation'
result | result | required | string (URI) | external reference

### MeasureObservation

In addition to the common properties listed above, MeasureObservation has the following properties:

OMSF Property name | JSON property name | Condition | Type | Value
--------------|--------------|-----------|------|-------
n/a (feature type) | observationType | required | string | fixed 'MeasureObservation'
result | result | required | number | |
result | unitOfMeasureName | optional | string | Name of the UoM
result | unitOfMeasureReference | optional | string (uri) | an URI pointing to the remote definition of the UoM


### CategoryObservation

In addition to the common properties listed above, CategoryObservation has the following properties:

OMSF Property name | JSON property name |  Condition | Type | Value
--------------|--------------|-----------|------|-------
n/a (feature type) | observationType | required | string | fixed 'CategoryObservation'
result | result | required | string | URI reference recommended indicating both the codelist and the value |

### TruthObservation

In addition to the common properties listed above, TruthObservation has the following properties:

OMSF Property name | JSON property name | Condition | Type | Value
--------------|--------------|-----------|------|-------
n/a (feature type) | observationType | required | string | fixed 'TruthObservation'
result | result | required | boolean | |


### CountObservation

In addition to the common properties listed above, CountObservation has the following properties:

OMSF Property name | JSON property name | Condition | Type | Value
--------------|--------------|-----------|------|-------
n/a (feature type) | observationType | required | string | fixed 'CountObservation'
result | result | required | integer | |


### MeasureTimeseriesObservation

In addition to the common properties listed above, MeasureTimeseriesObservation has the following properties:

OMSF Property name | JSON property name | Condition | Type | Value
--------------|--------------|-----------|------|-------
n/a (feature type) | observationType | required | string | fixed 'MeasureTimeseriesObservation'
timeStep | timeStep | required | array of string (date-time) | |
result | unitOfMeasureName | optional | string | Name of the UoM
result | unitOfMeasureReference | optional | string (uri) | an URI pointing to the remote definition of the UoM
result | result | required | array of number | |
