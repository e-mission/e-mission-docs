7 steps to an end-to-end development environment for UI-only changes
---

1. Install the server, including all [dependencies](https://github.com/e-mission/e-mission-server#dependencies) and the [e-mission code](https://github.com/e-mission/e-mission-server#installupdate)
1. Set up the devapp, by [downloading it](https://github.com/e-mission/e-mission-devapp#download) and [installing it in an emulator](https://github.com/e-mission/e-mission-devapp#installing)
1. Install the UI, including all [dependencies](https://github.com/e-mission/e-mission-phone#dependencies) and the [e-mission code](https://github.com/e-mission/e-mission-phone#installation)
1. [Start the server components](https://github.com/e-mission/e-mission-server#development), including mongod and the e-mission-server. At the end of this step, you should see the following

    ```
    $ ./e-mission-py.bash emission/net/api/cfc_webapp.py
    storage not configured, falling back to sample, default configuration
    Connecting to database URL localhost
    analysis.debug.conf.json not configured, falling back to sample, default configuration
    Finished configuring logging for <RootLogger root (WARNING)>
    Replaced json_dumps in plugin with the one from bson
    Changing bt.json_loads from <function <lambda> at 0x10f5fdb70> to <function loads at 0x1104b6ae8>
    Running with HTTPS turned OFF - use a reverse proxy on production
    Bottle v0.13-dev server starting up (using CherootServer())...
    Listening on http://0.0.0.0:8080/
    Hit Ctrl-C to quit.
    ```
1. Load [sample data for a test user](https://github.com/e-mission/e-mission-server#quick-start). At the end of this step, you should see the following. The server is now able to respond to API requests for this user.

    ```
    $ ./e-mission-py.bash bin/debug/load_timeline_for_day_and_user.py emission/tests/data/real_examples/shankari_2015-07-22 test_july_22
    storage not configured, falling back to sample, default configuration
    Connecting to database URL localhost
    emission/tests/data/real_examples/shankari_2015-07-22
    Loading file emission/tests/data/real_examples/shankari_2015-07-22
    After registration, test_july_22 -> 908eb622-be3f-4cf4-bf04-1b7e610bea1c
    
    $ ./e-mission-py.bash bin/debug/intake_single_user.py -e test_july_22
    storage not configured, falling back to sample, default configuration
    Connecting to database URL localhost
    analysis.debug.conf.json not configured, falling back to sample, default configuration
    google maps key not configured, falling back to nominatim
    nominatim not configured either, place decoding must happen on the client
    transit stops query not configured, falling back to default
    2018-05-22T19:56:36.262694-07:00**********UUID 908eb622-be3f-4cf4-bf04-1b7e610bea1c: moving to long term**********
    2018-05-22T19:56:36.281071-07:00**********UUID 908eb622-be3f-4cf4-bf04-1b7e610bea1c: filter accuracy if needed**********
    2018-05-22T19:56:36.293284-07:00**********UUID 908eb622-be3f-4cf4-bf04-1b7e610bea1c: segmenting into trips**********
    2018-05-22T19:56:45.741950-07:00**********UUID 908eb622-be3f-4cf4-bf04-1b7e610bea1c: segmenting into sections**********
    2018-05-22T19:56:45.777937-07:00**********UUID 908eb622-be3f-4cf4-bf04-1b7e610bea1c: smoothing sections**********
    2018-05-22T19:56:45.784900-07:00**********UUID 908eb622-be3f-4cf4-bf04-1b7e610bea1c: cleaning and resampling timeline**********
    2018-05-22T19:56:46.055701-07:00**********UUID 908eb622-be3f-4cf4-bf04-1b7e610bea1c: inferring transportation mode**********
    2018-05-22T19:56:46.063397-07:00**********UUID 908eb622-be3f-4cf4-bf04-1b7e610bea1c: checking active mode trips to autocheck habits**********
    2018-05-22T19:56:46.067243-07:00**********UUID 908eb622-be3f-4cf4-bf04-1b7e610bea1c: storing views to cache**********
    ```
1. Start the [UI development server](https://github.com/e-mission/e-mission-phone#running) and connect the devapp to the UI development server. The devapp will go through the "Downloading" and "Extracting" stages and then load the e-mission UI
1. Go through the e-mission onboarding process. When prompted for "dummy-dev auth", use `test_july_22`. After the onboarding is complete, you can load data for July 22 **2015** from the diary tab.

#### Congratulations, you have now successfully set up the phone and server and connected them to each other ####
