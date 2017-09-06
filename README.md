# OGC Observations &amp; Measurements - Simple Features Profile XML Implementation

---
*NOTE* This application profile is work-in-progress, and at this point, has not been endorsed by the OGC or any other standards organization. It may (and probably will) change in a backwards incompatible way during the drafting process. The namespace http://www.opengis.net/omsf/1.0 used has not (yet) been approved by the OGC Naming Authority, and thus may also change. Consider yourself warned.

---

This GML application schema defines a profile of the OGC Observations and Measurements v2.0 ([OGC Document 10-004r3](http://portal.opengeospatial.org/files/?artifact_id=41579), also published as ISO/DIS 19156:2010, Geographic information — Observations and Measurements), and it's XML encoding compliant with GML Simple Features Profile version 2.0 ([OGC Document 10-100r3](http://portal.opengeospatial.org/files/?artifact_id=42729)). All the O&M Observation types provided in this application schema are compliant with the GML Simple Features Profile level SF-0.

Example of a omsf:MeasurementObservation:

```xml
<omsf:MeasureObservation gml:id="f-1">
  <omsf:phenomenonTime>2017-08-17T12:00:00Z</omsf:phenomenonTime>
  <omsf:resultTime>2017-08-17T12:01:25Z</omsf:resultTime>
  <omsf:procedure
    xlink:href="http://xml.fmi.fi/process/met-surface-observations"
    xlink:title="Meteorological surface observations, collected following WMO No. 544, Manual on the Global Observing System, Volume I - Global aspects" />
  <omsf:parameters>http://foo.bar.org/some/fully/qualified/uri=2017-08-17T12:00:00Z analysisCount=1</omsf:parameters>
  <omsf:observedProperty codeSpace="cf-standard-names/46">air_temperature</omsf:observedProperty>
  <omsf:foiName>Helsinki Kumpula</omsf:foiName>
  <omsf:foiGeometry>
    <gml:Point gml:id="p-1" srsName="http://www.opengis.net/def/crs/EPSG/0/4258" srsDimension="2">
      <gml:pos>60.20307 24.96131</gml:pos>
    </gml:Point>
  </omsf:foiGeometry>
  <omsf:foiReference xlink:href="http://sws.geonames.org/843429/about.rdf"/>
  <omsf:result uom="degC">12.5</omsf:result>
</omsf:MeasureObservation>
```

This profile of the OGC Observations and Measurements Abstract Specification defines XML encodings only for the following O&M UML classes:

O&M v2.0 class | OGC name | OMSF feature type |
---------|----------|------------------|
OM\_CategoryObservation | http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_CategoryObservation | omsf:CategoryObservation
OM\_CountObservation | http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_CountObservation | omsf:CountObservation
OM\_Measurement | http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_Measurement | omsf:MeasureObservation
OM\_Observation | http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_Observation| omsf:GenericObservation
OM\_TimeSeriesObservation | http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_TimeSeriesObservation | omsf:TimeseriesObservation
OM\_TruthObservation | http://www.opengis.net/def/observationType/OGC-OM/2.0/OM_TruthObservation | omsf:TruthObservation

XML encodings for the O&M UML classes OM\_ComplexObservation, OM\_DiscreteCoverageObservation, OM\_GeometryObservation, OM\_PointCoverageObservation, and OM\_TemporalObservation are not provided. Thus this implementation is intentionally more limited than the Observations and Measurements - XML Implementation ([OGC Document 10-025r1](http://portal.opengeospatial.org/files/?artifact_id=41510)) and OGC Observations and Measurements – JSON implementation ([OGC Document 15-100r1](https://portal.opengeospatial.org/files/64910)) which are able to express the full O&M abstract model. However, expressing all these is possible by using the omsf:GenericObservation feature type with a remote reference to the Observation result.

This application profile does not provide encodings for the sampling feature data, as the feature of interest is only presented by it's geometry, optionally by it's name and, also optionally, by a remote reference to the description of the complete feature of interest.
 
The purpose of this application schema is to provide simple GML encodings for the most used O&M Observation types, and thus enable interoperable O&M data exchange with software applications, servers and clients limited to using simple (non-complex) GML features compatible with the GML Simple Features profile, version 2.0. For many WFS servers and clients, as well as geographical data presenting and analysing software, handling the complex feature structure mandated by the O&M XML Implementation (as in OGC 10-025r1), is possible only by a considerable implementation cost, added code complexity and lower performance. In cases when these costs cannot be justified, and when the actual provided data is relatively simple, the O&M Simple Features Profile application schema provides an alternative encoding for providing O&M Observations.

## Design considerations

The following primary design goals have been followed (in priority order):

1. The defined GML feature types must be compliant to the GML Simple Features Profile level SF-0.
1. Each defined feature type must have a relevant geometry property for spatial processing and map visualization purposes.
1. The defined GML feature types must follow the O&M model structure and property naming as long as it does not conflict with higher priority design goals.
1. The defined GML feature types should enable back and forth educated, automated translations between the same kind of OMXML (complex feature) and the OMSF (simple feature) feature instances without data loss in typical cases.
1. The defined GML feature types should be as simple as possible, but not simpler (so called Einstein's razor).

Design notes:

* As the simple features profile data models for level SF-0 does not allow for including the strutural (object oriented) presentation of the feature of interest of the Observation, it's presented by one mandatory (foiGeometry) and two optional (foiName and foiReference) properties. The foiReference property can be used to optionally refer to a full, external description of the feature of interest.
* Time properties with possible period content (phenomenonTime and validTime) have been encoded as two separate properties, one for the period start time (inclusive) and another for the period end time. The values are always of type xsd:dateTime. As the phenomenonTime may also be a time instant, the O&M simple features may either include a single phenomenonTime property (instant) or one or both of phenomenonTimeStart and phenomenonTimeEnd properties. If either the start or the end is not provided, the time period must be interpreted as an open-ended period.
* The metadata, relatedObservation and procedure properties are all provided as a reference-only.
* The observedProperty is provided as a code list with an optional namespace, rather than a plain reference, for semantic and practical reasons: the observed properties (such as air temperature) typically have code-list type, well-known identifiers, which is enough for unambiguous identification and just the right level of complexity for this schema. Also the by-reference property values (using xlink) are often not shown in mapping applications, unlike code list values expressed as a restricted gml:CodeType as required in the GML Simple Features Profile specification.
* In order to keep the Observation types compliant with the SF-0, the optional parameter properties are encoded as a white-space separated key-value pair list withing the parameters property. In practice the parameter properties are not used very much, and more than one per Observation only rarely.
* In contrast to the OMXML (complex feature) implementation, hard-typing is used for the different Observation types: GenericObservation, MeasureObservation, CategoryObservation, CountObservation, TruthObservation and TimeseriesObservation are each defined as separate feature types with fixed result value types. This is an intentional trade-off between simplicity and flexibility.
* Observations with complicated results, such as coverages, have been considered out-of-scope of this application schema. However, it's possible to encode these using the GenericObservation feature type with a reference to the remotely provided result.
* TimeseriesObservation has been provided due to a common need, but it only supports a evenly spaced (in time), double-valued results for a single observed property with the same unit of measure for each value, encoded as a white-space separated double list. This a concise, but not very XML-like encoding, and it's probably not suitable for very long time series data Observations. The optional resultOriginTime property is provided to unambiguously provide the time instance of the first value in the series. If not given, this is assumed to be equal to value of the phenomenonTimeStart property.

## Acknowledgements

Work on this application profile was initiated by Ilkka Rinne for the needs of Vaisala and Finnish Meteorological Institute in 2017.


