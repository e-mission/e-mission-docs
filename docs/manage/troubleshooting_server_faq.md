# Troubleshooting Tips (FAQ)
---

## Tools to use ##
The main tools that you will want to use for troubleshooting are:
- the logs that you configured in `conf/log` when you set up the server
- exploration of the collected data

You can use standard unix tools (`grep`, `less`, ...) to explore the logs - some particular logs to look for in particular scenarios are detailed below.

You can start exploring the data by running the sample python code in the sample (`[Timeseries_Sample.ipynb](https://github.com/e-mission/e-mission-server/blob/master/Timeseries_Sample.ipynb)`).

You can run the sample code from a terminal by using the ipython console (`e-mission-server$ ./e-mission-ipy.bash`) and copy-pasting the commands.

Alternatively, you can start a notebook server and run the notebook directly. Instructions for starting a notebook server on a remote host are not e-mission specific - but if you want an example, [this is the configuration](https://github.com/e-mission/e-mission-server/wiki/Multi-layer-stack#notebook-server) that I use in the reference server.

## Troubleshooting time! ##

### I am getting a 500 error ###

A [500 error code](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes#5xx_Server_errors) means that there was an exception thrown by the server code. Exceptions are typically printed to the console, so
- if you started the server directly, they will be visible in the screen where you started the server, and
- if you started the server [using supervisord](https://github.com/e-mission/e-mission-server/wiki/Deploying-your-own-server-to-production#the-webserver), they will be in the `stdout_logfile` (e.g. `/mnt/logs/emission/emissionpy.out.log` in the example)

### Is the data getting to the server correctly? ###

All background communication to the server happens using the usercache, we pull
data periodically using `/usercache/get` and push data periodically using
`/usercache/put`. We only push data if there is any to push, so
`/usercache/put` calls are much less frequent than `/usercache/get` calls.

#### Anatomy of a successful `/usercache/put` call ####
If you don't see all these steps, the push failed, and it failed at the first missing/incomplete step

##### on standard output ####

On standard output, you will see the `START` and `END` messages for the calls.
Note that the calls are in parallel since we support multi-threading through
cherrypy.

```
START 2017-10-09 13:50:46.520373 POST /usercache/put
START 2017-10-09 13:50:46.545886 POST /usercache/get
END 2017-10-09 13:50:46.581072 POST /usercache/get 236128e8-8c5e-405f-a4f9-1719bf181007 0.0348310470581
END 2017-10-09 13:50:47.154538 POST /usercache/put 236128e8-8c5e-405f-a4f9-1719bf181007 0.634107112885
```

Then, the webserver log has the start of the call

```
2017-10-08 17:45:57,927:DEBUG:139922601957120:START POST /usercache/put
2017-10-08 17:45:57,928:DEBUG:139922601957120:Called userCache.put
```

The mapping from the email to the uuid

```
2017-10-08 17:45:57,979:DEBUG:139922601957120:Found user email shankari@berkeley.edu
2017-10-08 17:45:57,979:DEBUG:139922601957120:user_uuid ea59084e-11d4-4076-9252-3b9a29ce35e0
```

And then the actual entries. The examples here are for the `config/consent`,
`stats/client_nav_event` and `background/battery` keys. Note that we haven't
seen location entries yet.

```
2017-10-08 17:45:58,092:DEBUG:139922601957120:Updated result for user = ea59084e-11d4-4076-9252-3b9a29ce35e0, key = config/consent, write_ts = 1499293551.84 = {'updatedExisting': True, u'nModified': 0, u'ok': 1, u'n': 1}
2017-10-08 17:45:58,105:DEBUG:139922601957120:Updated result for user = ea59084e-11d4-4076-9252-3b9a29ce35e0, key = stats/client_nav_event, write_ts = 1507410331.98 = {'updatedExisting': False, u'nModified': 0, u'ok': 1, u'upserted': ObjectId('59da6456cb17471ac0bc8ef7'), u'n': 1}
2017-10-08 17:45:58,112:DEBUG:139922601957120:Updated result for user = ea59084e-11d4-4076-9252-3b9a29ce35e0, key = background/battery, write_ts = 1507410332.0 = {'updatedExisting': False, u'nModified': 0, u'ok': 1, u'upserted': ObjectId('59da6456cb17471ac0bc8ef8'), u'n': 1}
```

Finally, here are some `background/location`, `background/filtered_location`
and `background/motion_activity` entries.

```
2017-10-08 17:45:59,146:DEBUG:139922601957120:Updated result for user = ea59084e-11d4-40 76-9252-3b9a29ce35e0, key = background/location, write_ts = 1507481639.3 = {'updatedExisting': False, u'nModified': 0, u'ok': 1, u'upserted': ObjectId('59da6457cb17471ac0bc8f48 '), u'n': 1}
2017-10-08 17:45:59,150:DEBUG:139922601957120:Updated result for user = ea59084e-11d4-4076-9252-3b9a29ce35e0, key = background/motion_activity, write_ts = 1507481639.39 = {'updatedExisting': False, u'nModified': 0, u'ok': 1, u'upserted': ObjectId('59da6457cb17471ac0bc8f49'), u'n': 1}
2017-10-08 17:45:59,152:DEBUG:139922601957120:Updated result for user = ea59084e-11d4-4076-9252-3b9a29ce35e0, key = background/filtered_location, write_ts = 1507481639.47 = {'updatedExisting': False, u'nModified': 0, u'ok': 1, u'upserted': ObjectId('59da6457cb17471ac0bc8f4a'), u'n': 1}
2017-10-08 17:45:59,155:DEBUG:139922601957120:Updated result for user = ea59084e-11d4-4076-9252-3b9a29ce35e0, key = background/motion_activity, write_ts = 1507481639.48 = {'updatedExisting': False, u'nModified': 0, u'ok': 1, u'upserted': ObjectId('59da6457cb17471ac0bc8f4b'), u'n': 1}
2017-10-08 17:45:59,157:DEBUG:139922601957120:Updated result for user = ea59084e-11d4-4076-9252-3b9a29ce35e0, key = background/location, write_ts = 1507481644.6 = {'updatedExisting': False, u'nModified': 0, u'ok': 1, u'upserted': ObjectId('59da6457cb17471ac0bc8f4c'), u'n': 1}
```

After a bunch of more such entries, the call ends

```
2017-10-08 17:46:02,400:DEBUG:139922601957120:Updated result for user = ea59084e-11d4-4076-9252-3b9a29ce35e0, key = statemachine/transition, write_ts = 1507483297.71 = {'updatedExisting': False, u'nModified': 0, u'ok': 1, u'upserted': ObjectId('59da645acb17471ac0bc903c'), u'n': 1}
2017-10-08 17:46:02,401:DEBUG:139922601957120:END POST /usercache/put ea59084e-11d4-4076-9252-3b9a29ce35e0 6.060338974
```


#### Confirm that the entries are really in the database ####

The incoming entries are initially put into the usercache and then copied to
the timeseries database as part of the intake pipeline. In order to check both
those locations, you can use the `cache_series` data structure either through
the ipython command line or through ipython notebook.

```
$ ~/e-mission-server$ ./e-mission-ipy.bash
Python 2.7.13 |Anaconda custom (64-bit)| (default, Dec 20 2016, 23:09:15)
Type "copyright", "credits" or "license" for more information.

IPython 5.3.0 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [757]: import emission.storage.timeseries.cache_series as esdc

In [758]: import emission.storage.timeseries.timequery as estt

In [759]: from uuid import UUID

In [760]: start_time = 1507410331.98

In [761]: end_time = 1507483297.71

In [762]: time_query = estt.TimeQuery("metadata.write_ts",
   .....:                             start_time, end_time)

In [763]: test_uuid = UUID("ea59084e-11d4-4076-9252-3b9a29ce35e0")

In [764]: data_list = esdc.find_entries(test_uuid, ["background/filtered_location"], time_query)

In [765]: len(data_list)
Out[765]: 80

```

In the script above:

- `start_time` is the `write_ts` of the first entry pushed

    ```
    2017-10-08 17:45:58,105:DEBUG:139922601957120:Updated result for user = ea59084e-11d4-4076-9252-3b9a29ce35e0, key = stats/client_nav_event, write_ts = 1507410331.98 = {'updatedExisting': False, u'nModified': 0, u'ok': 1, u'upserted': ObjectId('59da6456cb17471ac0bc8ef7'), u'n': 1}
    ```

- `end_time` is the `write_ts` of the last entry pushed

    ```
    2017-10-08 17:46:02,400:DEBUG:139922601957120:Updated result for user = ea59084e-11d4-4076-9252-3b9a29ce35e0, key = statemachine/transition, write_ts = 1507483297.71 = {'updatedExisting': False, u'nModified': 0, u'ok': 1, u'upserted': ObjectId('59da645acb17471ac0bc903c'), u'n': 1}
    ```

- `test_uuid` is the matching uuid for this user, e.g.

```
2017-10-08 17:45:57,979:DEBUG:139922601957120:user_uuid ea59084e-11d4-4076-9252-3b9a29ce35e0
```

More sophisticated queries that work directly against the timeseries are at 
https://github.com/e-mission/e-mission-server/blob/master/Timeseries_Sample.ipynb

They will only work once the intake pipeline has run and moved the data into the timeseries datastore.

### Trips on the phone stay in _draft_ (green background) forever ###

Trips are converted from draft -> processed when the intake pipeline has been run successfully on them.
- Instructions for running the intake pipeline for a single user (in debug mode): https://github.com/e-mission/e-mission-server#quick-start
- Instructions for running the intake pipeline as part of a cronjob: https://github.com/e-mission/e-mission-server/wiki/Deploying-your-own-server-to-production#the-analysis-pipeline
- Examples on how to setup the intake pipeline cronjob inside the docker container are here https://github.com/e-mission/e-mission-docker/tree/master/examples/em-server-multi-tier-cronjob 

If for some motives you need to run the cronjob outside the docker container, here's my little tutorial.

Since we are using source to set the environment for the intake script to run in, we create a little bash script by the name cron_intake.sh:
```
#!/bin/bash
source activate emission
./e-mission-py.bash bin/intake_multiprocess.py 4
```
make sure it's executable:
```
chmod a+x cron_intake.sh
```
Since we've couldn't figure out how to get the cronjob working inside the docker container, we are going to create the cronjob outside of the container on the host machine. As a first test, we can run this script from outside the container:
```
docker exec docker_web-server_1 /bin/bash /usr/src/app/cron_intake.sh
```
where docker_web-server_1 is the name of your running container.
Now for the cronjob. Make sure you are logged in as the user you created the containers with and edit the crontab.
```
crontab -e
```
And add the line like this to execute the pipeline every minute. This shouldn't be a problem since there's usually not a lot of trips coming in at the same time. For a higher volume solution, you should probably check if concurrent running cronjobs really don't cause any problems.
```
* * * * * docker exec docker_web-server_1 /bin/bash /usr/src/app/cron_intake.sh
```
Also, you should make sure the cron daemon is actually running with:
```
service crond status
```
or, on other systems like Ubuntu 16, the service is called "cron", not "crond", so it's
```
service cron status
```
If you see that it's running, all is setup. If not, launch the service with: (put in cron or crond depending on your system)
```
service crond start
```

our crontab finally looked like this
```
SHELL=/bin/bash
BASH_ENV="/root/.bashrc"
PATH=/bin:/sbin:/usr/bin:/usr/sbin:/snap/bin

* * * * * /bin/bash -c ". ~/.bashrc; docker exec -t docker_web-server_1 /bin/bash /usr/src/app/cron_intake.sh &>> intakedocker-crontest.txt"
```
the magic ingredient I think was to add /snap/bin to the PATH. Because we installed docker as snap package, it was never in the bash path and also .bashrc didn't add it but it was always in the environment through some other system-level black magic





#### Check 1: Was there an error while running the pipeline? ####

The pipeline run logs are in the location configured in https://github.com/e-mission/e-mission-server/wiki/Deploying-your-own-server-to-production#configuring-logs (`/var/tmp`) by default. The logs are automatically compressed and should be viewed using `zless`/`bzless` or `zcat`/`bzcat`.

If all went well, you should see logs like the ones below, which indicate that user with UUID = 'f973520d-a339-44c8-bc02-6e35430a7b63' has trips for days `2017-09-03`, `2017-09-09` and `2017-09-10`.

```
2017-10-03 23:55:37,908:DEBUG:140303737620224:Found 0 messages in response to query {'$o
r': [{'metadata.key': 'manual/incident'}], 'user_id': UUID('f973520d-a339-44c8-bc02-6e35
430a7b63'), 'data.ts': {'$lte': 1505016612.259, '$gte': 1505016082.7513525}}
....
2017-10-03 23:55:37,909:DEBUG:140303737620224:After binning, we have 3 bins, of which 0
are empty
2017-10-03 23:55:37,910:DEBUG:140303737620224:Adding 2 trips for day 2017-09-09
2017-10-03 23:55:37,928:DEBUG:140303737620224:Result = {'updatedExisting': True, u'nModified': 1, u'ok': 1.0, u'n': 1} after updating document diary/trips-2017-09-09
2017-10-03 23:55:37,928:DEBUG:140303737620224:Adding 7 trips for day 2017-09-10
2017-10-03 23:55:37,996:DEBUG:140303737620224:Result = {'updatedExisting': True, u'nModified': 1, u'ok': 1.0, u'n': 1} after updating document diary/trips-2017-09-10
2017-10-03 23:55:37,996:DEBUG:140303737620224:Adding 9 trips for day 2017-09-03
2017-10-03 23:55:38,087:DEBUG:140303737620224:Result = {'updatedExisting': True, u'nModified': 1, u'ok': 1.0, u'n': 1} after updating document diary/trips-2017-09-03
2017-10-03 23:55:38,088:DEBUG:140303737620224:curr_key_list = [u'common-trips', u'diary/trips-2017-09-09', u'diary/trips-2017-09-10', u'diary/trips-2017-09-03'], valid_key_list = [u'diary/trips-2017-09-09', u'diary/trips-2017-09-10', u'diary/trips-2017-09-03', 'config/sensor_config', 'config/sync_config', 'config/consent']
2017-10-03 23:55:38,095:DEBUG:140303737620224:obsolete keys are: set([u'common-trips'])
2017-10-03 23:55:38,096:DEBUG:140303737620224:Result of removing document with key common-trips is {u'ok': 1.0, u'n': 1}
```

If you don't see those messages for a particular user, you need to look back and find the error that occurred earlier in the pipeline. If you do see these messages, you should be able to the timeline for those days on the phone. 

#### Check 2: Is there any error in the webserver? ####

The webserver logs are in the same location as the intake pipeline logs. The webserver is multi-threaded, so the thread id is part of the log message - e.g.

```
Formatted time:         LEVEL:   threadid:    message
2017-09-17 04:59:48,096:DEBUG:140063347021568:Called timeline.getTrips/2017-09-16
```

They also have a `START` and `END` log bookending every call, and the `END` log message also shows the UUID - e.g.

```
2017-09-30 17:54:19,218:DEBUG:140063355414272:START POST /timeline/getTrips/2017-08-12
2017-09-30 17:54:19,269:DEBUG:140063355414272:END POST /timeline/getTrips/2017-08-12 f973520d-a339-44c8-bc02-6e35430a7b63 0.0516500473022
```

And to map the username to the UUID (if you are testing), or the UUID to the username (if you want to see who was affected by an error), you can use the UUID database - e.g.

```
$ ~/e-mission-server$ ./e-mission-ipy.bash
Python 2.7.13 |Anaconda custom (64-bit)| (default, Dec 20 2016, 23:09:15)
Type "copyright", "credits" or "license" for more information.

IPython 5.3.0 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: import emission.core.get_database as edb

In [2]: from uuid import UUID

In [4]: edb.get_uuid_db().find_one({"uuid": UUID("f973520d-a339-44c8-bc02-6e35430a7b63")
   ...: })
Out[4]:
{u'_id': ObjectId('59a5965d22e316f169fc2b8f'),
 u'update_ts': datetime.datetime(2017, 9, 30, 8, 57, 52, 33000),
 u'user_email': u'<redacted>',
 u'uuid': UUID('f973520d-a339-44c8-bc02-6e35430a7b63')}

In [5]: edb.get_uuid_db().find_one({"user_email": "<redacted>"})
Out[5]:
{u'_id': ObjectId('59a5965d22e316f169fc2b8f'),
 u'update_ts': datetime.datetime(2017, 9, 30, 8, 57, 52, 33000),
 u'user_email': u'<redacted>',
 u'uuid': UUID('f973520d-a339-44c8-bc02-6e35430a7b63')}
```

### My data disappears when I run the intake pipeline ###

The data in the usercache is copied into the timeseries as part of running the
intake pipeline. If you saw data in the usercache before running the intake
pipeline, but didn't see corresponding entries in the timeseries, there is an
error in the input -> storage conversion formatter.

#### How do I know that this is the case? ####

There will be errors in the intake logs that you can grep (e.g. `$ bzgrep <key> <path/to>/intake_*.log.gz`). For example:

```
$ bzgrep survey /var/tmp/intake_*.log.gz | vim -
/var/tmp/intake_1.log.gz:2017-09-29 22:36:44,777:DEBUG:140594779940608:module_name = emission.net.usercache.formatters.android.survey
/var/tmp/intake_1.log.gz:2017-09-29 22:36:44,806:WARNING:140594779940608:Got error 'AttrDict' instance has no attribute 'ts' while saving entry AttrDict(...) -> None
/var/tmp/intake_1.log.gz:2017-09-29 22:36:44,807:DEBUG:140594779940608:Inserting entry {u'data': {...}, u'_id': ObjectId('59c03b5422e316f169fcbc68'), u'user_id': UUID('...'), u'metadata': {...}} into error timeseries
```

#### Have I lost my data? ####

No, as the logs say, the entries are saved in the error database.

```
In [11]: edb.get_timeseries_error_db().find().count()
Out[11]: 23

In [12]: edb.get_timeseries_error_db().find().distinct("metadata.key")
Out[12]: [u'manual/survey']
```

#### How do I recover? ####

First, you have to fix the formatter. The formatters are in
`emission/net/usercache/formatters`, and are platform and key specific. In the
example above, the formatter being run was module
`emission.net.usercache.formatters.android.survey` =
`emission/net/usercache/formatters/android/survey.py`

Next, you can run the `bin/debug/fix_usercache_processing.py` script, which
will copy entries from the error database back to the usercache, and then try
to move them to the timeseries again. Repeat until there are no more errors.
The `fix_usercache_processing` script currently does not log to a file, so you
may want to redirect the output to see whether there are any errors or not.

```
$ bin/debug/fix_usercache_processing.py 2>&1 | tee /var/tmp/fix_formatter.log
```

Finally, if the entries affect the analysis outputs, you can reset the pipeline state
and re-run it.
- you can reset the pipeline state to the time before the errors occurred by
  using the `bin/reset_pipeline.py` script - e.g.
    ```
    $ bin/debug/reset_pipeline.py -a -d 2017-07-01 2>&1 | tee /var/log/pipeline_reset.log
    ```

- After resetting the pipeline, you need to re-run the intake pipeline using
  the standard process.
