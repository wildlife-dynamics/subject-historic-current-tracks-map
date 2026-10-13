# subject-historic-current-comparison

subject-historic-current-comparison

## What it does

| Step | What it does |
|---|---|
| **Set Workflow Details** | Add information that will help to differentiate this workflow from another. |
| **Set Groupers** |  |
| **Trajectory Segment Filter** | Build a TrajectorySegmentFilter with the project's default thresholds. |
| **BaseMap URLs** |  |
| **Set Time Range** | Choose the period of time to analyze. |
| **Connect to EarthRanger** |  |
| **Subject Group** |  |
| **Current Subject Group Observations** | Get observations for a subject group from EarthRanger. |
| **Previous Period** | Compute a comparison period ending at timerange.since (i.e. immediately preceding, and not overlapping with, the current period), with the start determined by one of three modes:   - Custom: manually specify years/months/weeks/days/hours/minutes/seconds   - Preset: pick a named offset (same period, 1/3/6 months, 1 year back)   - Calendar: pick an exact since date from a date picker |
| **Process Current Relocations** |  |
| **Previous Subject Group Observations** | Get observations for a subject group from EarthRanger. |
| **Current Relocations to Trajectories** |  |
| **Process Previous Relocations** |  |
| **Persist Previous Relocations** | Persist dataframe to a file or cloud storage object. |
| **Persist Current Relocations** | Persist dataframe to a file or cloud storage object. |
| **Map Columns** | Maps and transforms the columns of a DataFrame based on the provided parameters. The order of the operations is as follows: drop columns, retain/reorder columns, and rename columns. |
| **Previous Relocations to Trajectories** |  |
| **Persist Current Trajectories** | Persist dataframe to a file or cloud storage object. |
| **Current Duration Status** | Create a new column in the DataFrame with a default value if it doesn't already exist. |
| **Map Columns** | Maps and transforms the columns of a DataFrame based on the provided parameters. The order of the operations is as follows: drop columns, retain/reorder columns, and rename columns. |
| **Persist Previous Trajectories** | Persist dataframe to a file or cloud storage object. |
| **Previous Duration Status** | Create a new column in the DataFrame with a default value if it doesn't already exist. |
| **Concatenate Current and Previous Dataframes** | Merge multiple dataframes into a single dataframe. |
| **Add Temporal Index** |  |
| **Split Groups** |  |
| **Add Status Color Columns** | Assign hex and RGBA colors for a status column (current vs. everything else). |
| **Sort Values by Duration Status** |  |
| **Subset Columns** | Return a DataFrame restricted to a subset of columns. |
| **Create Path Layer** | Creates a polyline layer definition based on the provided configuration. |
| **Compute View State From Gdf** |  |
| **Groupbykey** |  |
| **Draw Current Previous Map** | Creates a map based on the provided layer definitions and configuration. |
| **Persist map as HTML** | Persist text to a file or cloud storage object. |
| **Create Map Widget Single View** | Create a map widget with a single view. |
| **Gather Dashboard** |  |

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

### Set Time Range

| Field | Description | Default |
|---|---|---|
| Since *(required)* | The start time | — |
| Until *(required)* | The end time | — |
| Timezone |  | — |

### Connect to EarthRanger

| Field | Description | Default |
|---|---|---|
| Data source *(required)* | Select one of your configured data sources. | — |

### Subject Group

| Field | Description | Default |
|---|---|---|
| Var *(required)* |  | — |

---

## Developing

```bash
# recompile spec.yaml (first time: --install; after that: --update, which bumps VERSION.yaml)
./dev/recompile.sh --update

# run the test cases in test-cases.yaml
pixi run --manifest-path pixi.toml -e default bash dev/pytest-cli.sh subject_historic_current_comparison --all
```

Documentation: [docs/index.html](docs/index.html).
