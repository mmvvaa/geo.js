geo.js
======

***a node.js library for performing geospatial operations with geojson***

All features are written in a functional manner with no side effects. In nearly all cases, they accept objects created by the point, linestring, polygon, and featurecollection functions, but these are simply for convenience. Any valid geojson Feature of FeatureCollection will do.

```bash
npm install geo.js
```

- - -

*note: This module is under active development and is in a pre-release form. The first official release is planned mid November 2013.  Most features are pretty stable, but expect some changes periodically up until then.*

- - -

**Features**

- load
- point
- linestring
- polygon
- featurecollection
- extent
- center
- centroid
- explode
- combine
- distance
- buffer
- nearest
- tin
- grid
- planepoint
- inside
- contour

**Planned Features**

Additional feature requests welcomed and encouraged. To request a feature, please add a [github issue](https://github.com/morganherlocker/geo.js/issues) with a description.

- interpolate
- tag
- area
- filter
- intersect
- quantile
- reclass
- remove
- union
- erase
- smooth

- - -

***Examples:***

**load**

Loads a Feature or FeaturCollection from a file.

```javascript
var g = require('geo.js')
var geojsonFile = '/path/to/file/example.geojson'

g.load(geoJsonFile, function(trees, err){
  if(err) throw err
  console.log(trees)
})
```

**point**

Creates a geojson point Feature based on an x and a y coordinate. Properties can be added optionally.

```javascript
var g = require('geo.js')

var point1 = g.point(-75.343, 39.984)
var point2 = g.point(-75.343, 39.984, {name: 'point 1', population: 5000})
console.log(point1)
console.log(point2)
```

**linestring**

Creates a geojson linestring Feature based on a coordinate array. Properties can be added optionally.

```javascript
var g = require('geo.js')
var linestring1 = g.linestring([[102.0, -10.0], [103.0, 1.0], [104.0, 0.0], [130.0, 4.0]])
var linestring2 = g.linestring([[102.0, -10.0], [103.0, 1.0], [104.0, 0.0], [130.0, 4.0]], 
  {name: 'line 1', distance: 145})
console.log(linestring1)
console.log(linestring2)
```

**polygon**

Creates a geojson polygon Feature based on a coordinate array. Properties can be added optionally.

```javascript
var g = require('geo.js')

var polygon1 = g.point([[[20.0,0.0],[101.0,0.0],[101.0,1.0],[100.0,1.0],[100.0,0.0]]])
var polygon2 = g.point([[[20.0,0.0],[101.0,0.0],[101.0,1.0],[100.0,1.0],[100.0,0.0]]], 
  {name: 'line 1', distance: 145})
console.log(polygon1)
console.log(polygon2)
```

**featurecollection**

Creates a geojson FeatureCollection based on an array of features.

```javascript
var g = require('geo.js')
var pt1 = g.point(-75.343, 39.984, {name: 'Location A'})
var pt2 = g.point(-75.833, 39.284, {name: 'Location B'})
var pt3 = g.point(-75.534, 39.123, {name: 'Location C'})

var fc = g.featurecollection([pt1, pt2, pt3])
console.log(fc)
```


**extent**

Calculates the extent of all features and returns a bounding box.

```javascript
var g = require('geo.js')

g.load('path/to/file/example.geojson', function(err, features){
  if(err) throw err
  g.extent(features, function(extent){
    console.log(extent) // [minX, minY, maxX, maxY]
  })
})
```

**center**

Calculates the absolute center point of all features.

```javascript
var g = require('geo.js')

g.load('path/to/file/example.geojson', function(layer, err){
  if(err) throw err
  g.center(layer, function(center){
    console.log(center)
  })
})
```

**centroid**

Calculates the centroid of a polygon Feature or FeatureCollection using the geometric mean of all vertices. This lessons the effect of small islands and artifacts when calculating the centroid of a set of polygons.

```javascript
var g = require('geo.js')
var poly = g.polygon([[[0,0], [0,10], [10,10] , [10,0]]])

g.centroid(poly, function(err, centroid){
  if(err) throw err
  console.log(centroid) // a point at 5, 5
})
```



**explode**

Takes a Feature or FeatureCollection and return all vertices as a collection of points.

```javascript
var g = require('geo.js')
var poly = g.polygon([[[0,0], [0,10], [10,10] , [10,0]]])

g.explode(poly, function(err, vertices){
  if(err) throw err
  console.log(vertices)
})
```

**combine**

Combines an array of point, linestring, or polygon features into multipoint, multilinestring, or multipolygon features.
    
```javascript
var g = require('geo.js')
var pt1 = g.point(50, 1)
var pt2 = g.point(100, 101)

g.combine([pt1, pt2], function(err, combined){
  if(err) throw err
  console.log(combined)
})
```

**inside**

Checks to see if a point is inside of a polygon. The polygon can be convex or concave.

```javascript
var g = require('geo.js')
var poly = g.polygon([[[0,0], [50, 50], [0,100], [100,100], [100,0]]])
var pt = g.point(75, 75)

g.inside(pt, poly, function(err, isInside){
  if(err) throw err
  console.log(isInside) // true
})
```

**buffer**

Buffers a point feature to a given radius. Lines and Polygons support coming soon. Unit selection coming soon too (degrees, miles, km).


```javascript
var g = require('geo.js')
var pt = g.point(0, 0.5)

g.buffer(pt, 10, function(err, buffered){
  if(err) throw err
  console.log(buffered)
})
```

**distance**

Calculates the distance between two point features in miles or kilometers. This uses the haversine formula to account for global curvature.

```javascript
var g = require('geo.js')
var point1 = g.point(-75.343, 39.984)
var point2 = g.point(-75.534, 39.123)
var unit = 'miles' // or 'kilometers'

g.distance(point1, point2, unit, function(err, distance){
  if(err) throw err
  console.log(distance)
})
```

**nearest**

Returns the nearest point feature.

```javascript
var g = require('geo.js')    
var inPoint = g.point(-75.4, 39.4, {name: 'Location A'})

var pt1 = g.point(-75.343, 39.984, {name: 'Location B'})
var pt2 = g.point(-75.833, 39.284, {name: 'Location C'})
var pt3 = g.point(-75.534, 39.123, {name: 'Location D'})
var inFeatures = g.featurecollection([pt1, pt2, pt3])

g.nearest(inPoint, inFeatures, function(err, closestPoint){
  if(err) throw err
  console.log(closestPoint)
})
```

**tin**

Takes a set of points and the name of a z-value property and creates a tin (Triangulated Irregular Network). These are often used for developing elevation contour maps or stepped heat visualizations.

```javascript
var g = require('geo.js')
var z = 'elevation'

g.load('/path/to/pointsfeatures/elevationPoints.geojson', function(err, points){
  g.tin(points, z, function(err, tin){
    if(err) throw err
    console.log(tin)
  })
})
```

**grid**

Takes a bounding box and a cell depth and outputs a feature collection of points in a grid.

```javascript
var g = require('geo.js')
var depth = 15

g.grid([0,0,10,10], depth, function(err, grid){
  console.log(grid) // 15x15 grid of points in a FeatureCollection
})
```

**planepoint**

Takes a trianglular plane and calculates the z value for a point on the plane.

```javascript
var g = require('geo.js')
var point = g.point(-75.3221, 39.529)
// triangle is a polygon with "a", "b", and "c" values representing
// the values of the coordinates in order.
var triangle = g.polygon(
  [[[-75.1221,39.57],[-75.58,39.18],[-75.97,39.86]]], 
  "properties": {"a": 11, "b": 122, "c": 44}
  )

g.planepoint(point, triangle, function(err, zValue){
  if(err) throw err
  console.log(zValue)
})
```

**contour**

Takes a FeatureCollection of points with z values and an array of value breaks and generates contour polygons.  This is a great way to visualize interpolated density on a map.  It is often used for elevation maps, weather maps, and isocrones.  The main advantage over a heat map is that contours allow you to see definitive value boundaries, and the polygons can be used to aggregate data.  For example, you could get the 5000 ft elevation contour of a mountain and the 10000 ft elevation contour, then aggregate the number of trees in each to see how elevation affects tree survival.

```javascript
var g = require('geo.js')
var z = 'elevation'
var resolution = 15
var breaks = [.1, 22, 45, 55, 65, 85,  95, 105, 120, 180]

g.load('../path/to/points.geojson', function(err, points){
  g.contour(points, z, resolution, breaks, function(err, contours){
    if(err) throw err
    console.log(contours)
  })
})
```

- - -

***Development***

**Run Tests**

```shell
cd test 
mocha .
```

- - -

***Credits***

This library is built and maintained by @morganherlocker. If you would like to contribute, please do! :)

I have taken a "picasso" approach to building this library, borrowing from existing code when available and modifying it to meet coding styles and standards of geo.js. Here is a list of places I have pulled ideas and/or code from (all open source or public domain, as far as I know):

https://github.com/ironwallaby/delaunay

https://github.com/jasondavies/conrec.js

http://stackoverflow.com/a/839931/461015

http://en.wikipedia.org/wiki/Haversine_formula

http://en.wikipedia.org/wiki/Ramer-Douglas-Peucker_algorithm 

https://github.com/mbloch/mapshaper

http://en.wikipedia.org/wiki/Delaunay_triangulation

http://svn.osgeo.org/grass/grass/branches/releasebranch_6_4/vector/v.overlay/main.c

http://www.ecse.rpi.edu/Homepages/wrf/Research/Short_Notes/pnpoly.html

http://en.wikipedia.org/wiki/Even%E2%80%93odd_rule

https://github.com/substack/point-in-polygon/blob/master/index.js


[![Bitdeli Badge](https://d2weczhvl823v0.cloudfront.net/morganherlocker/geo.js/trend.png)](https://bitdeli.com/free "Bitdeli Badge")

