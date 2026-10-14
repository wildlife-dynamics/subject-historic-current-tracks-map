# subject-historic-current-comparison

Maps each subject in an EarthRanger subject group with its tracks from the chosen time range on top of its tracks from a comparison period just before it. Current tracks are drawn in the subject's EarthRanger colour, previous tracks in grey, with one map per subject.

## What it does

| Step | What it does |
|---|---|
| **Set Workflow Details** | Add information that will help to differentiate this workflow from another. |
| **Set Groupers** | Splits the results by subject, so the dashboard has one map per subject. |
| **Connect to EarthRanger** | The EarthRanger site to read from. |
| **Set Time Range** | Choose the period of time to analyze. |
| **Trajectory Segment Filter** | Build a TrajectorySegmentFilter with the project's default thresholds. |
| **Subject Group** | The subject group to map. |
| **BaseMap URLs** | Tile layers drawn under the tracks. |
| **Current Subject Group Observations** | Get observations for a subject group from EarthRanger. |
| **Previous Period** | Compute a comparison period ending at timerange.since (i.e. immediately preceding, and not overlapping with, the current period), with the start determined by one of three modes:   - Custom: manually specify years/months/weeks/days/hours/minutes/seconds   - Preset: pick a named offset (same period, 1/3/6 months, 1 year back)   - Calendar: pick an exact since date from a date picker |
| **Process Current Relocations** | Cleans the current period's fixes and drops known bad coordinates. |
| **Previous Subject Group Observations** | Get observations for a subject group from EarthRanger. |
| **Current Relocations to Trajectories** | Joins the current fixes into track segments, using the Trajectory Segment Filter settings. |
| **Process Previous Relocations** | Cleans the previous period's fixes and drops known bad coordinates. |
| **Persist Previous Relocations** | Persist dataframe to a file or cloud storage object. |
| **Persist Current Relocations** | Persist dataframe to a file or cloud storage object. |
| **Map Columns** | Maps and transforms the columns of a DataFrame based on the provided parameters. The order of the operations is as follows: drop columns, retain/reorder columns, and rename columns. |
| **Previous Relocations to Trajectories** | Joins the previous fixes into track segments, using fixed limits (up to 100 km and 500 km/h per segment). |
| **Persist Current Trajectories** | Persist dataframe to a file or cloud storage object. |
| **Current Duration Status** | Create a new column in the DataFrame with a default value if it doesn't already exist. |
| **Map Columns** | Maps and transforms the columns of a DataFrame based on the provided parameters. The order of the operations is as follows: drop columns, retain/reorder columns, and rename columns. |
| **Persist Previous Trajectories** | Persist dataframe to a file or cloud storage object. |
| **Previous Duration Status** | Create a new column in the DataFrame with a default value if it doesn't already exist. |
| **Concatenate Current and Previous Dataframes** | Merge multiple dataframes into a single dataframe. |
| **Add Temporal Index** | Indexes the tracks by segment start time. |
| **Split Groups** | One table of tracks per subject. |
| **Add Status Color Columns** | Assign hex and RGBA colors for a status column (current vs. everything else). |
| **Sort Values by Duration Status** | Orders rows so current tracks are drawn on top of previous ones. |
| **Subset Columns** | Return a DataFrame restricted to a subset of columns. |
| **Create Path Layer** | Creates a polyline layer definition based on the provided configuration. |
| **Compute View State From Gdf** | Zooms each map to its subject's tracks. |
| **Groupbykey** | Pairs each subject's path layer with its view state. |
| **Draw Current Previous Map** | Creates a map based on the provided layer definitions and configuration. |
| **Persist map as HTML** | Persist text to a file or cloud storage object. |
| **Create Map Widget Single View** | Create a map widget with a single view. |
| **Merge Widget Views** | Combines the per-subject maps into one Movement Tracks widget, so the subject filter switches between them. |
| **Gather Dashboard** | Collects the map widgets into the dashboard. |

## Requirements

- Access to a **EarthRanger** data source, set up as a connection

---

## 1. Load the Workflow

In the workflow runner, add a new template using this repository's URL. Once added, select
**subject-historic-current-comparison** from the available templates list to load it.

---

## 2. Configure the Workflow

You'll be prompted to fill in the following parameters before running.

### Set Workflow Details

| Field | Description | Default |
|---|---|---|
| Workflow Name *(required)* |  | — |
| Workflow Description |  | — |

### Connect to EarthRanger

| Field | Description | Default |
|---|---|---|
| Data source *(required)* | Select one of your configured data sources. | — |

### Set Time Range

| Field | Description | Default |
|---|---|---|
| Since *(required)* | The start of the current period. | — |
| Until *(required)* | The end of the current period. | — |
| Timezone |  | — |

### Trajectory Segment Filter

| Field | Description | Default |
|---|---|---|
| Min Length Meters | Minimum segment length in meters. Advanced. | `0.001` |
| Max Length Meters | Maximum segment length in meters. Advanced. | `5000` |
| Min Time Secs | Minimum segment duration in seconds. Advanced. | `1` |
| Max Time Secs | Maximum segment duration in seconds. Advanced. | `21600` |
| Min Speed Kmhr | Minimum segment speed in km/h. Advanced. | `0.01` |
| Max Speed Kmhr | Maximum segment speed in km/h. Advanced. | `9.0` |

### Subject Group

| Field | Description | Default |
|---|---|---|
| Subject Group Name *(required)* | Enter the subject group name exactly as it appears in EarthRanger (case-sensitive). | `Elephants` |

### BaseMap URLs

| Field | Description | Default |
|---|---|---|
| Base maps | Tile layers to draw under the tracks. The first layer in the list is the bottommost. | Studio's default layers |

### Previous Period

| Field | Description | Default |
|---|---|---|
| Offset *(required)* | How to set the start of the comparison period. It always ends where the current period starts. **Preset**: *Same as current period*, *1 month back*, *3 months back*, *6 months back* or *1 year back*. **Custom**: any number of years, months, weeks, days, hours, minutes and seconds. **Calendar**: an exact start date. | Preset, `1 month back` |

---

## Developing

The run form is set in `spec.yaml`:

- `rjsf-overrides` relabels the **Subject Group** field from *Var* to *Subject Group Name*, defaults it to
  `Elephants` and says the name must match EarthRanger exactly. Inside a task group the override key is
  `<group title>.properties.<step id>`, so it is `Subject Group.properties.subject_group_name...`.
- **Trajectory Segment Filter**, **BaseMap URLs** and **Previous Period** have no fixed values in the spec,
  so they're on the form for each run.
- The current tracks use the **Trajectory Segment Filter** settings, but the previous tracks use fixed limits
  in **Previous Relocations to Trajectories** (up to 100 km and 500 km/h per segment). Keep them in step if
  the two periods should be filtered the same way.

```bash
# recompile spec.yaml (first time: --install; after that: --update, which bumps VERSION.yaml)
./dev/recompile.sh --update

# run the test cases in test-cases.yaml
pixi run --manifest-path pixi.toml -e default bash dev/pytest-cli.sh subject_historic_current_comparison --all
```

Documentation: [docs/index.html](docs/index.html).
