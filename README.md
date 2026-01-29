# Healthsites.io → GEO Knowledge Hub Upload

Upload global health facility data from [healthsites.io](https://healthsites.io) to the [GEO Knowledge Hub](https://gkhub.earthobservations.org).

## Data

The source data is a Shapefile export from healthsites.io containing worldwide health facility locations extracted from OpenStreetMap.

Two datasets:
- **World-node** — point locations of individual health facilities (OSM nodes)
- **World-way** — building footprints/outlines of health facilities (OSM ways)

Each dataset consists of 5 Shapefile component files (`.shp`, `.dbf`, `.shx`, `.prj`, `.cpg`). Total size is ~3.9 GB (the `.dbf` files are the largest).

The data is licensed under the [Open Database License (ODbL)](http://opendatacommons.org/licenses/odbl/) and must credit `© OpenStreetMap contributors`.

## GEO Knowledge Hub structure

The upload is structured as:
- **1 Knowledge Package** — "Global Health Facilities from Healthsites.io" (with DOI)
- **2 Knowledge Resources** (no DOI):
  1. World-node (point locations)
  2. World-way (building footprints)

## File layout

```
├── knowledge-package.json              # manifest for geo-package-loader
├── package/
│   └── metadata.json                   # package metadata (InvenioRDM schema)
├── resources/
│   ├── world-node-metadata.json        # resource 1 metadata
│   └── world-way-metadata.json         # resource 2 metadata
├── data/
│   ├── README.md                       # provenance info from healthsites.io
│   ├── LICENSE.txt                     # ODbL license text
│   ├── World-node.{shp,dbf,shx,prj,cpg}
│   └── World-way.{shp,dbf,shx,prj,cpg}
```

## Metadata format

The `knowledge-package.json` manifest tells `geo-package-loader` where to find everything:
- `knowledge_package.metadata_file` — path to the package metadata JSON
- `resources[].metadata_file` — path to each resource metadata JSON
- `resources[].files` — list of data files to upload for each resource

Each metadata JSON follows the [InvenioRDM record schema](https://inveniordm.docs.cern.ch/reference/rest_api_drafts_records/) with these key fields:
- `access` — `"public"` or `"restricted"`
- `metadata.title`, `metadata.creators`, `metadata.publication_date` — required
- `metadata.resource_type.id` — use `"knowledge"` for packages, `"dataset"` for resources
- `metadata.rights[].id` — license vocabulary ID (e.g. `"odbl-1.0"`)

To find valid vocabulary IDs, query the GKHub API:
- Licenses: `https://gkhub.earthobservations.org/api/vocabularies/licenses?q=<search>`
- Resource types: `https://gkhub.earthobservations.org/api/vocabularies/resourcetypes?q=<search>`

## How to upload

### 1. Set up Python environment

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install git+https://github.com/geo-knowledge-hub/geo-package-loader.git
```

### 2. Get a personal access token

Create one at: https://gkhub.earthobservations.org/account/settings/applications/

### 3. Run the upload

```bash
export GKH_PERSONAL_ACCESS_TOKEN="your-token-here"

geo-package-loader load \
  -k /path/to/this/repo \
  -p https://gkhub.earthobservations.org/api/packages \
  -r https://gkhub.earthobservations.org/api/records \
  -t $GKH_PERSONAL_ACCESS_TOKEN
```

Add `-ph` to publish immediately (otherwise it stays as a draft).

The upload will take a while due to the large file sizes (~3.9 GB total).

## References

- [GEO Knowledge Hub REST API docs](https://gkhub.earthobservations.org/doc/reference/rest-api/)
- [InvenioRDM REST API reference](https://inveniordm.docs.cern.ch/reference/rest_api_index/)
- [geo-package-loader](https://github.com/geo-knowledge-hub/geo-package-loader)
- [healthsites.io](https://healthsites.io)
