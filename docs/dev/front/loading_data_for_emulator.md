# How to Load Data into Local Server for Use with devapp

In order to fully test the functionality of the devapp, you will usually need to create an opcode with data. You **can** login to the devapp with a "real" opcode (ie the one you are logged into on a physical phone), which is useful for debugging. For more general development purposes, however, it is often more useful to work with sample data, loaded into an opcode of your choice. There are a few useful routes provided by the server for accomplishing this.

## Load Test Data from Server

You can find sample data in `e-mission-server/emission/tests/data/real_examples`. 
Then, you can load the data into an opcode with the following two scripts. Make sure you have activated the emission environment first.

```./e-mission-py.bash bin/debug/load_timeline_for_day_and_user.py emission/tests/data/real_examples/[file of your choice] nrelop_[complete valid opcode]```

```./e-mission-py.bash bin/debug/intake_single_user.py -e nrelop_[same valid opcode]```

After these two commands run sucessfully, you should be able to run the server and login with the opcode you created in the devapp.

## Load Data from Local File

Sometimes, you will have specific data that you want to load into the emulator. Once you have the two g-zipped files in a local directory, you can load the data into an opcode:

`./e-mission-py bin/debug/load_multi_timeline_for_range.py [path/to/files/with/prefix` -- this path might look something like `/Users/name/Downloads/my_test_data/test_data` where `my_test_data` is a folder containing the two g-zipped files that each start with `test_data`

The data will automatically create a "user email" for this opcode, by default it will be "user-0". This can be updated with: `edb.get_uuid_db().update_one({"user_email": "user-0"}, {"$set": {"user_email": "nrelop_usaid-laos-ev_default_test-1"}})`

Then, process the data by running the pipeline.

`./e-mission-py.bash bin/debug/intake_single_user.py -e [user-0 or updated]`

If you updated to a valid opcode, you can now start the server and log into the devapp with that opcode

## Not on Working with Deployment Configs

There are development configs designed to be used with the local server. They include `dev-emulator-program`, `dev-emulator-study`, and `dev-emulator-timeuse`. 

Sometimes, it is useful to load data into a deployment config for testing, demo, or debugging purposes. If you want to use the local server and a deployment config, you need to take a few extra steps. 
1. Fork `nrel-openpath-deploy-configs`
2. Modify the config you want to work on, making sure that there is no `server` defined (if no server is defined, the app will connect to the local server, as desired).
3. Update the downloadURL in `dynamicConfig` for the version of `e-mission-phone` you are running to `https://raw.githubusercontent.com/[MY-REPO]/nrel-openpath-deploy-configs/[MY-BRANCH]/configs/${label}.nrel-op.json`
4. Now you can use a development config with the local server

## Notes on making up a valid opcode
- opcodes must begin with `nrelop`
- opcodes must contain the name of a config (ie `dev-emulator-study` or `nrel-commute`)
- some configs require a subgroup (ie `default` or `test`)
- the endings to opcodes can be anything, it is most useful to choose something memorable (ie `myTestData` or `test2`)

All together the structure should be `nrelop_configName_subgroup_ending`
