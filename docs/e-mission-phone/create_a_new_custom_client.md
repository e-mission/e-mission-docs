# Create a new custom client
---

Steps to create a new custom client with a new app store entry.
Document now, clean up later.

1. copy over `config.xml`
1. change the package in the `id`
1. reset the `android-versionCode`, `ios-CFBundleVersion` and `version`
1. create a new appid in apple (https://developer.apple.com) with the new id specified in step 2
1. the current version of the push plugin requires firebase to be configured even if you are not using i
    - create a new firebase project for the new package (https://console.firebase.google.com)
    - set up an iOS app with the new appid from the previous step
    - go to Settings -> Cloud Messaging
    - upload your APNs Authentication Key (create one from the apple developer account under Keys -> APNs key if you don't have one)
    - get the `google-services.json` and `GoogleService-Info.plist` and put them into the project root
1. *optional: iff you want to support UI channels* create a new entry for the app in the ionic console
    - copy the app id (next to the project name) into the `config.xml` under `cordova-plugin-ionic`
1. copy over `package.json` and make the same changes as `config.xml`
1. follow the full installation steps from the README (https://github.com/e-mission/e-mission-phone#updating-the-e-mission--plugins-or-adding-new-plugins)
1. create or copy over relevant HTML + javascript into `www`
1. create an icon under `resources/icon.png`
1. copy over the hooks (`./hooks`) and any notification icons needed (`resources/android/*`)
1. If you want to use google auth, create google auth credentials correctly for the new package name and include them into `www/json/connectionConfig.json`
1. After you have finished testing, follow the standard instructions to create a production release and upload to the app/play stores. For android, you can use the `bin/sign_and_align_keys.sh` as a template. Note that this script will not work for you without modification - it has hardcoded paths. If you don't know how to edit shell scripts to match your environment, feel free to use android studio instead.
