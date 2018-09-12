# OGC Observations and Measurements - Simple Feature Encodings

---

**NOTE**: These encodings are work-in-progress, and at this point, has not been endorsed by the OGC or any other standards organization.
It may (and probably will) change in a backwards incompatible way during the drafting process.
The namespaces http://www.opengis.net/omsf-gml/1.0 and http://www.opengis.net/omsf-json/1.0 have not (yet)
been approved by the OGC Naming Authority, and thus may also change. Consider yourself warned.

---

The purpose of this activity is to define simple encodings for the most used O&M Observation types, and thus
enable interoperable O&M data exchange between existing software applications, servers and clients limited to using simple (non-complex)
GML features and/or [GeoJSON](http://geojson.org/). Handling complex feature structure of the O&M XML Implementation
(as in OGC 10-025r1)  is only possible for many WFS server and client software with a considerable implementation cost,
added code complexity and lower performance. In cases when these costs cannot be justified, and when the actual provided
data is relatively simple, the O&M Simple Feature Encodings provide an easy-to-use alternative.

Example of a omsf:MeasureObservation using the GML Simple Features Profile 2.0 encoding:

```xml
<omsf:MeasureObservation gml:id="f-1">
  <gml:identifier codeSpace="fmi-fi-weatherobs">kumpula-2017-08-17_12-00_air-temp-1</gml:identifier>
  <omsf:phenomenonTime>2017-08-17T12:00:00Z</omsf:phenomenonTime>
  <omsf:resultTime>2017-08-17T12:01:25Z</omsf:resultTime>
  <omsf:usedProcedure
    xlink:href="http://xml.fmi.fi/process/met-surface-observations"
    xlink:title="Meteorological surface observations" />
  <omsf:observedProperty
    xlink:href="http://vocab.nerc.ac.uk/collection/P07/current/CFSN0023/"
    xlink:title="air_temperature" />
  <omsf:geometry>
    <gml:Point gml:id="p-1" srsName="http://www.opengis.net/def/crs/EPSG/0/4258" srsDimension="2">
      <gml:pos>60.20307 24.96131</gml:pos>
    </gml:Point>
  </omsf:geometry>
  <omsf:samplingFeatureReference
      xlink:href="TBA"
      xlink:title="Helsinki Kumpula weather observation station"/>
  <omsf:ultimateFeatureOfInterestReference
    xlink:href="http://sws.geonames.org/843429/about.rdf"
    xlink:title="Helsinki Kumpula"/>
  <omsf:result uom="Cel">12.5</omsf:result>
</omsf:MeasureObservation>
```

The same feature using the GeoJSON encoding:
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
    "observedPropertyName": "Air temperature",
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

More details:

* [GML Simple Features Profile 2.0 encoding](./omsf-gml), and
* [GeoJSON encoding](./omsf-json/).


## Acknowledgements

Work on this application profile was initiated by Ilkka Rinne for the needs of Vaisala and Finnish Meteorological Institute in 2017.
