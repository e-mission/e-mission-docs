# Changes needed when the set of tabs changes
---

An example of all these changes is at: https://github.com/e-mission/e-mission-phone/pull/304

In general, to change the set of tabs shown in the UI, it is sufficient to comment out unwanted tabs, or restore required tabs in `www/templates/main.html`
e.g. add a new tab

```
  <!-- CCI About Tab -->
  <ion-tab title="Welcome" icon="ion-home" href="#/root/main/cci-about">
    <ion-nav-view name="main-cci-about"></ion-nav-view>
  </ion-tab>
```

or remove an existing one

```
  <!-- Game Tab  -->
  <!--ion-tab title="Goals" icon="ion-android-checkbox" href="#/root/main/goals">
    <ion-nav-view name="main-goals"></ion-nav-view>
  </ion-tab-->
```

But there are a few considerations to keep in mind if you change the set of tabs significantly.

### What should the initial tab be? ###
The logic about the first tab shown when the user launches the app is in `startprefs.getNextState`. 
The default (after onboarding is complete, there is no redirect, and there is no referral) is 

```
194           } else {
195             return 'root.main.metrics';
196           }
```

Change this to the state you want (e.g. `root.main.diary` if you want to start with the diary).

### What to do if the user disagrees ###
First, you need to ensure that there is a default state to go to if the user does not want to consent (e.g. hits "Disagree" on the consent screen). By default, as you can see from `www/js/intro.js`,

```
  $scope.disagree = function() {
    $state.go('root.main.heatmap');
  };
```

we go to the heatmap screen. But if you have commented out the heatmap, you need to have a different screen for the redirect.

#### Simplest option ####
If you can use an existing state, you can just replace the state name, e.g. `$state.go('root.intro')` will take the user back to the start of the onboarding. But if you do want to add a new screen, maybe one that has information on answering questions about the data collection, you need to add a new state.

#### More customization ####
You add a new state with two steps:
- a new HTML file in `www/templates` with the information to be shown on rejection (similar to `www/templates/main-heatmap.html`, e.g. `www/templates/main-reject.html`). Note that this is a top level screen, and needs to be in an `ion-view` - e.g.
```
<ion-view.....>
   <ion-content....>
   </ion-content....>
</ion-view....>
```
- a new state config in `www/js/main.js` that maps to an existing view in
  `main.html` and an existing controller - e.g.

    ```
  .state('root.main.refuse', {
    url: '/refuse',
    views: {
      'main-cci-about': {
        templateUrl: 'templates/intro/refuse.html',
        controller: 'CCIAboutCtrl'
      }
    }
  });
    ```
An example of such a change is https://github.com/e-mission/e-mission-phone/pull/281, with some corrections in https://github.com/e-mission/e-mission-phone/pull/284

### Check the list of states that display personalized information ###
If a particular state displays personalized information, and the user tries to navigate to it without having consented, we should pop up the consent form again. Without consent, we will not collect data, and we cannot show any personalized information. The list of personalized states is in `www/js/controllers.js` and by default, it is

```
      var personalTabs = ['root.main.common.map',
                          'root.main.control',
                          'root.main.metrics',
                          'root.main.goals',
                          'root.main.diary']
```

If any personalized tabs have been added, they need to be added to this list as well. Note that the reverse is not true - if any personalized tabs have been commented out, they do not need to be removed from this list, although there is no harm in removing them.