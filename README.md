# ListingStill parcel registry

The county parcel layers ListingStill's Lot lines module asks, as data. `parcel-sources.json` is the same file the app
ships with; the app fetches this copy once a day, validates it, and merges it over its bundled list — an entry here
replaces the bundled one with the same `id`, a new entry is appended. So a county added here reaches every copy of the
app the next day, with no release.

## Adding a county

One object in `sources`:

```json
{
  "id": "tx-frio",
  "name": "Frio County Appraisal District parcels",
  "county": "Frio", "state": "TX",
  "layerURL": "https://gisdata.pandai.com/pamaps02/rest/services/Frio/FrioCADPublic/MapServer/0",
  "idField": "FrioCADWeb.DBO.TaxParcels.Name",
  "addressField": null,
  "addressFields": ["FrioCADWeb.DBO.Accounts.Prop_Street_Number", "FrioCADWeb.DBO.Accounts.Prop_Street", "FrioCADWeb.DBO.Accounts.Prop_City"],
  "legalFields": ["FrioCADWeb.DBO.Accounts.Legal1", "FrioCADWeb.DBO.Accounts.Legal2"],
  "acreageField": "FrioCADWeb.DBO.TaxParcels.StatedArea",
  "bbox": { "minLat": 28.55, "maxLat": 29.10, "minLon": -99.42, "maxLon": -98.79 }
}
```

- `layerURL` is an ArcGIS REST layer (MapServer or FeatureServer, `/N`) with `Query` in its capabilities and polygon
  geometry, over https. Verify it live before adding: a point query must answer a parcel.
- `idField` is the parcel number as the county prints it on the listing. `addressField` is one situs field;
  `addressFields` several, joined with spaces. `acreageField` may carry a unit ("146.76 a"). `legalFields` are shown
  as the legal description. **Never an owner, mailing or value field.**
- `bbox` is the county's extent in WGS84; the app asks only the layers whose box contains the point.
- `version` at the top changes with every edit (`YYYY.MM.DD.n`).

The app refuses the whole file when any entry is not https, has no `idField`, or has an empty box, and keeps the
last good copy — so a mistake here cannot break a customer, it just does not land.

`registry.requestEmail` / `registry.requestURL` is where the app's "Request my county" button sends the county, the
state and a point on the listing.
