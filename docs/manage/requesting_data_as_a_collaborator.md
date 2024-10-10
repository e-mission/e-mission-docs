# Requesting & Using Data as a Collaborator
---
## Sourcing Data

The **Transportation Secure Data Center (TSDC)**  hosts data collected by OpenPATH during a variety of surveys.  This data can be used to replicate previous study findings, generate new visualizations, or simply to explore the platform's capabilites.  To request data from a specific program, please visit the TSDC [website](https://www.nrel.gov/transportation/secure-transportation-data/index.html).

## Working With Data  ##

After requesting data from TSDC, you should receive a "mongodump" file -- a collection of data, archived in `.tar.gz` format.  Here are the broad steps you need to take in order to work with this data:

1. **Start Docker**:  Ensure you have docker installed on your machine, and a `docker-compose.yml` file saved to your chosen repository.  The following command should start the development environment:
    ```bash
    $ docker-compose -f [example-docker-compose].yml up
    ```
    Example docker config files can be found in the server repository [here](https://github.com/e-mission/e-mission-server/blob/d2f38bc18d5c415888451e7ad98d40325a74c999/emission/integrationTests/docker-compose.yml#L4). The general construction of a compose file is as follows:

    ```yml
    version: "3"
    services:
    db:
        image: mongo:4.4.0
        volumes:
        - mongo-data:/data/db
        networks:
        - emission
        ports:
        - "27017:27017" # May change depending on repo

    networks:
    emission:

    volumes:
    mongo-data:
    ```
2. **Load your data**:  There are a few ways to go about this:
    - Certain repositories will have a `load_mongodump.sh` script.  Given the correct docker was started in the previous step, this should load all of the data for you.  
        - Depending on the data being analyzed, loading the entire mongodump may take a _very_ long time.  Ensure that docker's resources are properly increased, and ample time is set aside for the loading process.
    - If only a portion of data is needed, the mongodump may be unzipped, and its individual components loaded into the docker.
        - First, unpack your mongo dump file by running `tar -xvf [your_mongo_dump.tar.gz]`
        - Navigate to the unzipped folder.  Create a new directory, `./dump/Stage_database/`.  Copy your data files into this new directory.
        - Copy the new `./dump/Stage_database` directory into your Docker's `/tmp/` directory.  This can be done by dragging and dropping the directory into the Docker Desktop client, or done via the command line.
        - Using the following commands, connect to your docker image, 
            ```bash
            $ docker exec -it [your_docker_image_name] /bin/bash
            root@12345:/ cd tmp; mongorestore
            ```
        - More information on this approach can be found in the public dashboard [ReadMe](https://github.com/e-mission/em-public-dashboard/blob/main/README.md#large-dataset-workaround).


In general, it is best to follow the instructions of the repository you are working with. There are subtle differences between them, and these instructions are intended as general guidance only.

### Public Dashboard ###
This repository has several ipython notebooks that may be used to visualize raw data.  For detailed instructions on working with the dashboard, please consult the repository's [ReadMe](https://github.com/e-mission/em-public-dashboard/blob/main/README.md).

### Private Eval ###
Like the public dashboard, this repository contains several notebooks that may be used to process raw data.  These notebooks are designed to evaluated the efficacy of OpenPATH, test new algorithms, and provide some additional visualizations. Further details, including how to load data into this repository, may be found in the repository's [ReadMe](https://github.com/e-mission/e-mission-eval-private-data/blob/master/README.md)

---

## Internal Data Analysis ##

In the past, user-specific data was analyzed with scripts found in the [e-mission-server](https://github.com/e-mission/e-mission-server) repository.  This method of analysis is now reserved for internal debugging only.  In other words, **if you are an external collaborator, please use the methods detailed in the previous section!**   

### Install the server ###
Follow the [README](https://github.com/e-mission/e-mission-server) and install e-mission server locally on your own laptop.

### Load the data ###
Load the data into your local database. Since this data contains information from mutiple users, and you presumably want to retain the uuids, to correlate with other surveys that you might have performed, you should use the `load_multi_timeline_for_range.py` script. Since there are multiple files, the timeline will typically be a directory, and you should pass in the prefix. For example, if the user files are `all_users_sep_dec_2016/dump_0109c47b-e640-411e-8d19-e481c52d7130`, `all_users_sep_dec_2016/dump_026f8d13-4d7a-4f8f-8d35-0ec22b0f8f8b, ...,` you should run the following command line.

```
./e-mission-py.bash bin/debug/load_multi_timeline_for_range.py all_users_sep_dec_2016/dump_
```

#### Linked data ONLY ####
You can load the email <-> UUID mapping also into the database by using the `-m MAPFILE` option (see below). In this case, the mapping will be loaded into the `get_uuid_db()` similar to the regular server. If you don't specify the `-m` option, then the users will be loaded with dummy names - `user001`, `user002` ... - and you can manipulate the mapping json file directly to find out their email addresses.

```
./e-mission-py.bash bin/debug/load_multi_timeline_for_range.py -m all_users.json all_users_sep_dec_2016/dump_
```

### Run the pipeline ###
As discussed earlier, downloaded timelines typically contain analysis results, so there is no need to re-run the intake pipeline. The script will automatically detect this and print the following line.

```
timeline contains analysis results, no need to run the intake pipeline
```

If they do not contain analysis results, the script will print the line

```
all entries in the timeline contain only raw data, need to run the intake pipeline
```

In that case, you should [run the intake pipeline](https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-server/deploying_your_own_server_to_production.md#the-analysis-pipeline) (`bin/intake_multiprocess.py`) to get the processed analysis results.

### Clean up the loaded data ###
You can also remove the data by using `bin/purge_database_json.py`, which will delete everything, or `bin/debug/purge_multi_timeline_for_range.py`, which will only delete the entries in that timeline - e.g.

```
./e-mission-py.bash bin/debug/purge_multi_timeline_for_range.py all_users_sep_dec_2016
```

### Play with the Data ###
An example ipython notebook that shows data access parameters is at
https://github.com/e-mission/e-mission-server/blob/master/Timeseries_Sample.ipynb

It has examples on how to access raw data, processed data, and plot points.
Please use the timeseries interfaces as opposed to direct mongodb queries wherever possible.
That will make it easier to migrate to other, more scalable timeseries later.

---

## Final Notes ## 

For more information on how data is formatted, feel free to explore the [emission/core/wrapper/](https://github.com/e-mission/e-mission-server/tree/master/emission/core/wrapper) portion of the server repository.

Please contact @shankari if you have any further questions!