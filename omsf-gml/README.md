# OGC Observations and Measurements - GML Simple Features Profile Encoding

This GML application schema defines GML encoding of the the [OMSF implementation model](../) compliant with GML Simple Features Profile
version 2.0 ([OGC Document 10-100r3](http://portal.opengeospatial.org/files/?artifact_id=42729)).
The TimeseriesObservation type is compliant with the Simple Features Profile level SF-1 due to repeated time and value
elements, all other Observation types are compliant level SF-0.

Example of a omsf:MeasureObservation encoded in GML:

```xml
<omsf:MeasureObservation gml:id="f-1">
  <gml:identifier codeSpace="fmi-fi-weatherobs">kumpula-2017-08-17_12-00_air-temp-1</gml:identifier>
  <omsf:phenomenonTime>2017-08-17T12:00:00Z</omsf:phenomenonTime>
  <omsf:resultTime>2017-08-17T12:01:25Z</omsf:resultTime>
  <omsf:usedProcedureReference
    xlink:href="http://xml.fmi.fi/process/met-surface-observations"
    xlink:title="Meteorological surface observations" />
  <omsf:observedPropertyReference
    xlink:href="http://vocab.nerc.ac.uk/collection/P07/current/CFSN0023/air_temperature"
    xlink:title="air_temperature" />
  <omsf:samplingFeatureName>Helsinki Kumpula weather observation station</omsf:samplingFeatureName>
  <omsf:geometry>
      <gml:Point gml:id="p-1" srsName="http://www.opengis.net/def/crs/EPSG/0/4258" srsDimension="2">
          <gml:pos>60.20307 24.96131</gml:pos>
      </gml:Point>
  </omsf:geometry>
  <omsf:ultimateFeatureOfInterestName>Helsinki Kumpula</omsf:ultimateFeatureOfInterestName>
  <omsf:ultimateFeatureOfInterestReference xlink:href="http://sws.geonames.org/843429/about.rdf"/>
  <omsf:result uom="Cel">12.5</omsf:result>
</omsf:MeasureObservation>
```

The work version of the XML Schema for the OMSF is available at [omsf-profile.xsd](./omsf-profile.xsd)

## GML encoding details

### omsf:AbstractObservationType

This type is an abstract base type for all the OMSF Observation types. The ```phenomenonTime``` mapping is implemented
with XML Schema choice between a single ```xsd:dateTime``` valued property ```phenomenonTime``` (when the value is a time
instant) and a set of two ```xsd:dateTime``` valued properties ```phenomenonTimeStart``` and ```phenomenonTimeEnd``` (when the value is a time
period). Similarly the time period of the ```validTime``` property is split into two properties ```validTimeStart```
and ```validTimeEnd```.

Unless stated otherwise, all the OMSF implementation model property names with external reference value have been post-fixed with a
word ```Reference```. The ```samplingFeature``` and the ```ultimateFeatureOfInterest``` OMSF implementation model properties have been encoded with two
properties each: ```samplingFeatureName``` and ```samplingFeatureReference```
and ```ultimateFeatureOfInterestName``` and ```ultimateFeatureOfInterestReference```. The reason for providing
the separate, optional 'name' properties here is that referenceable external resources may not be available in all
cases, but it may still be useful for the data user to get the display names of the sampling feature and the
ultimate feature of interest. This would not be possible using the gml:ReferenceType by itself,
as the xlink:href attribute is required. Separate properties 'name' are not defined for the properties for which
the actual references are considered essential. In this case the ```xlink:title``` attributes are used for encoding
the display names for the linked resources.

OMSF property name | GML SF property name | Multiplicity | GML type |  Notes
--------------|--------------|-----------|------|
metadata      | metadataReference | 0..1 | gml:ReferenceType | external reference
resultTime    | resultTime | 1 | xsd:dateTime | |
phenomenonTime | phenomenonTime | 0..1 (choice with start-end pair) | xsd:dateTime | required only if the phenomenon time is a time instant |
phenomenonTime | phenomenonTimeStart | 0..1 (choice with single phenomenonTime) | xsd:dateTime | required only if the phenomenon time is a time range  |
phenomenonTime | phenomenonTimeEnd | 0..1 (choice with single phenomenonTime) | xsd:dateTime | required only if the phenomenon time is a time range  |
validTime    | validTimeStart | 0..1 | xsd:dateTime | |
validTime | validTimeEnd | 0..1 | xsd:dateTime | |
madeBySensor | madeBySensorReference | 0..1 | gml:ReferenceType | external reference |
usedProcedure | usedProcedureReference | 0..1 | gml:ReferenceType | external reference |
observedProperty | observedPropertyReference | 1 | gml:ReferenceType | external reference |
samplingFeatureName | samplingFeatureName | 0..1 | xsd:string | |
geometry | geometry | 1 | gml:GeometryPropertyType | |
samplingFeature | samplingFeatureReference | 0..1 | gml:ReferenceType | external reference |
ultimateFeatureOfInterestName | ultimateFeatureOfInterestName | 0..1 | xsd:string | |
ultimateFeatureOfInterest | ultimateFeatureOfInterestReference | 0..1 | gml:ReferenceType | external reference |

### omsf:GenericObservationType extends omsf:AbstractObservationType

In addition to the properties of omsf:AbstractObservationType, omsf:GenericObservationType has the following property:

OMSF property name | GML SF property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------
result | result | 1 | gml:ReferenceType | extenal reference

Note that as a deviation of the generic rule of post-fixing a word ```Reference``` to the names of the
externally referenced properties, the ```result``` name is not post-fixed to keep the name of the result Property
consistent among all the OMSF Observation types.

### omsf:MeasureObservationType extends omsf:AbstractObservationType

In addition to the properties of omsf:AbstractObservationType, omsf:MeasureObservationType has the following property:

OMSF property name | GML SF property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------
result | result | 1 | gml:MeasureType | |


### omsf:CategoryObservationType extends omsf:AbstractObservationType

In addition to the properties of omsf:AbstractObservationType, omsf:CategoryObservationType has the following property:

OMSF property name | GML SF property name |  Multiplicity | Type | Notes
--------------|--------------|-----------|------
result | result | 1 |  gml:ReferenceType | URI reference recommended indicating both the codelist and the value |

Note that as a deviation of the generic rule of post-fixing a word ```Reference``` to the names of the
externally referenced properties, the ```result``` name is not post-fixed to keep the name of the result Property
consistent among all the OMSF Observation types.

### omsf:TruthObservationType extends omsf:AbstractObservationType

In addition to the properties of omsf:AbstractObservationType, omsf:TruthObservationType has the following property:

OMSF property name | GML SF property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------
result | result | 1 | xsd:boolean | |


### omsf:CountObservationType extends omsf:AbstractObservationType

In addition to the properties of omsf:AbstractObservationType, omsf:CountObservationType has the following property:

OMSF property name | GML SF property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------
result | result| 1 | xsd:integer | |

### omsf:AbstractTimeseriesObservationType extends omsf:AbstractObservationType

This abstract feature type is a common ancestor for all time series Observation types.
In addition to the properties of omsf:AbstractObservationType, omsf:AbstractTimeseriesObservationType has the following property:

OMSF property name | GML SF property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------
timeStep | timeStep | 1..n | xsd:dateTime | one for each time series data point |

### omsf:MeasureTimeseriesObservationType extends omsf:AbstractTimeseriesObservationType

In addition to the properties of omsf:AbstractTimeseriesObservationType, omsf:MeasureTimeseriesObservation has the following properties:

OMSF property name | GML SF property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------
unitOfMeasure | unitOfMeasureReference | 0..1 |  gml:ReferenceType | external reference|
result | 1..n | xsd:double | one for each time series time step |
