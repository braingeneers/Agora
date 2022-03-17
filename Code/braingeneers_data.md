# Organizing Data
*This wiki describes how data is stored and organized across different types of experiments.*

Data is stores in batches of experiments.

A **batch** is a logical collection of experiments that belong to the same investigation.

A batch can contain multiple **experiment** files.

## UUID
Each batch has a unique identifier (**UUID**) formatted as:

    YYYY-MM-DD-[eficp]-[descriptor]

Middle letter [eficp] is type of experiment:

        ephys (e), fluidics (f), imaging (i), calcium (a), patch clamp (p), widefield fluorescence (w), confocal (o)

The descriptor is a unique string (without spaces) describing the experiment. Example:

        2020-10-06-e-UCSF-neurons-axionplate

## Metadata JSON File

Each batch of experiments has a **metadata.json** file which describes overall contents and notes for the investigation.

[Link to latest metadata.json template](templates/metadata.json)

       {
        "experiments": [
            "experimen1.json",
            "experimen2.json",
            "experimen2.json"],
        "issue": "https://github.com/braingeneers/internal/issues/#",
        "notes": " ",
        "timestamp": "yyyy-mm-ddThh:mm:ss",
        "uuid": "YYYY-MM-DD-[eficp]-[descriptor]"
       }

**experiments**: list all experiments part of the batch, links to JSON file for each experiment (see section: **Experiment JSON File**)

**issue**: corresponding GitHub issue for the batch

**notes**: inline text or link to external document with notes/overacring procedures for the whole batch of experiments

**timestamp**: date when first experiment in the batch was started, example: `2020-10-06T18:22:35`

**uuid**: UUID for the batch of experiments



## Experiment JSON File
May be different for differt data types.

Example for electrophysiology:

[Link to latest ephys-experiment.json template](templates/ephys-experiment.json)

    {
        "blocks": [
            {
                "num_frames": 1200240,
                "path": "6well-intancontroller_201006_182018.bin",
                "source": "./original/2020-10-06-e-UCSF-neurons-axionplate/6well-intancontroller_201006_182018.rhd",
                "timestamp": "2020-10-06T18:20:18"
            },
            {
                "num_frames": 1200240,
                "path": "6well-intancontroller_201006_182124.bin",
                "source": "./original/2020-10-06-e-UCSF-neurons-axionplate/6well-intancontroller_201006_182124.rhd",
                "timestamp": "2020-10-06T18:21:24"
            }
        ],
        "channels": [],
        "hardware": "Intan RHD 2000 Controller",
        "name": "6well-intancontroller",
        "notes": "",
        "num_channels": 32,
        "num_current_input_channels": 0,
        "num_voltage_channels": 32,
        "offset": 0,
        "sample_rate": 20000,
        "voltage_scaling_factor": 0.195,
        "timestamp": "2020-10-06T18:20:18",
        "units": "\u00b5V",
        "version": "0.0.1"
    }

Example for fluidics:

    {
    }

Example for imaging:

    {
    }


Example for calcium:

    {
    }

Example for patch:

    {
    }

## GitHub Issue
Every experiment batch has a corresponding GitHub issue in `braingeneers/internal/issues/` to announce the dataset and manage discussion.
[link](https://github.com/braingeneers/internal/issues)

## Storage Hierarchy in S3


    braingeneers/
          ├── ephys/
          │      └── YYYY-MM-DD-e-[descriptor]/
          │                 ├── metadata.json
          │                 ├── original/
          │                 │       ├── experiment1.json
          │                 │       ├── experiment2.json
          │                 │       └── data/
          │                 │             ├── experiment1-file1.bin
          │                 │             ├── experiment2-file1.bin
          |                 |             └── experiment2-fileN.bin
          │                 └── derived/
          │                        ├── spikesort.json
          │                        └── spikesort/
          │                               └── spikesort_experiment1.bin
          │
          ├── fluidics/
          │
          ├── imaging/
          │
          ├── calcium/         
          │
          └── patchclamp/          


## Searching Data

- Rule: metadata JSON sits outside the folder it describes
- Tags in metadata?
    - Data types (orginal, spike sorted?)
- Each feature/module should have its own JSON file and folder with output
    - when a process is done it produces a JSON to announce completion
    - user can scrape process JSONs without going into its output folder

## NOBACKUP
- A NOBACKUP file can be added to a path in order to ensure that whenever data is being backed up, that specific file path will be excluded.
- For example, using the storage heirarchy above, if we add a `NOBACKUP` file as shown below:

    ```
    braingeneers/
          ├── ephys/
          │      └── 2021-06-28-e-test6/
          │                 ├── metadata.json
          │                 ├── NOBACKUP
          │                 ├── etc../
     ```

    - Then anything with the prefix `s3://braingeneers/ephys/2021-06-28-e-test6/` will be excluded from any backup we perform.
- Please note the backups do cost money.
