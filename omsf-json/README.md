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
    "usedProcedureTitle": "Meteorological surface observations",
    "usedProcedureReference": "http://xml.fmi.fi/process/met-surface-observations",
    "observedPropertyTitle": "Temperature",
    "observedPropertyReference": "http://vocab.nerc.ac.uk/collection/P07/current/CFSN0023/",
    "featureOfInterestTitle": "Helsinki Kumpula",
    "featureOfInterestReference": "http://sws.geonames.org/843429/about.rdf",
    "resultValue": 10.1,
    "resultUnitOfMeasure": "Cel"
  }
}
```

## Design considerations

The [GeoJSON Feature model](http://geojson.org/schema/Feature.json) is a very simple one to start with: a GeoJSON Feature is JSON object with property 'type' with a fixed value 'Feature', a 'geometry' property with one of the 
GeoJSON geometry types (Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon or GeometryCollection), and property called 'properties', containing any JSON object 
(see [RFC 7946](https://tools.ietf.org/html/rfc7946)).

The specification explicitly forbids 'extending' the GeoJSON types, including the Feature, but at the same time it is allowed to 
use additional properties (called foreign members) in
GeoJSON documents. The way this requirement is interpreted in this project, is that you cannot require additional foreing members 
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

The geometry property of the OMSF GeoJSON Feature is used for the geometry of the feature of interest of the observation.
Other OMSF properties expected to be used in each of the observation type are given in the tables below. 

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
madeBySensorTitle | optional | string | |
madeBySensorReference | optional | string (URI) | external reference |
usedProcedureTitle | optional | string | |
usedProcedureReference | optional | string (URI) | external reference |
observedPropertyTitle | optional | string | |
observedPropertyReference | required | string (URI) | external reference |
featureOfInterestTitle | optional | string | |
featureOfInterestReference | optional | string (URI) | external reference |

### GenericObservation

In addition to the common properties listed above, GenericObservation has the following properties:

Property name | Condition | Type | Value
--------------|--------------|-----------|------
observationType | required | string | fixed 'GenericObservation'
resultReference | required | string (URI) | extenal reference


### MeasureObservation

In addition to the common properties listed above, MeasureObservation has the following properties:

Property name | Condition | Type | Value
--------------|--------------|-----------|------
observationType | required | string | fixed 'MeasureObservation'
resultValue | required | number | |
resultUnitOfMeasure | optional | string | UCUM code or an URI


### CategoryObservation

In addition to the common properties listed above, CategoryObservation has the following properties:

Property name |  Condition | Type | Value
--------------|--------------|-----------|------
observationType | required | string | fixed 'CategoryObservation'
resultValue | required | string | URI reference recommended indicating both the codelist and the value |

### TruthObservation

In addition to the common properties listed above, TruthObservation has the following properties:

Property name | Condition | Type | Value
--------------|--------------|-----------|------
observationType | required | string | fixed 'TruthObservation'
resultValue | required | boolean | |


### CountObservation

In addition to the common properties listed above, CountObservation has the following properties:

Property name | Condition | Type | Value
--------------|--------------|-----------|------
observationType | required | string | fixed 'CountObservation'
resultValue | required | integer | |



### MeasureTimeseriesObservation

In addition to the common properties listed above, MeasureTimeseriesObservation has the following properties:

Property name | Condition | Type | Value
--------------|--------------|-----------|------
observationType | required | string | fixed 'MeasureTimeseriesObservation'
timeSteps | required | array of string (date-time) | |
resultUnitOfMeasure | optional | string | UCUM code or an URI
resultValues | required | array of number | |
