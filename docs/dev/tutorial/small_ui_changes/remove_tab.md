Remove tab
---

Several of the e-mission projects so far have removed some subset of the tabs. The naive way to remove a tab is to simply comment it out from the UI.

### Tab removal ###

1. Open `www/templates/main.html`
1. Comment out the game tab by changing
    ```
      <!-- Game Tab  -->
      <ion-tab title="Goals" icon="ion-android-checkbox" href="#/root/main/goals">
         <ion-nav-view name="main-goals"></ion-nav-view>
     </ion-tab>
    ```
    to
    ```
      <!-- Game Tab 
      <ion-tab title="Goals" icon="ion-android-checkbox" href="#/root/main/goals">
         <ion-nav-view name="main-goals"></ion-nav-view>
     </ion-tab> -->
    ```
1. Save the file
1. The change is detected and the UI reloads.
    ```
    [phonegap] file changed <path_to_e-mission-phone>/www/templates/intro/summary.html
    [phonegap] Running command: <path_to_emission_phone>/hooks/after_prepare/010_add_platform_class.js <path_to_emission_phone>
    ...
    [phonegap] [console.log] Ending config
    [phonegap] [console.log] Starting run
    [phonegap] [console.log] onLaunch method from factory called
    [phonegap] [console.log] Ending run
    ...
    [phonegap] [console.log] Running calorieData with 0 and 0
    [phonegap] [console.log] Running calculation with NaN and NaN
    [phonegap] 200 /__api__/autoreload?appID=39ec34dbbee54c9df1a13aa626c467ca
    ```
1. Game tab is gone!


### Caveats ###

1. This is the most naive approach to removing the tab - just hide it from the user. This leaves background operations (e.g. invoked using `$ionicPlatform.ready`) in place. In order to fully remove them, you would want to remove the corresponding HTML and javascript as well. That is outside the scope of a *simple* change, use the ionic1 or angular1 documentation to experiment further.
1. Removing the heatmap tab is problematic because, since it is aggregate, and not personal, we redirect to it if the user does not consent. If you want to remove the heatmap tab, you need to change the disagree logic (https://github.com/e-mission/e-mission-phone/wiki/Changes-needed-when-the-set-of-tabs-changes), which involves a javascript change.

