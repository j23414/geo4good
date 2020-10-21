# geo4good
Prep for Geo for Good Summit, mostly a quick run through of online tutorials

* [9th Geo for Good Summit](https://earthoutreachonair.withgoogle.com/events/geoforgood20)
* [Google Earth Engine](https://earthengine.google.com/)
* You'll need to register a API key (and probably enter billing information, hopefully will get free trial to test it out first)
* Then restrict the API either by website or API(?), the dashboard walks through the steps

**Javascript**

```
var L8 = ee.ImageCollection("LANDSAT/LC08/C01/T1_TOA"),       // Load Landsat 8 data
    Iowa = /* color: #64f4ff */ee.Geometry.Polygon(           // Polygon defining Iowa
        [[[-96.51781804374994, 43.12793910148268],
          [-96.69359929374994, 42.70957522673677],
          [-96.29809148124994, 42.22332018226993],
          [-95.85863835624994, 40.575253989325205],
          [-91.90356023124994, 40.575253989325205],
          [-91.42016179374994, 40.274171078114364],
          [-90.98070866874994, 40.874988085677586],
          [-91.15648991874994, 41.10718072617262],
          [-91.15648991874994, 41.43746500277387],
          [-90.45336491874994, 41.60198019649388],
          [-90.18969304374994, 42.09301452390881],
          [-91.20043523124994, 42.87082056151188],
          [-91.15648991874994, 43.51159871705633],
          [-96.60570866874994, 43.54346098094327]]]);
var IAlat = -93.6001497,                                       // Center on Ames, Iowa
    IAlong = 42.0518278;

// (2) Filter Data (Get rid of Cloud Cover)
// This example demonstrates the use of the Landsat 8 QA band to mask clouds.
// Function to mask clouds using the quality band of Landsat 8.
var maskL8 = function(image) {
  var qa = image.select('BQA');
  /// Check that the cloud bit is off.
  // See https://www.usgs.gov/land-resources/nli/landsat/landsat-collection-1-level-1-quality-assessment-band
  var mask = qa.bitwiseAnd(1 << 4).eq(0);
  return image.updateMask(mask);
}

// Map the function over one year of Landsat 8 TOA data and take the median.
var composite = L8
    .filterDate('2016-01-01', '2016-12-31')                     // Pick a time frame
    .map(maskL8)                                                // Remove Clouds
    .filterBounds(Iowa)                                         // Only images over Iowa
    .median();
var image = ee.Image(composite);
var RGB_VIS = {min: 0, max: 0.3, gamma: 1.5, bands: ['B4', 'B3', 'B2']};  // Render 3 bands
var title = ui.Label("LANDSAT/LC08/C01/T1_TOA over Iowa");
title.style().set({
  position: 'top-center'
})

var panel = ui.Panel();
panel.style().set({
  width: '400px',
  position: 'bottom-right'
});

// Organize Elements
//Map.addLayer(image, {bands:['B4','B3','B2'], min: 0, max: 0.3});
Map.setCenter(IAlat, IAlong, 4);
Map.addLayer(image, RGB_VIS, 'RGB');
Map.add(title);
Map.add(panel)

```
