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
