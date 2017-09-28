# A serverless approach to hosting mbtiles

Taking advantage of the mbtile structure to avoid the whole tileserver thing.

[Here's the demo](https://zwhitman.github.io/serverlessDemo/)

## Tools needed
1. [Tippecanoe](https://github.com/mapbox/tippecanoe)
2. [mb-util](https://github.com/mapbox/mbutil)


## Here's the approach

Pulled down a random state geojson like [this one](http://eric.clst.org/wupl/Stuff/gz_2010_us_040_00_20m.json)
```
tippecanoe -o out.mbtiles -l test -z -Z0 gz_2010_us_040_00_20m.json 
mb-util --image_format=pbf out.mbtiles out
gzip -d -r -S .pbf *
find . -type f -exec mv '{}' '{}'.pbf \;
```
load folder to s3 instance 

## Getting the client onboard
Once you have the endpoints, just point your tile source to the folder structure https://s3-us-west-2.amazonaws.com/zwhitman-mbtiletest/out-test/{z}/{x}/{y}.pbf and you're good to go.

### Code w/ zoom-specific styling
```javascript
  map.addLayer({
    "id":"test",
    "type":"line",
    "source": {
        "type": "vector",
	"tiles": ["https://s3-us-west-2.amazonaws.com/zwhitman-mbtiletest/out-test/{z}/{x}/{y}.pbf"],
	},
    "source-layer":"test",
    "paint": {
		"line-width": {
		    "base": 3,
		    "stops": [
				[0,5],
				[12,1]
			]
	
		},
                "line-color": {
                    "base": 1,
                    "stops": [
                        [
                            0,
                            "hsl(109, 58%, 52%)"
                        ],
                        [
                            22,
                            "hsl(240, 100%, 50%)"
                        ]
                    ]
                }
            }

    })
```
