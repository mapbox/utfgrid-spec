# UTFGrid

UTFGrid is a specification for rasterized interaction data. As of version 1.2,
it was removed from incubation in the
[MBTiles Specification](https://github.com/mapbox/mbtiles-spec) and split into
its own repository.

See CHANGELOG.md for per-version changes.

## License

This specification is licensed under a [Creative Commons Attribution 3.0 United States License](http://creativecommons.org/licenses/by/3.0/us/).

Applications which make use of the specification are not subject to any restrictions.

## Implementations

### Write

* Mapnik's [grid_renderer](https://github.com/mapnik/mapnik/wiki/MapnikRenderers#grid_renderer) (Uses Antigrain Geometry library for rendering).
* [Various python sample implementations](https://github.com/springmeyer/utfgrid-example-writers)
* GeoScript [groovy example](https://github.com/jericks/geoscript-groovy/blob/master/examples/utfgrid.groovy)
* [TileStache](http://tilestache.org/)
* [create-utfgrids](https://github.com/Ecotrust/create-utfgrids)

### Read

* Wax for adding support to the Leaflet, Modest Maps, OpenLayers, and Google Maps clients: [examples](http://mapbox.com/wax/interaction-mm.html), [code](https://github.com/mapbox/wax/blob/master/control/lib/gridinstance.js)
* [Leaflet.utfgrid](https://github.com/danzel/Leaflet.utfgrid)
* OpenLayers Native: [blog post](http://www.perrygeo.net/wordpress/?p=160), [general demo](http://openlayers.org/dev/examples/utfgrid.html), [example multi-grid](http://openlayers.org/dev/examples/utfgrid_twogrids.html), [example geography-class](http://openlayers.org/dev/examples/utfgrid-geography-class.html)
* [GDAL MBTiles support](http://www.gdal.org/frmt_mbtiles.html)
* [Landez - Python module](https://github.com/makinacorpus/landez)

### Servers

* TileMill - via [tilelive.js](https://github.com/mapbox/tilelive.js) which uses [tilelive-mapnik](https://github.com/mapbox/tilelive-mapnik) to request grid creation and [node-mbtiles](https://github.com/mapbox/node-mbtiles) to cache and fetch grids stored in [MBTiles](http://mapbox.com/mbtiles-spec/) format.
* [TileStache's](https://github.com/migurski/TileStache) `TileStache.Goodies.Providers.MapnikGrid:Provider`
* [django-mbtiles](https://github.com/makinacorpus/django-mbtiles) which serves grids stored in *MBTiles* along with tiles using a simple template tag. 

## Authors

* Tom MacWright (tmcw)
* Will White (willwhite)
* Konstantin Kaefer (kkaefer)
* Justin Miller (incanus)
* Dane Springmeyer (springmeyer)
