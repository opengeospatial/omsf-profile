# OGC Observations &amp; Measurements - Simple Features Profile

---
*NOTE* This application profile is work-in-progress, and at this point, has not been endorsed by the OGC or any other standards organization. It may (and probably will) change in a backwards incompatible way during the drafting process. The namespaces http://www.opengis.net/omsf-gml/1.0 and http://www.opengis.net/omsf-json/1.0 have not (yet) been approved by the OGC Naming Authority, and thus may also change. Consider yourself warned.

---

The same O&M properties have been encoded into two simple feature encodings: one compliant with the GML Simple Features 
Profile 2.0 and the other with the GeoJSON specification ([IETF RFC 7946](https://tools.ietf.org/html/rfc7946)).

## GML Simple Features Profile Encoding

This GML application schema defines a profile of the OGC Observations and Measurements v2.0 ([OGC Document 10-004r3](http://portal.opengeospatial.org/files/?artifact_id=41579), also published as ISO 19156:2011, Geographic information — Observations and Measurements), and it's XML encoding compliant with GML Simple Features Profile version 2.0 ([OGC Document 10-100r3](http://portal.opengeospatial.org/files/?artifact_id=42729)). The TimeseriesObservation type is compliant with the Simple Features Profile level SF-1 due to repeated time and value elements, all other Observation types are compliant level SF-0.

Example of a omsf:MeasureObservation:

```xml
<omsf:MeasureObservation gml:id="f-1">
  <gml:identifier codeSpace="fmi-fi-weatherobs">kumpula-2017-08-17_12-00_air-temp-1</gml:identifier>
  <omsf:phenomenonTime>2017-08-17T12:00:00Z</omsf:phenomenonTime>
  <omsf:resultTime>2017-08-17T12:01:25Z</omsf:resultTime>
  <omsf:usedProcedure
    xlink:href="http://xml.fmi.fi/process/met-surface-observations"
    xlink:title="Meteorological surface observations" />
  <omsf:observedProperty xlink:href="http://vocab.nerc.ac.uk/collection/P07/current/CFSN0023/" xlink:title="air_temperature" />
  <omsf:featureOfInterestTitle>Helsinki Kumpula</omsf:featureOfInterestTitle>
  <omsf:featureOfInterestGeometry>
    <gml:Point gml:id="p-1" srsName="http://www.opengis.net/def/crs/EPSG/0/4258" srsDimension="2">
      <gml:pos>60.20307 24.96131</gml:pos>
    </gml:Point>
  </omsf:featureOfInterestGeometry>
  <omsf:featureOfInterestReference xlink:href="http://sws.geonames.org/843429/about.rdf"/>
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

XML encodings for the O&M UML classes OM\_ComplexObservation, OM\_DiscreteCoverageObservation, OM\_GeometryObservation, OM\_PointCoverageObservation, and OM\_TemporalObservation are not provided. Thus this implementation is intentionally more limited than the Observations and Measurements - XML Implementation ([OGC Document 10-025r1](http://portal.opengeospatial.org/files/?artifact_id=41510)) and OGC Observations and Measurements – JSON implementation ([OGC Document 15-100r1](https://portal.opengeospatial.org/files/64910)) which are able to express the full O&M abstract model. However, expressing all these is possible by using the omsf:GenericObservation feature type with a remote reference to the Observation result.

This application profile does not provide encodings for the sampling feature data, as the feature of interest is only presented by it's geometry, optionally by it's name and, also optionally, by a remote reference to the description of the complete feature of interest.
 
The purpose of this application schema is to provide simple GML encodings for the most used O&M Observation types, and thus enable interoperable O&M data exchange with software applications, servers and clients limited to using simple (non-complex) GML features compatible with the GML Simple Features profile, version 2.0. For many WFS servers and clients, as well as geographical data presenting and analysing software, handling the complex feature structure mandated by the O&M XML Implementation (as in OGC 10-025r1), is possible only by a considerable implementation cost, added code complexity and lower performance. In cases when these costs cannot be justified, and when the actual provided data is relatively simple, the O&M Simple Features Profile application schema provides an alternative encoding for providing O&M Observations.

The work version of the XML Schema for the OMSF is available at [omsf-gml/omsf-profile.xsd](./omsf-gml/omsf-profile.xsd)

### Design considerations

The following primary design goals have been followed (in priority order):

1. The defined GML feature types must be compliant to the GML Simple Features Profile level SF-0 or SF-1.
1. Each defined feature type must have a relevant geometry property for spatial processing and map visualization purposes.
1. The defined GML feature types must follow the O&M model structure and property naming as long as it does not conflict with higher priority design goals.
1. The defined GML feature types should enable back and forth educated, automated translations between the same kind of OMXML (complex feature) and the OMSF (simple feature) feature instances without data loss in typical cases.
1. The defined GML feature types should be as simple as possible, but not simpler (so called Einstein's razor).

*Notes:*

* As the simple features profile data models for level SF-0 does not allow for including the structural (object oriented) presentation of the feature of interest of the Observation, it's presented by one mandatory (foiGeometry) and two optional (foiName and foiReference) properties. The foiReference property can be used to optionally refer to a full, external description of the feature of interest.
* Time properties with possible period content (phenomenonTime and validTime) have been encoded as two separate properties, one for the period start time (inclusive) and another for the period end time. The values are always of type xsd:dateTime. As the phenomenonTime may also be a time instant, the O&M simple features may either include a single phenomenonTime property (instant) or one or both of phenomenonTimeStart and phenomenonTimeEnd properties. If either the start or the end is not provided, the time period must be interpreted as an open-ended period.
* The metadata and the procedure properties are all provided as a reference-only.
* The observedProperty is provided as a code list with an optional namespace, rather than a plain reference, for semantic and practical reasons: the observed properties (such as air temperature) typically have code-list type, well-known identifiers, which is enough for unambiguous identification and just the right level of complexity for this schema. Also the by-reference property values (using xlink) are often not shown in mapping applications, unlike code list values expressed as a restricted gml:CodeType as required in the GML Simple Features Profile specification.
* In contrast to the OMXML (complex feature) implementation, hard-typing is used for the different Observation types: GenericObservation, MeasureObservation, CategoryObservation, CountObservation, TruthObservation and TimeseriesObservation are each defined as separate feature types with fixed result value types. This is an intentional trade-off between simplicity and flexibility.
* Observations with complicated results, such as coverages, have been considered out-of-scope of this application schema. However, it's possible to encode these using the GenericObservation feature type with a reference to the remotely provided result.
* O&M properties relatedObservation and resultQuality are not included in the encoding to keep it as simple as possible.
* To align better with the [W3C Semantic Sensor Network Ontology specification](https://www.w3.org/TR/vocab-ssn/), the method and the implementation of the measurement procedure has been split into two separate properties: usedProcedure and madeBySensor. 
 
### Timeseries Observations

O&M Simple Features Profile XML Schema contains MeasureTimeseriesObservation feature type for encoding simple, double-valued time series data. 
Time series with result values for several points in time does not fit with the GML Simple Features Profile compliance level SF-0 without mild violence, since repeated elements are not allowed. Technically the time series values (and even time instances) could be encoded inside a single element using list type, but encoding and decoding would require special processing, which would at least partly defeat the gains of restricting the feature type to SF-0. So the TimeseriesObservation is compliant with SF-1, not SF-0 like the other OMSF Observation types.

```xml
<omsf:MeasureTimeseriesObservation gml:id="f-1">
  <omsf:phenomenonTimeStart>2017-08-17T12:00:00Z</omsf:phenomenonTimeStart>
  <omsf:phenomenonTimeEnd>2017-08-17T18:00:00Z</omsf:phenomenonTimeEnd>
  <omsf:resultTime>2017-08-17T12:11:20Z</omsf:resultTime>
  <omsf:usedProcedure xlink:href="http://xml.fmi.fi/process/met-surface-observations" xlink:title="Meteorological surface observations" />
  <omsf:observedProperty xlink:href="http://vocab.nerc.ac.uk/collection/P07/current/CFSN0023/" xlink:title="air_temperature" />
  <omsf:featureOfInterestTitle>Helsinki Kumpula</omsf:foiName>
  <omsf:featureOfInterestGeometry>
    <gml:Point gml:id="p-1" srsName="http://www.opengis.net/def/crs/EPSG/0/4258" srsDimension="2">
      <gml:pos>60.20307 24.96131</gml:pos>
    </gml:Point>
  </omsf:featureOfInterestGeometry>
  <omsf:featureOfInterestReference xlink:href="http://sws.geonames.org/843429/about.rdf"/>
  <omsf:resultTime>2017-08-17T12:00:00Z</omsf:resultTime>
  <omsf:resultTime>2017-08-17T13:00:00Z</omsf:resultTime>
  <omsf:resultTime>2017-08-17T14:00:00Z</omsf:resultTime>
  <omsf:resultTime>2017-08-17T15:00:00Z</omsf:resultTime>
  <omsf:resultTime>2017-08-17T16:00:00Z</omsf:resultTime>
  <omsf:resultTime>2017-08-17T17:00:00Z</omsf:resultTime>
  <omsf:resultTime>2017-08-17T18:00:00Z</omsf:resultTime>
  <omsf:resultUnitOfMeasure codeSpace="UCUM">degC</omsf:resultUnitOfMeasure>
  <omsf:resultValue>12.5</omsf:resultValue>
  <omsf:resultValue>12.0</omsf:resultValue>
  <omsf:resultValue>11.0</omsf:resultValue>
  <omsf:resultValue>13.2</omsf:resultValue>
  <omsf:resultValue>12.5</omsf:resultValue>
  <omsf:resultValue>14.1</omsf:resultValue>
  <omsf:resultValue>14.1</omsf:resultValue>
</omsf:MeasureTimeseriesObservation>
```

The repeated property encoding allows client applications to treat both resultTime and resultValue as arrays is simple values, wtih would not be possible using time-value-pair encoding.

## GeoJSON Encoding
In addition to the XML implementation, a GeoJSON encoding of the same data model is provided with data content and structure matching the GML profile as closely as possible.

The GeoJSON Feature model is a very simple one to start with: a GeoJSON Feature is JSON object with property 'type' with a fixed value 'Feature', a 'geometry' property with one of the 
GeoJSON geometry types (Point, MultiPoint, LineString, MultiLineString, Polygon, MultiPolygon or GeometryCollection), and property called 'properties', containing any JSON object. 

The specification explicitly forbids 'extending' the GeoJSON types, including the Feature, but at the same time it is allowed to use additional properties (called foreign members) is 
GeoJSON documents. The way this requirement is interpreted here, is that you cannot require additional foreing members to be included in a GeoJSON Feature, or change any aspect 
of the Feature properties defined in the specification, and claim that it still is a GeoJSON Feature. Thus using foreign members in GeoJSON Feature instances is OK, as long as you are
not trying to 'retype' the Feature.

Because of both the forbidden extending and better interoperability with existing client applications, the decision has been made to use the GeoJSON Feature type as-is, and to leverage the
'properties' JSON object for encoding the O&M properties. Requiring the use of particular JSON content inside the 'properties' is interpreted as not extending the Feature type, as this
property can contain any JSON by the specification. It's allowed to use properties additional to the OMSF defined ones, too. Thus any OMSF feature defined by the rules below is 
automatically a valid GeoJSON Feature. Additionally, attention has been paid to ensure that the JSON contents of the 'properties' are simple enough to be interpreted by 
typical existing client software.

The geometry property of the OMSF GeoJSON Feature is used for the geometry of the feature of interest of the observation.

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
    "resultUnitOfMeasure": "degC"
  }
}
```

The GeoJSON encoding defines a property set for the same types of O&M features as the GML profile does: GenericObservation, 
MeasureObservation, CategoryObservation, CountObservation and MeasureTimeseriesObservation. The properties expected to be used in
each of the observation type are given in the tables below. The work versions of validation rules for each observation type have been defined using 
JSON Schema in [omsf-json/feature.json](./omsf-json/feature.json).

**Commmon properties**

These properties are defined for all the Observation types

Property name | Multiplicity | Condition | Type | Value
--------------|--------------|-----------|------|------
metadataReference | 0..1 | optional | string (URI) | external reference 
resultTime | 1 | required | string (date-time) | |
phenomenonTime | 0..1 | required if the phenomenon time is a time instant | string (date-time) | |
phenomenonTimeStart | 0..1 | required if the phenomenon time is a time range | string (date-time) | |
phenomenonTimeEnd | 0..1 | required if the phenomenon time is a time range | string (date-time) | |
validTimeStart | 0..1 | optional | string (date-time) | |
validTimeEnd | 0..1 | optional | string (date-time) | |
madeBySensorTitle | 0..1 | optional | string | |
madeBySensorReference | 0..1 | optional | string (URI) | external reference |
usedProcedureTitle | 0..1 | optional | string | |
usedProcedureReference | 0..1 | optional | string (URI) | external reference |
observedPropertyTitle | 0..1 | optional | string | |
observedPropertyReference | 1 | required | string (URI) | external reference |
featureOfInterestTitle | 0..1 | optional | string | |
featureOfInterestReference | 0..1 | optional | string (URI) | external reference |

TODO: rest of the tables

## Acknowledgements

Work on this application profile was initiated by Ilkka Rinne for the needs of Vaisala and Finnish Meteorological Institute in 2017.


