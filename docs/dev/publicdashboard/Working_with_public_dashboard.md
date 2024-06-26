# Note on execution of public dashboard to launch the notebook
- First execute `mapping_dictionaries.ipynb` before launching any other notebooks.

# Test Scenario for Public Dashboard changes:
1. Front end testing
    - Make sure the dropdown menu have valid list of metrics.
    - Make sure the default charts are loaded at the first instance of loading the website.
    - Make sure all the charts are being launched or the error table is displayed.
2. Test with loading and unloading different program/study dataset. 
    - Load different datasets, run the scripts to execute the notebooks to make sure the charts are being generate properly.
    - Between datasets, drop the old dataset and load the new dataset.
    - Also check with empty MongoDB.
3. After the code has been merged, validate the changes on the staging environment.
    - Generate a snapshot of the charts from the staging website.
    - Request for the dataset for staging-program.
    - Validate the changes for both the charts and the Front end aspect.

# Different docker-compose yml files?
We have two docker-compose yml files, one specifically to aid in running in development mode. Another to run in production - alike mode.

1. About the `docker-compose.dev.yml` file:
 - This is useful if you want to launch the Jupyter notebook and execute the code block and explore output of each code blocks.

To launch docker-compose.dev.yml:
```
docker-compose -f docker-compose.dev.yml up
```

2. About the `docker-compose.yml` file:
 - This is useful if you want to execute the scripts to run all the required notebooks, without making any changes in the existing notebooks.
 - Note: We use cron jobs to execute the different notebooks in a scheduled way daily to generate the charts.

To launch docker-compose.yml:
```
docker-compose -f docker-compose.yml up
```

 Key difference amongst these two docker-compose file is the `CRON_MODE=TRUE` in case of `docker-compose.yml` while its disabled for `docker-compose.dev.yml`.

 - `docker-compose.yml`:
 ```
services:
    notebook-server:
        environment:
        - ....
        - CRON_MODE=
        - ....
```

- `docker-compose.dev.yml`
```
services:
    notebook-server:
        environment:
        - ....
        - CRON_MODE=TRUE
        - ....
```

 # Changes required in docker-compose.dev.yml and/or docker-compose.yml to load different dataset

The default `docker-compose.yml` with DB_HOST=db will load the dataset in MongoDB stored as `Stage_database`.

What if we want to load a different dataset than staging, and test for a different program/study?
Let us consider the example below: 
- (Note: This might need to updated once https://github.com/e-mission/e-mission-docs/issues/1048 issue is checked-in.)
```
services:
    notebook-server:
        environment:
        - DB_HOST=mongodb://db/openpath_prod_usaid_laos_ev
        - ....
        - STUDY_CONFIG=usaid-laos-ev
```
1. For testing different config: Change the STUDY_CONFIG, as `STUDY_CONFIG=<program/study type>`.
 Example: `STUDY_CONFIG=usaid-laos-ev` for the `usaid-laos-ev` study. List of other configs for study/program are accessible from: https://github.com/e-mission/nrel-openpath-deploy-configs/tree/main/configs
1. Similarly, change the `DB_HOST=<dataset>`.
Example: `DB_HOST=mongodb://db/openpath_prod_usaid_laos_ev` to load the dataset `openpath_prod_usaid_laos_ev` which has been loaded into the MongoDB.

# Execution of different public dashboard notebooks automatically (without launching Jupyter notebook)
---

``` 
$ docker-compose -f docker-compose.yml build
$ docker-compose -f docker-compose.yml  up 
$ docker ps
$ docker exec -it em-public-dashboard-notebook-server-1 /bin/bash
$ root@06f07def2c0e:/usr/src/app# source setup/activate.sh
$ (emission) root@06f07def2c0e:/usr/src/app# cd saved-notebooks
```
- Note: Make sure the `Year` and `Month` parameter for the executing notebook is not `None` while executing the `generate_plots.py` script. If it's set to None, it will not generate the charts.
Execution of notebooks
```
$ (emission) root@06f07def2c0e:/usr/src/app/saved-notebooks# PYTHONPATH=.. python bin/update_mappings.py mapping_dictionaries.ipynb
$ (emission) root@06f07def2c0e:/usr/src/app/saved-notebooks# PYTHONPATH=.. python bin/generate_plots.py generic_metrics.ipynb default
```
Refer to additional notebooks from https://github.com/e-mission/em-public-dashboard/blob/main/viz_scripts/docker/crontab

Then go to http://localhost:3274/

- If you want to test for specific configuration (program/study):
For example, if you want to test for usaid-laos-ev:
    http://localhost:3274/?study_config=usaid-laos-ev
and test.

# Launch and explore the database

- Through the Server code

```
$ docker-compose -f docker-compose.yml  up 
$ docker ps
$ docker exec -it em-public-dashboard-notebook-server-1 /bin/bash
$ cd emission
```

Use scripts and relevant usecases available here to access/update the database.

# Testing with staging 
- Load the snapshot of the dataset into MongoDB.
You may want to use `reset_snapshot_to_ts.py` to reset the timestamp of the dataset to a particular date/time.
- Execute different required notebooks as mentioned above.
- Load the http://localhost:3274/
- Access the staging website and validate changes against: https://openpath-stage.nrel.gov/public/

Test the dropdown of metrics using different `study_config` as show below for `ebikegj`
https://openpath-stage.nrel.gov/public/?study_config=ebikegj

# Existing issue with browser caching the old chart data
- While testing the changes, it's a good idea to clear up the cache and reload the webpage to make sure the latest charts are loaded.
- Always better to validate the changes via. the timestamp under the charts and the timestamp of generation of charts.