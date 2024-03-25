# DJI Flight Control

## Info

### Waypoint Feature

- In order to load a flight plan onto a DJI drone without their prioprietary software,
  the drone must support loading [Waypoints][1].
- It's a mixed bag which drones actually support this feature, with the Sep '23 Mini 4 Pro
  supporting and the May '22 Mini 3 Pro not supporting it.
- Generally the more expensive 'enterprise' drones will support this feature,
  while cheaper 'hobbyist' drones will not, making this obstructive for drone TM in
  developing contexts.

#### Supported Drones

- Mini 4 Pro
- Mavic 3
- Mavic 3 Classic
- Mavic 3 Pro
- Mavic 3 Cine
- Air 3

#### The KMZ Flight Plan Format

- The drones require a KMZ file, which basically contains zipped
  (ZIP compression) KML files.
- Structure [Reference 1][2] [Reference 2][3]:

```
any_route_name_you_want.kmz
	wpmz (dir)
		res (auxillary resource files, assuming this could be bbox etc that output above, just for info?)
		template.kml (the flight path file)
		waylines.wpml (can be generated by drone from template if missing, so not required)#
```

- The `wpmz` directory is required.
- The `res` subdirectory is not required, but can contain 'auxillary resource files', with the
  example given being 'pre-prepared reference target photos for the AI Spot-Check'.
- The `template.kml` file is required, containing the flight params.
  - [Detailed dev docs][4]
- The `waylines.wpml` is not mandatory and can be generated by the drone fro the template file.
  - Alternatively, the template file can be simplified and the waylines file provided manually.
  - The waylines file is the executable file of the drone for the flight path.
  - [Detailed dev docs][5]

## Tools

### QGIS Flight Planner

- This is a nice tool for flight planning, with lots of useful configuration options.
- Input:
	- Locally projected AOI in meters (important).
	- Camera defaults (there are presets, including Zenmuse P1 used in Mini 4 Pro).
	- Fight params, such as duration, angle, etc.
- Outputs:
	- Flight path (what we need)
	- Projection centers
	- Waypoints
	- Photo area BBOXes
- The generated flight_lines (flight paths) can be exported as KML, but this is not
  the exact format we need to be useful for Waypoint Features.

### [WayPointMap.com][6]

- A free service for generating flight plans, and outputting the required KMZ format!
- It's not open-source, but it works if we need to just get things done.
- It uses a fremium model, where some advanced flight planning features are restricted.
- An excellent source of info for the KML output files, for reverse engineering.
- Example of output template.kml: ![template.kml](./template.kml)
- Example of output waylines.wpml: ![waylines.wpml](./waylines.wpml)

## Example Workflow

Until Drone TM is operational and we add our own processing workflow, the following
is a workable flow to get these drones in use now.

### Create Flight Plan

- Go to [WayPointMap][6] and create an account.
- Draw an AOI with the 'draw a shape' tool.
  - Advanced: individual waypoints can be added for complex flights / controlling travel.
  - Advanced: POI (points of interest) can be added to generate a flight plan around a single 
    feature.
- Enter params in fields below the map.
- Click 'generate' & visualise the flight plan on the map.
- Click 'Download KMZ' to get the flight plan file.

### Add Flight Plan to Drone

- The flight plan is loaded onto the **remote** and synced with the drone.

> Note: if you are both using the DJI RC (or RC-N2) flight controller and a Mac device,
> you will need to install [Android file transfer][7] to access your RC's files.

- Plug your RC 2 into your computer and navigate to...


[1]: https://support.dji.com/help/content?customId=en-us03400007343&spaceId=34&re=US&lang=en&documentType=artical&paperDocType=paper "waypoints"

[2]: https://developer.dji.com/doc/cloud-api-tutorial/en/api-reference/dji-wpml/overview.html "kmz overview"

[3]: https://sdk-forum.dji.net/hc/en-us/articles/5975557266841-How-to-develop-waypoint-mission-What-are-the-format-standards-for-waypoint-mission-files "kmz high level"

[4]: https://developer.dji.com/doc/cloud-api-tutorial/en/api-reference/dji-wpml/template-kml.html "template.kml"

[5]: https://developer.dji.com/doc/cloud-api-tutorial/en/api-reference/dji-wpml/waylines-wpml.html "waylines.wpml"

[6]: https://www.waypointmap.com "waypointmap"

[7]: https://www.android.com/filetransfer/