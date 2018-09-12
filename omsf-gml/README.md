# OGC Observations and Measurements - GML Simple Features Profile Encoding

This GML application schema defines a profile of the OGC Observations and Measurements v2.0
([OGC Document 10-004r3](http://portal.opengeospatial.org/files/?artifact_id=41579), also published as ISO 19156:2011,
Geographic information — Observations and Measurements), and it's XML encoding compliant with GML Simple Features Profile
version 2.0 ([OGC Document 10-100r3](http://portal.opengeospatial.org/files/?artifact_id=42729)).
The TimeseriesObservation type is compliant with the Simple Features Profile level SF-1 due to repeated time and value
elements, all other Observation types are compliant level SF-0.

Example of a omsf:MeasureObservation:

```xml
<omsf:MeasureObservation gml:id="f-1">
  <gml:identifier codeSpace="fmi-fi-weatherobs">kumpula-2017-08-17_12-00_air-temp-1</gml:identifier>
  <omsf:phenomenonTime>2017-08-17T12:00:00Z</omsf:phenomenonTime>
  <omsf:resultTime>2017-08-17T12:01:25Z</omsf:resultTime>
  <omsf:usedProcedure
    xlink:href="http://xml.fmi.fi/process/met-surface-observations"
    xlink:title="Meteorological surface observations" />
  <omsf:observedProperty
    xlink:href="http://vocab.nerc.ac.uk/collection/P07/current/CFSN0023/air_temperature"
    xlink:title="air_temperature" />
  <omsf:samplingFeatureReference
      xlink:href="TBA"
      xlink:title="Helsinki Kumpula weather observation station"/>
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

This profile of the OGC Observations and Measurements Abstract Specification defines XML encodings only for the following O&M UML classes:

O&M v2.0 class | OGC name | OMSF feature type | SF compliance
---------------|----------|-------------------|---------------
OM\_CategoryObservation | http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_CategoryObservation | omsf:CategoryObservation | SF-0
OM\_CountObservation | http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_CountObservation | omsf:CountObservation | SF-0
OM\_Measurement | http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_Measurement | omsf:MeasureObservation | SF-0
OM\_Observation | http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_Observation | omsf:GenericObservation | SF-0
OM\_TimeSeriesObservation | http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_TimeSeriesObservation | omsf:MeasureTimeseriesObservation | SF-1
OM\_TruthObservation | http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_TruthObservation | omsf:TruthObservation | SF-0

XML encodings for the O&M UML classes OM\_ComplexObservation, OM\_DiscreteCoverageObservation, OM\_GeometryObservation, OM\_PointCoverageObservation, and
OM\_TemporalObservation are not provided. Thus this implementation is intentionally more limited than the
Observations and Measurements - XML Implementation ([OGC Document 10-025r1](http://portal.opengeospatial.org/files/?artifact_id=41510))
and OGC Observations and Measurements – JSON implementation ([OGC Document 15-100r1](https://portal.opengeospatial.org/files/64910))
which are able to express the full O&M abstract model. However, expressing all these is possible by using the omsf:GenericObservation
feature type with a remote reference to the Observation result.

This application profile does not provide encodings for the sampling feature data, as the feature of interest is only presented by
it's geometry, optionally by it's name and, also optionally, by a remote reference to the description of the complete feature of
interest.

The work version of the XML Schema for the OMSF is available at [omsf-profile.xsd](./omsf-profile.xsd)

## Design considerations

The following primary design goals have been followed (in priority order):

1. The defined GML feature types must be compliant to the GML Simple Features Profile level SF-0 or SF-1.
1. Each defined feature type must have a relevant geometry property for spatial processing and map visualization purposes.
1. The defined GML feature types must follow the O&M model structure and property naming as long as it does not conflict with higher priority design goals.
1. The defined GML feature types should enable back and forth educated, automated translations between the same kind of OMXML (complex feature) and the OMSF (simple feature) feature instances without data loss in typical cases.
1. The defined GML feature types should be as simple as possible, but not simpler (so called Einstein's razor).

*Notes:*

* As the simple features profile data models for level SF-0 does not allow for including the structural (object oriented) presentation of the feature of interest of the Observation, it's presented by one mandatory (featureOfInterestGeometry) and two optional (featureOfInterestName and featureOfInterestReference) properties. The featureOfInterestReference property can be used to optionally refer to a full, external description of the feature of interest.
* Time properties with possible period content (phenomenonTime and validTime) have been encoded as two separate properties, one for the period start time (inclusive) and another for the period end time. The values are always of type xsd:dateTime. As the phenomenonTime may also be a time instant, the O&M simple features may either include a single phenomenonTime property (instant) or one or both of phenomenonTimeStart and phenomenonTimeEnd properties. If either the start or the end is not provided, the time period must be interpreted as an open-ended period.
* The metadata and the procedure properties are all provided as a reference-only.
* In contrast to the OMXML (complex feature) implementation, hard-typing is used for the different Observation types: GenericObservation, MeasureObservation, CategoryObservation, CountObservation, TruthObservation and MeasureTimeseriesObservation are each defined as separate feature types with fixed result value types. This is an intentional trade-off between simplicity and flexibility.
* Observations with complicated results, such as coverages, have been considered out-of-scope of this application schema. However, it's possible to encode these using the GenericObservation feature type with a reference to the remotely provided result.
* O&M properties relatedObservation and resultQuality are not included in the encoding to keep it as simple as possible.
* To align better with the [W3C Semantic Sensor Network Ontology specification](https://www.w3.org/TR/vocab-ssn/), the method and the implementation of the measurement procedure has been split into two separate properties: usedProcedure and madeBySensor.

## Properties
The geometry property of the OMSF features is the geometry of the sampling feature of the observation, or if
no sampling feature was used, a representative geometry of the ultimate feature of interest.
Other OMSF properties expected to be used in each of the observation type are given in the tables below.

### Simplified mapping of O&M Observation properties
The implementation model of the OMSF GML encoding is a simplified version of the Observation class
as defined in the OGC and ISO 19156 standard. The following table summarises the simplification decisions applied:

O&M attribute/association | O&M type | O&M Multiplicity  |OMSF property | OMSF type |
--------------------------|----------|-------------------|--------------|-----------|
featureOfInterest | association with GFI_Feature | 1 | geometry | gml:GeometryPropertyType |
featureOfInterest | association with GFI_Feature | 1 | samplingFeatureReference | gml:ReferenceType |
featureOfInterest | association with GFI_Feature | 1 | ultimateFeatureOfInterestName | xsd:string |
featureOfInterest | association with GFI_Feature | 1 | ultimateFeatureOfInterestReference | gml:ReferenceType |
metadata | association with MD_Metadata | 0..1 | metadataReference | gml:ReferenceType |
observedProperty | association with GF_PropertyType | 1 | observedProperty | gml:ReferenceType |
parameter | NamedValue | 0..n | n/a | n/a |
phenomenonTime | TM_Object | 1  | phenomenonTime | xsd:dateTime |
phenomenonTime | TM_Object | 1  | phenomenonTimeStart | xsd:dateTime |
phenomenonTime | TM_Object | 1  | phenomenonTimeEnd | xsd:dateTime |
procedure | association with OM_Process | 1 | usedProcedure | gml:ReferenceType |
procedure | association with OM_Process | 1 | madeBySensor | gml:ReferenceType |
relatedObservation | association with self | 0..n |  n/a | n/a |
result | Any | 1 | result | varied |
result | Any | 1 | timeStep | xsd:dateTime |
result | Any | 1 | unitOfMeasure | gml:ReferenceType |
resultQuality | DQ_Element | 0..n | n/a | n/a |
resultTime | TM_Instant | 1 | resultTime | xsd:dateTime |
validTime | TM_Period | 0..1 | validTimeStart | xsd:dateTime |
validTime | TM_Period | 0..1 | validTimeEnd | xsd:dateTime |

Rationale for the not included properties:

* **parameter**: 0..n multiplicity of name-value pairs (as user defined types) would require compliance level SF-1, or a specially encoded list type. Trade-off between completeness and simplicity.
* **relatedObservation**: not a problem to include technically (as a reference), but rarely used in practice
* **resultQuality**: embedded quality info would a user-defined type and thus SF-1 level. Rarely used in practice,  trade-off between completeness and simplicity.

### Common properties
These properties are defined for all the Observation types:

Property name | Multiplicity | Type |  Notes
--------------|--------------|-----------|------|
metadataReference | 0..1 | gml:ReferenceType | external reference
resultTime | 1 | xsd:dateTime | |
phenomenonTime | 0..1 (choice with start-end pair) | xsd:dateTime | required only if the phenomenon time is a time instant |
phenomenonTimeStart | 0..1 (choice with single phenomenonTime) | xsd:dateTime | required only if the phenomenon time is a time range  |
phenomenonTimeEnd | 0..1 (choice with single phenomenonTime) | xsd:dateTime | required only if the phenomenon time is a time range  |
validTimeStart | 0..1 | xsd:dateTime | |
validTimeEnd | 0..1 | xsd:dateTime | |
madeBySensor | 0..1 | gml:ReferenceType | external reference |
usedProcedure | 0..1 | gml:ReferenceType | external reference |
observedProperty | 1 | gml:ReferenceType | external reference |
geometry | 1 | gml:GeometryPropertyType | |
samplingFeatureReference | 0..1 | gml:ReferenceType | external reference |
ultimateFeatureOfInterestName | 0..1 | xsd:string | |
ultimateFeatureOfInterestReference | 0..1 | gml:ReferenceType | external reference |

### GenericObservation

In addition to the common properties listed above, omsf:GenericObservation has the following properties:

Property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------
result | 1 | gml:ReferenceType | extenal reference


### MeasureObservation

In addition to the common properties listed above, omsf:MeasureObservation has the following properties:

Property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------
result | 1 | gml:MeasureType | |


### CategoryObservation

In addition to the common properties listed above, omsf:CategoryObservation has the following properties:

Property name |  Multiplicity | Type | Notes
--------------|--------------|-----------|------
result | 1 |  gml:ReferenceType | URI reference recommended indicating both the codelist and the value |

### TruthObservation

In addition to the common properties listed above, omsf:TruthObservation has the following properties:

Property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------
result | 1 | xsd:boolean | |


### CountObservation

In addition to the common properties listed above, omsf:CountObservation has the following properties:

Property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------
result| 1 | xsd:integer | |


### MeasureTimeseriesObservation

In addition to the common properties listed above, omsf:MeasureTimeseriesObservation has the following properties:

Property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------
timeStep | 1..n | xsd:dateTime | |
unitOfMeasure | 0..1 |  gml:ReferenceType | external reference|
result | 1..n | xsd:double | |

## Encoding timeseries data

O&M Simple Features Profile XML Schema contains MeasureTimeseriesObservation feature type for encoding simple,
double-valued time series data.
Time series with result values for several points in time does not fit with the GML Simple Features Profile
compliance level SF-0 without mild violence, since repeated elements are not allowed. Technically the time series
values (and even time instances) could be encoded inside a single element using list type, but encoding and
decoding would require special processing, which would at least partly defeat the gains of restricting the feature type to SF-0.
So the MeasureTimeseriesObservation is compliant with SF-1, not SF-0 like the other OMSF Observation types.

```xml
<omsf:MeasureTimeseriesObservation gml:id="f-1">
  <omsf:phenomenonTimeStart>2017-08-17T12:00:00Z</omsf:phenomenonTimeStart>
  <omsf:phenomenonTimeEnd>2017-08-17T18:00:00Z</omsf:phenomenonTimeEnd>
  <omsf:resultTime>2017-08-17T12:11:20Z</omsf:resultTime>
  <omsf:usedProcedure
    xlink:href="http://xml.fmi.fi/process/met-surface-observations"
    xlink:title="Meteorological surface observations" />
  <omsf:observedProperty
    xlink:href="http://vocab.nerc.ac.uk/collection/P07/current/CFSN0023/"
    xlink:title="air_temperature" />
  <omsf:samplingFeatureReference
      xlink:href="TBA"
      xlink:title="Helsinki Kumpula weather observation station"/>
  <omsf:geometry>
      <gml:Point gml:id="p-1" srsName="http://www.opengis.net/def/crs/EPSG/0/4258" srsDimension="2">
          <gml:pos>60.20307 24.96131</gml:pos>
      </gml:Point>
  </omsf:geometry>
  <omsf:ultimateFeatureOfInterestReference
    xlink:href="http://sws.geonames.org/843429/about.rdf"
    xlink:title="Helsinki Kumpula"/>
  <omsf:timeStep>2017-08-17T12:00:00Z</omsf:timeStep>
  <omsf:timeStep>2017-08-17T13:00:00Z</omsf:timeStep>
  <omsf:timeStep>2017-08-17T14:00:00Z</omsf:timeStep>
  <omsf:timeStep>2017-08-17T15:00:00Z</omsf:timeStep>
  <omsf:timeStep>2017-08-17T16:00:00Z</omsf:timeStep>
  <omsf:timeStep>2017-08-17T17:00:00Z</omsf:timeStep>
  <omsf:timeStep>2017-08-17T18:00:00Z</omsf:timeStep>
  <omsf:unitOfMeasure
    xlink:href="www.opengis.net/def/uom/UCUM/degC"
    xlink:title="Degree Celsius"/>
  <omsf:result>12.5</omsf:result>
  <omsf:result>12.0</omsf:result>
  <omsf:result>11.0</omsf:result>
  <omsf:result>13.2</omsf:result>
  <omsf:result>12.5</omsf:result>
  <omsf:result>14.1</omsf:result>
  <omsf:result>14.1</omsf:result>
</omsf:MeasureTimeseriesObservation>
```

The repeated property encoding allows client applications to treat both ```timeStep``` and ```result``` as arrays of
simple values, which would not be possible using time-value-pair encoding.
