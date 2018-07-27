# Adding a New Data Type
---

More advanced users may want to collect new types of data. The app currently stores data in a local sqlite database and pushes it to the server:
- at the end of every trip (on iOS)
- every hour (on both android and iOS)

This document outlines the steps to save a new datastructure to the phone buffer and have it show up in the timeseries object of the server.

#### Planning ####

1. You need to decide what kind of data it is. Is it sensor data (e.g.
location), or a message (e.g. state machine transitions) or as local-only
storage (e.g. notification configuration)?

1. You need to decide the data format.

On the phone, the data format can be:
- a native class, which allows data validation and easier access of the elements. For example, SimpleLocation, with android (https://github.com/e-mission/e-mission-data-collection/blob/master/src/android/wrapper/SimpleLocation.java) and iOS (https://github.com/e-mission/e-mission-data-collection/blob/master/src/ios/Wrapper/SimpleLocation.m) versions, OR
- pure JSON, which requires no new classes, but is harder to use because the fields have to be accessed as strings

On the server, the data is in JSON (e.g.
https://github.com/e-mission/e-mission-server/blob/master/emission/core/wrapper/location.py).  In addition to creating such an object, you need to
- include a reference to it in `entry.py`
- in `builtin_timeseries.py`
- add formatters that can expand fields if necessary (e.g. https://github.com/e-mission/e-mission-server/blob/master/emission/net/usercache/formatters/android/location.py)

An example of a single PR that includes all the changes required to add an
object on the server is https://github.com/e-mission/e-mission-server/pull/517

#### Client changes ####

1. You need to store the object.
    - If you are storing the object as a native class, use `putSensorData`, `putMessage`, or `putLocalStorage` depending on your object type (android: https://github.com/e-mission/e-mission-data-collection/blob/master/src/android/location/LocationChangeIntentService.java#L89 or ios: )

    android:

                SimpleLocation simpleLoc = new SimpleLocation(loc);
                uc.putSensorData(R.string.key_usercache_location, simpleLoc);

    iOS:

                Transition* transitionWrapper = [Transition new];
                transitionWrapper.currState = [TripDiaryStateMachine getStateName:self.currState];
                transitionWrapper.transition = transition;
                transitionWrapper.ts = [BuiltinUserCache getCurrentTimeSecs];
            [[BuiltinUserCache database] putMessage:@"key.usercache.transition" value:transitionWrapper];

      The key in both these cases is mapped to the relevant string in the code (e.g. `R.string.key_usercache_location` is mapped to `background/location`). This mapping is in a resource XML for android and a plist for iOS. You need to add your own resource files to your plugin, similar to the ones in https://github.com/e-mission/cordova-usercache/tree/master/res, and then add entries to plugin.xml that copy them to the resource locations, similar to (https://github.com/e-mission/cordova-usercache/blob/master/plugin.xml)

          <resource-file src="res/android/usercachekeys.xml" target="res/values/usercachekeys.xml"/>
          ...
          <resource-file src="res/ios/usercachekeys.plist"/>

    - If you are storing the object as raw JSON, use similar methods, but pass in a JSONObject on android and an NSDictionary on iOS.

        ```
             JSONObject configWrapper = UserCacheFactory.getUserCache(ctxt).getLocalStorage(eventName, false);
            ...
            if (modified) {
                UserCacheFactory.getUserCache(ctxt).putLocalStorage(eventName, configWrapper);
            }
        ```

1. You might want to read the object to perform local computation during the
trip, before it is pushed to the server. If so, for sensor data and messages,
you can get the first n entries, the last n entries, and all entries in a
particular time range - e.g. `getFirstMessages`, `getLastMessages` and
`getMessagesForInterval`. Caveats:
    - If you are using native classes, you need to pass in the class on iOS. 
    - Local storage is supposed to be kv pairs, so it only supports get. It is
      also the only type that supports remove.

Examples:
android

        SimpleLocation[] last10Points = uc.getLastSensorData(R.string.key_usercache_filtered_location, pointsToQuery , SimpleLocation.class);

iOS

        NSArray* last10Points = [[BuiltinUserCache database]
                                              getLastSensorData:@"key.usercache.location" nEntries:10
                                              wrapperClass:[SimpleLocation class]];


iOS, local storage

         NSDictionary* notifyConfigWrapper = [[BuiltinUserCache database] getLocalStorage:eventName


To check whether the data was saved correctly to the phone database, you can
email the database to yourself (Profile -> Check sensed data -> Email). The database
will be sent as an attachment. It is an sqlite3 database and can be opened using 

    $ sqlite3 <db_file_name>


#### Push the data to the server ####

To test this, you need to be running a server (https://github.com/e-mission/e-mission-server/README.md). The easiest option is to run the server on your laptop. Ensure that your phone is connected to your server - if needed, by configuring `www/json/connectionConfig.json` accordingly (https://github.com/e-mission/e-mission-phone#end-to-end-testing)

The data is normally pushed automatically to the server, but you may want to force it during testing. In order to ensure that the data for the trip can be retrieved during the trip for local processing, only data upto the last `TRIP_ENDED` transition is pushed to the server. This means that you need to start and end a trip every time you push data.

You can start and end trips by directly manipulating the state machine in the developer zone. `EXIT_GEOFENCE` followed by `STOPPED_MOVING` on android or `TRIP_ENDED` will generate a trip. Force syncing after that should push the data to the server. Check out the troubleshooting section to confirm if this worked correctly (https://github.com/e-mission/e-mission-server/wiki/Troubleshooting-tips-(FAQ)#is-the-data-getting-to-the-server-correctly).

#### Access the data on the server ####

When the data gets to the server, it is in the usercache. When the regular pipeline runs (https://github.com/e-mission/e-mission-server/wiki/Deploying-your-own-server-to-production#the-analysis-pipeline), the formatters are run on it, and it moves from the usercache to the timeseries.

A unified view of the both the usercache and the timeseries is provided by the `cache_series` datastructure and can be used to confirm that the objects were saved correctly.
(https://github.com/e-mission/e-mission-server/wiki/Troubleshooting-tips-(FAQ)#confirm-that-the-entries-are-really-in-the-database)

If they were originally visible, but are lost when the pipeline runs, there is an error with the formatter (https://github.com/e-mission/e-mission-server/wiki/Troubleshooting-tips-(FAQ)#my-data-disappears-when-i-run-the-intake-pipeline). If they are visible even after the formatter runs, you can use other timeseries operations to manipulate the data.
