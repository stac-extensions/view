# View Geometry Extension Specification

- **Title:** View Geometry
- **Identifier:** <https://stac-extensions.github.io/view/v1.0.0/schema.json>
- **Field Name Prefix:** view
- **Scope:** Item, Collection
- **Extension [Maturity Classification](https://github.com/radiantearth/stac-spec/tree/master/extensions.md#extension-maturity):** Stable
- **Owner**: @matthewhanson
- **History:** [Prior to March 30, 2021](https://github.com/radiantearth/stac-spec/commits/v1.0.0-rc.2/extensions/view)

This document explains the View Geometry Extension to the
[SpatioTemporal Asset Catalog](https://github.com/radiantearth/stac-spec) (STAC) specification.

View Geometry adds metadata related to 
angles of sensors and other radiance angles that affect the view of resulting data. It will often be combined with other 
extensions that describe the actual data, such as the `eo`, `sat` or `sar` extensions.

- [Example](examples/item.json)
- [JSON Schema](json-schema/schema.json)
- [Changelog](./CHANGELOG.md)

## Fields

The fields in the table below can be used in these parts of STAC documents:

- [ ] Catalogs
- [ ] Collections
- [x] Item Properties (incl. Summaries in Collections)
- [x] Assets (for both Collections and Items, incl. Item Asset Definitions in Collections)
- [ ] Links

| Field Name           | Type   | Description |
| -------------------- | ------ | ----------- |
| view:off_nadir       | number | The angle from the sensor between nadir (straight down) and the scene center. Measured in degrees (0-90). |
| view:incidence_angle | number | The incidence angle is the angle between the vertical (normal) to the intercepting surface and the line of sight back to the satellite at the scene center. Measured in degrees (0-90). |
| view:azimuth         | number | Viewing azimuth angle. The angle measured from the sub-satellite point (point on the ground below the platform) between the scene center and true north. Measured clockwise from north in degrees (0-360). |
| view:sun_azimuth     | number | Sun azimuth angle. From the scene center point on the ground, this is the angle between truth north and the sun. Measured clockwise in degrees (0-360). |
| view:sun_elevation   | number | Sun elevation angle. The angle from the tangent of the scene center point to the sun. Measured from the horizon in degrees (`-90`-`90`). Negative values indicate the sun is below the horizon, e.g. sun elevation of -10° means the data was captured during [nautical twilight](https://www.timeanddate.com/astronomy/different-types-twilight.html). |

The angles `off_nadir`, `incidence_angle`, and `sun_elevation` are angles measured on a 2d plane formed: sensor location, 
sub-sensor point on the earth, the sun, and the center of the viewed area.

The off-nadir angle and the incidence angle are related. When the off-nadir angle is low (high incidence angle) then the 
two angles sum to about 90, so one can be calculated from the other. However, at high off-nadir angles with high altitude 
sensors the curvature of the earth has an impact and their sum will be less than 90. If only providing one of the two angles, 
the off-nadir angle is preferred.

The angles `azimuth` and `sun_azimuth` indicate the position of the viewed scene and the sun by the angle from true north, as shown below.

Example:

```js
{
  "stac_version": "1.1.0",
  "stac_extensions": [
    "https://stac-extensions.github.io/view/v1.0.0/schema.json"
  ],
  "id": "20171110",
  "type": "Feature",
  ...
  "properties": {
    "platform": "mysatellite",
    "instruments": ["mycamera1", "mycamera2"],
    "constellation": "allmysatellites",
    "view:off_nadir": 0,
    "view:incidence_angle": 90,
    "view:azimuth": 23.9,
    "view:sun_elevation": 45.0,
    "view:sun_azimuth": 56.4
  }
}
```

## Asset Roles

One of the best practices is to use [Asset Roles](https://github.com/radiantearth/stac-spec/tree/master/item-spec/item-spec.md#asset-roles)
to provide clients with more information about the assets in an item. The following list includes a shared vocabulary for some common EO assets. 
This list should not be considered definitive, and implementors are welcome to use other asset roles. If consensus and tooling consolidates around
these role names then they will be specified in the future as more standard than just 'best practices'. The roles listed below
all tend to be additional files that contain specific values for every single pixel. It is recommended to use them all with the role of 'metadata'.

| Role Name            | Description |
| -------------------- | ----------- |
| incidence-angle      | Points to a file with per-pixel incidence angles. |
| azimuth              | Points to a file with per-pixel azimuth angles. |
| sun-azimuth          | Points to a file with per-pixel sun azimuth angles. |
| sun-elevation        | Points to a file with per-pixel sun elevation angles. |
| terrain-shadow       | Points to a file that indicates whether a pixel is not directly illuminated due to terrain shadowing. |
| terrain-occlusion    | Points to a file that indicates whether a pixel is not visible to the sensor due to terrain occlusion during off-nadir viewing. |
| terrain-illumination | Points to a file with coefficients used for terrain illumination correction are provided for each pixel. |

## Contributing

All contributions are subject to the
[STAC Specification Code of Conduct](https://github.com/radiantearth/stac-spec/blob/master/CODE_OF_CONDUCT.md).
For contributions, please follow the
[STAC specification contributing guide](https://github.com/radiantearth/stac-spec/blob/master/CONTRIBUTING.md) Instructions
for running tests are copied here for convenience.

### Running tests

The same checks that run as checks on PR's are part of the repository and can be run locally to verify that changes are valid. 
To run tests locally, you'll need `npm`, which is a standard part of any [node.js installation](https://nodejs.org/en/download/).

First you'll need to install everything with npm once. Just navigate to the root of this repository and on 
your command line run:
```bash
npm install
```

Then to check markdown formatting and test the examples against the JSON schema, you can run:
```bash
npm test
```

This will spit out the same texts that you see online, and you can then go and fix your markdown or examples.

If the tests reveal formatting problems with the examples, you can fix them with:
```bash
npm run format-examples
```
