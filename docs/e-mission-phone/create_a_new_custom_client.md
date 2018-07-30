# Create a new custom client
---

Steps to create a new custom client with a new app store entry.
Document now, clean up later.

1. copy over `config.xml`
1. change the package in the `id`
1. reset the `android-versionCode`, `ios-CFBundleVersion` and `version`
1. create a new appid in apple (https://developer.apple.com) with the new id specified in step 2
1. create a new firebase project for the new package (https://console.firebase.google.com)
    - set up an iOS app with the new appid from the previous step
    - go to Settings -> Cloud Messaging
    - upload your APNs Authentication Key (create one from the apple developer account under Keys -> APNs key if you don't have one)
    - get the firebase sender ID and put it into the config.xml for the push plugin
1. create a new entry for the app in the ionic console
    - copy the app id (next to the project name) into the `config.xml` under `cordova-plugin-ionic`
1. copy over `package.json` and make the same changes as `config.xml`
1. follow installation steps from the README (https://github.com/e-mission/e-mission-phone/blob/master/README.md)
1. create or copy over relevant HTML + javascript into `www`
1. create an icon under `resources/icon.png`
1. copy over the hooks (`./hooks`) and any notification icons needed (`resources/android/*`)
1. The .R file is currently created referenced in many places as `edu.berkeley.eecs.emission.R`. It needs to be `<your_package>.R`. I need to find a better way to do this, but for now, simply rename all instances in your local platform directory.
1. If you want to use google auth, create google auth credentials correctly for the new package name and include them into `www/json/connectionConfig.json`

 