# subject-historic-current-comparison

Maps each subject in an EarthRanger subject group with its tracks from the chosen time range on top of its tracks from a comparison period just before it. By default, current tracks are drawn in the subject's EarthRanger colour on top of grey comparison tracks; both colours and which period is on top can be changed on the form. There is one map per subject.

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
| **Comparison Period** | The earlier period whose tracks are drawn as comparison tracks. It ends where the current time range starts and, by default, is the same length. |
| **Process Current Relocations** | Cleans the current period's fixes and drops known bad coordinates. |
| **Previous Subject Group Observations** | Get observations for a subject group from EarthRanger. |
| **Current Relocations to Trajectories** | Joins the current fixes into track segments, using the Trajectory Segment Filter settings. |
| **Process Previous Relocations** | Cleans the previous period's fixes and drops known bad coordinates. |
| **Persist Previous Relocations** | Persist dataframe to a file or cloud storage object. |
| **Persist Current Relocations** | Persist dataframe to a file or cloud storage object. |
| **Map Columns** | Maps and transforms the columns of a DataFrame based on the provided parameters. The order of the operations is as follows: drop columns, retain/reorder columns, and rename columns. |
| **Previous Relocations to Trajectories** | Joins the previous fixes into track segments, using the same Trajectory Segment Filter settings as the current tracks. |
| **Persist Current Trajectories** | Persist dataframe to a file or cloud storage object. |
| **Current Duration Status** | Create a new column in the DataFrame with a default value if it doesn't already exist. |
| **Map Columns** | Maps and transforms the columns of a DataFrame based on the provided parameters. The order of the operations is as follows: drop columns, retain/reorder columns, and rename columns. |
| **Persist Previous Trajectories** | Persist dataframe to a file or cloud storage object. |
| **Previous Duration Status** | Create a new column in the DataFrame with a default value if it doesn't already exist. |
| **Concatenate Current and Previous Dataframes** | Merge multiple dataframes into a single dataframe. |
| **Add Temporal Index** | Indexes the tracks by segment start time. |
| **Split Groups** | One table of tracks per subject. |
| **Track Colors** *(Track Order and Colors)* | Colours the tracks: comparison tracks in the Comparison Tracks Color; current tracks in each subject's EarthRanger colour, or in the Current Tracks Color if that's turned off. |
| **Track Order** *(Track Order and Colors)* | Orders the rows so current tracks are drawn on top of comparison tracks, or comparison tracks on top when **Current tracks on top** is unticked. |
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

Applies to both the current and the historic tracks.

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
| Subject Group Name *(required)* | The EarthRanger subject group to map, picked from a drop-down of the subject groups on the data source chosen under **Connect to EarthRanger**. | — |

### BaseMap URLs

| Field | Description | Default |
|---|---|---|
| Base maps | Tile layers to draw under the tracks. The first layer in the list is the bottommost. | Studio's default layers |

### Comparison Period

| Field | Description | Default |
|---|---|---|
| Comparison Period Length *(required)* | How far back the comparison period starts. It always ends where the current time range starts. **Preset**: *Same as current period*, *1 month back*, *3 months back*, *6 months back* or *1 year back*. **Custom**: any number of years, months, weeks, days, hours, minutes and seconds. **Calendar**: an exact start date. | Preset, `Same as current period` |

### Track Order and Colors

| Field | Description | Default |
|---|---|---|
| Comparison Tracks Color *(required)* | Colour of the tracks from the comparison period. | `#a9a9a9` (grey) |
| Use subject colors for current tracks | Colour each subject's current tracks with its EarthRanger colour. Untick to use the Current Tracks Color for every subject. | ticked |
| Current Tracks Color | Colour of the current tracks when **Use subject colors for current tracks** is unticked. | `#6495ed` (blue) |
| Current tracks on top | Draw current tracks over comparison tracks where they overlap. Untick to draw comparison tracks on top. | ticked |

---

## Developing

The run form is set in `spec.yaml`:

- `rjsf-overrides` turns the **Subject Group** field (*Var*) into a *Subject Group Name* drop-down: the
  `EarthRangerEnumResolver` transform fills it with the subject groups of the data source picked under
  **Connect to EarthRanger** (`depends_on: properties.er_connection.properties.data_source.properties.name`).
  Inside a task group the override key is `<group title>.properties.<step id>`, so it is
  `Subject Group.properties.subject_group_name.properties.var`.
- `rjsf-overrides` also titles and describes the **Comparison Period** step and defaults its length to the
  *Same as current period* preset: `previous_time_range.properties.offset.default` picks the Preset option,
  and `$defs: PresetPreviousPeriod.properties.preset.default` sets the preset inside it.
- `rjsf-overrides` turns the **Track Order and Colors** fields into colour pickers (`format: color`) and a
  checkbox, with their titles and defaults. The two steps are in one task group, so the keys are
  `Track Order and Colors.properties.status_color_columns.properties.<field>` and
  `Track Order and Colors.properties.sort_values_by_status.properties.ascending`.
- Rows draw in order, so the label sorted last ends up on top: *Comparison Tracks* sorts before
  *Current Tracks*, so `ascending: true` puts current tracks on top. That's also `sort_values`' own default
  when a run leaves it out. `previous_color_hex` has no default, so `test-cases.yaml` sets it.
- **Trajectory Segment Filter**, **BaseMap URLs** and **Comparison Period** have no fixed values in the spec,
  so they're on the form for each run.
- Both periods are joined into tracks with the **Trajectory Segment Filter** settings
  (`${{ workflow.segment_filter.return }}`), so they're filtered the same way.

```bash
# recompile spec.yaml (first time: --install; after that: --update, which bumps VERSION.yaml)
./dev/recompile.sh --update

# run the test cases in test-cases.yaml
pixi run --manifest-path pixi.toml -e default bash dev/pytest-cli.sh subject_historic_current_comparison --all
```

Documentation: [docs/index.html](docs/index.html).
