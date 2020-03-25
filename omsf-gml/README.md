# OGC Observations and Measurements - GML Simple Features Profile Encoding

This GML application schema defines GML encoding of the the [OMSF implementation model](https://github.com/opengeospatial/omsf-profile) compliant with GML Simple Features Profile
version 2.0 ([OGC Document 10-100r3](http://portal.opengeospatial.org/files/?artifact_id=42729)).
The TimeseriesObservation type is compliant with the Simple Features Profile level SF-1 due to repeated time and value
elements, all other Observation types are compliant level SF-0.

Example of a omsf:MeasureObservation encoded in GML:

```xml
<omsf:MeasureObservation gml:id="f-1">
  <gml:identifier codeSpace="fmi-fi-weatherobs">kumpula-2017-08-17_12-00_air-temp-1</gml:identifier>
  <omsf:geometry>
      <gml:Point gml:id="p-1" srsName="http://www.opengis.net/def/crs/EPSG/0/4258" srsDimension="2">
          <gml:pos>60.20307 24.96131</gml:pos>
      </gml:Point>
  </omsf:geometry>
  <omsf:phenomenonTime>2017-08-17T12:00:00Z</omsf:phenomenonTime>
  <omsf:resultTime>2017-08-17T12:01:25Z</omsf:resultTime>
  <omsf:procedureName>Meteorological surface observations</omsf:procedureName>
  <omsf:procedureReference xlink:href="http://xml.fmi.fi/process/met-surface-observations" />
  <omsf:observedProperty xlink:href="http://vocab.nerc.ac.uk/collection/P07/current/CFSN0023/" xlink:title="air_temperature" />
  <omsf:observerName>Vaisala AWS310</omsf:observerName>  
  <omsf:platformName>Helsinki Kumpula weather observation station</omsf:platformName>
  <omsf:ultimateFeatureOfInterestName>Helsinki Kumpula</omsf:ultimateFeatureOfInterestName>
  <omsf:ultimateFeatureOfInterestReference xlink:href="http://sws.geonames.org/843429/about.rdf"/>
  <omsf:result uom="Cel">12.5</omsf:result>
```

MeasureTimeseriesObservation example:
```xml
<omsf:MeasureTimeseriesObservation gml:id="f-1">
   <omsf:geometry>
        <gml:Point gml:id="p-1" srsName="http://www.opengis.net/def/crs/EPSG/0/4258" srsDimension="2">
            <gml:pos>60.20307 24.96131</gml:pos>
        </gml:Point>
    </omsf:geometry>
    <omsf:phenomenonTimeStart>2017-08-17T12:00:00Z</omsf:phenomenonTimeStart>
    <omsf:phenomenonTimeEnd>2017-08-17T18:00:00Z</omsf:phenomenonTimeEnd>
    <omsf:resultTime>2017-08-17T12:11:20Z</omsf:resultTime>
    <omsf:procedureName>Meteorological surface observations</omsf:procedureName>
    <omsf:procedureReference xlink:href="http://xml.fmi.fi/process/met-surface-observations" />
    <omsf:observedProperty xlink:href="http://vocab.nerc.ac.uk/collection/P07/current/CFSN0023/" xlink:title="air_temperature" />
    <omsf:observerName>Vaisala AWS310</omsf:observerName>    
    <omsf:platformName>Helsinki Kumpula weather observation station</omsf:platformName>
    <omsf:ultimateFeatureOfInterestName>Helsinki Kumpula</omsf:ultimateFeatureOfInterestName>
    <omsf:ultimateFeatureOfInterestReference xlink:href="http://sws.geonames.org/843429/about.rdf"/>
    <omsf:timeStep>2017-08-17T12:00:00Z</omsf:timeStep>
    <omsf:timeStep>2017-08-17T13:00:00Z</omsf:timeStep>
    <omsf:timeStep>2017-08-17T14:00:00Z</omsf:timeStep>
    <omsf:timeStep>2017-08-17T15:00:00Z</omsf:timeStep>
    <omsf:timeStep>2017-08-17T16:00:00Z</omsf:timeStep>
    <omsf:timeStep>2017-08-17T17:00:00Z</omsf:timeStep>
    <omsf:timeStep>2017-08-17T18:00:00Z</omsf:timeStep>
    <omsf:unitOfMeasure xlink:href="www.opengis.net/def/uom/UCUM/degC" xlink:title="Degree Celsius"/>
    <omsf:result>12.5</omsf:result>
    <omsf:result>12.0</omsf:result>
    <omsf:result>11.0</omsf:result>
    <omsf:result>13.2</omsf:result>
    <omsf:result>12.5</omsf:result>
    <omsf:result>14.1</omsf:result>
    <omsf:result>14.1</omsf:result>
</omsf:MeasureTimeseriesObservation>
```

The work version of the XML Schema for the OMSF is available at [omsf-profile.xsd](./omsf-profile.xsd)

## GML encoding details

### omsf:AbstractObservationType

This type is an abstract base type for all the OMSF Observation types. The ```phenomenonTime``` mapping is implemented
with XML Schema choice between a single ```xsd:dateTime``` valued property ```phenomenonTime``` (when the value is a time
instant) and a set of two ```xsd:dateTime``` valued properties ```phenomenonTimeStart``` and ```phenomenonTimeEnd``` (when the value is a time
period). Similarly the time period of the ```validTime``` property is split into two properties ```validTimeStart```
and ```validTimeEnd```.

Unless stated otherwise, all property names linking to externally referenced resources end with
```@link```. The display names for those resources have otherwise identical property names but end with ```@name```. The reason for providing
the separate, optional ```@name``` properties instead of leveraging the ```xlink:title``` attributes is that referenced 
external resources may not be available in all cases, but it may still be useful for the data user to get the display names these resources.
This would not be possible using the only the gml:ReferenceType which does not allow using xlink:title by itself as the xlink:href attribute 
is always required. In cases where there is no externally referenceable object to link to the ```@name``` property alone without 
the ```@link``` may be sufficient to convey the required information.

OMSF property name | GML SF property name | Multiplicity | GML type |  Notes
--------------|--------------|-----------|------|-------
geometry | geometry | 1 | gml:GeometryPropertyType | |
phenomenonTime | phenomenonTime | 0..1 (choice with start-end pair) | xsd:dateTime | required only if the phenomenon time is a time instant |
phenomenonTime | phenomenonTimeStart | 0..1 (choice with single phenomenonTime) | xsd:dateTime | required only if the phenomenon time is a time range  |
phenomenonTime | phenomenonTimeEnd | 0..1 (choice with single phenomenonTime) | xsd:dateTime | required only if the phenomenon time is a time range  |
resultTime    | resultTime | 1 | xsd:dateTime | |
stimulusTime    | stimulusTime | 0..1 | xsd:dateTime | |
validTime    | validTimeStart | 0..1 | xsd:dateTime | |
validTime | validTimeEnd | 0..1 | xsd:dateTime | |
observer | observerName | 0..1 | xsd:string | |
observer | observerReference | 0..1 | gml:ReferenceType | external reference |
platform | platformName | 0..1 | xsd:string | |
platform | platformReference | 0..1 | gml:ReferenceType | external reference |
procedure | procedureName | 0..1 | xsd:string | |
procedure | procedureReference | 0..1 | gml:ReferenceType | external reference |
observedProperty | observedProperty | 1 | gml:ReferenceType | external reference to a code list|
proximateFeatureOfInterest | proximateFeatureOfInterestName | 0..1 | xsd:string | |
proximateFeatureOfInterest | proximateFeatureOfInterestReference | 0..1 | gml:ReferenceType | external reference |
ultimateFeatureOfInterestName | ultimateFeatureOfInterestName | 0..1 | xsd:string | |
ultimateFeatureOfInterest | ultimateFeatureOfInterestReference | 0..1 | gml:ReferenceType | external reference |
metadata      | metadataReference | 0..1 | gml:ReferenceType | external reference

### omsf:GenericObservationType extends omsf:AbstractObservationType

In addition to the properties of omsf:AbstractObservationType, omsf:GenericObservationType has the following property:

OMSF property name | GML SF property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------|-------
result | resultReference | 1 | gml:ReferenceType | external reference

### omsf:MeasureObservationType extends omsf:AbstractObservationType

In addition to the properties of omsf:AbstractObservationType, omsf:MeasureObservationType has the following property:

OMSF property name | GML SF property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------|-------
result | result | 1 | gml:MeasureType | |


### omsf:CategoryObservationType extends omsf:AbstractObservationType

In addition to the properties of omsf:AbstractObservationType, omsf:CategoryObservationType has the following property:

OMSF property name | GML SF property name |  Multiplicity | Type | Notes
--------------|--------------|-----------|------|-------
result | result | 1 |  gml:ReferenceType | URI reference recommended indicating both the codelist and the value |

Note that as a deviation of the generic rule of post-fixing a word ```Reference``` to the names of the
externally referenced properties, the ```result``` name is not post-fixed. 

### omsf:TruthObservationType extends omsf:AbstractObservationType

In addition to the properties of omsf:AbstractObservationType, omsf:TruthObservationType has the following property:

OMSF property name | GML SF property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------|-------
result | result | 1 | xsd:boolean | |


### omsf:CountObservationType extends omsf:AbstractObservationType

In addition to the properties of omsf:AbstractObservationType, omsf:CountObservationType has the following property:

OMSF property name | GML SF property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------|-------
result | result| 1 | xsd:integer | |

### omsf:AbstractTimeseriesObservationType extends omsf:AbstractObservationType

This abstract feature type is a common ancestor for all time series Observation types.
In addition to the properties of omsf:AbstractObservationType, omsf:AbstractTimeseriesObservationType has the following property:

OMSF property name | GML SF property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------|-------
timeStep | timeStep | 1..n | xsd:dateTime | one for each time series data point |

### omsf:MeasureTimeseriesObservationType extends omsf:AbstractTimeseriesObservationType

In addition to the properties of omsf:AbstractTimeseriesObservationType, omsf:MeasureTimeseriesObservation has the following properties:

OMSF property name | GML SF property name | Multiplicity | Type | Notes
--------------|--------------|-----------|------|-------
unitOfMeasure | unitOfMeasure | 0..1 |  gml:ReferenceType | external reference to a code list|
result | 1..n | xsd:double | one for each time series time step | |

Time series with result values for several points in time does not fit with the GML Simple Features Profile
compliance level SF-0 without mild violence, since repeated elements are not allowed. Technically the time series
values (and even time instances) could be encoded inside a single element using list type, but encoding and
decoding would require special processing, which would at least partly defeat the gains of restricting the feature type to SF-0.
Thus the MeasureTimeseriesObservation contains repeated ```timeStep``` and ```result``` properties which require using
SF-1 compliance level when encoding into GML Simple Features.

Using of repeated properties allows client applications to treat both ```timeStep``` and ```result``` as arrays of
simple values, which would not be possible using time-value-pair encoding.
