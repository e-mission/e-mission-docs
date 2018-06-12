# High-level FAQ
---

#### How do you deploy a custom UI on the base app? ####
https://github.com/e-mission/e-mission-phone/wiki/You-have-a-branch-for-your-custom-client.-Now-what%3F

#### I get a blank screen while loading the e-mission phone UI ####
This is the White Screen Of Death (WSOD) and typically indicates that you have an error preventing the scripts from loading.
- First make sure that you have followed all setup instructions. Some of them download Javascript dependencies into the project. If they are not run, the files will be missing, and you will get errors. If in doubt, re-run the setup instructions, they are idempotent.
- Next, check the UI logs in the terminal where you run the UI development server. UI logs are redirected by default, so if the error occurred after logging was set up, you can see the error there.
- Otherwise, connect to the app using a debugger.
  - For iOS, the debugger is built in to Safari (https://www.lifewire.com/how-to-enable-safari-develop-menu-2260894)
  - for android, the debugger is built in to chrome (http://geeklearning.io/apache-cordova-and-remote-debugging-on-android/).
  
There is ample documentation on how to use these tools - e.g. search for "debug javascript error in cordova using safari" for tutorials, etc. You might also find [this video](https://people.eecs.berkeley.edu/~shankari/syntax_error_wsod.mov) useful.

#### How to get the app onto a real phone ####
This really depends on whether you still plan to change the UI, or whether you are largely done with the UI changes and want to build a custom app for deployment, potentially with a different set of plugins.
- *For changing the UI*: [Use the devapp on your phone](https://github.com/e-mission/e-mission-devapp/blob/master/README.md#installing-on-a-real-phone)
- *For building a custom app*: [Build the phone app directly](https://github.com/e-mission/e-mission-phone/blob/master/README.md#updating-the-e-mission--plugins-or-adding-new-plugins)

#### I get an error while adding plugins ####

Sometimes, if you are on a poor internet connection, you will encounter errors related to adding plugins while building the native versions of the phone apps. Retrying will just cause a different set of plugins to fail, since the underlying issue is the internet quality. In this case, a workaround is to manually clone and add the repositories, which seems to work.

##### Plugin clone errors #####

When you are trying to build and run the native code, you sometimes get plugin clone errors

```
$ npm run phonegap -- run ios | 2>&1 | grep --context=3 "Failed to restore plugin"
...
Failed to restore plugin "phonegap-plugin-contentsync" from config.xml. You might need to try adding it again. Error: Failed to fetch plugin git+https://github.com/e-mission/phonegap-plugin-contentsync.git via registry.
...
Failed to restore plugin "de.appplant.cordova.plugin.local-notification-ios9-fix" from config.xml. You might need to try adding it again. Error: Failed to fetch plugin https://github.com/shankari/cordova-plugin-local-notifications.git via registry.
...
Failed to restore plugin "cordova-plugin-inappbrowser" from config.xml. You might need to try adding it again. Error: Failed to fetch plugin git+https://github.com/shankari/cordova-plugin-inappbrowser.git via registry.
```
##### Workaround #####

The workaround is to manually clone the repo(s), add the local copy(ies) and rebuild. So for the case above, you would do:

```
$ cd ..
$ git clone https://github.com/e-mission/phonegap-plugin-contentsync.git
$ git clone https://github.com/shankari/cordova-plugin-local-notifications.git
$ git clone https://github.com/shankari/cordova-plugin-inappbrowser.git
$ cd e-mission-devapp/
$ cordova plugin add ../phonegap-plugin-contentsync/
$ cordova plugin add ../cordova-plugin-local-notifications/
$ cordova plugin add ../cordova-plugin-inappbrowser/
$ cordova run ios
```
