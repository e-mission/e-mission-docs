# Schedule a trip-end notification
Currently, the app prompts users to confirm their mode and purpose for each trip. While this is good, we might want, for example, to have them at the end of the day.

Follow the following steps to schedule a trip-end notification.

### Step 1 — Disable the old trip-end notification.

Comment out the content of the `registerTripEnd` method found in `www/js/tripconfirm/post-trip-prompt.js` to disable the trip-end notification, .

### Step 2 - Setup cordova notification plugin

First, install the plugin

```shell
$ cordova plugin add cordova-plugin-local-notification
$ cordova prepare
```

Then add the following code snippet to `www/js/constant.js`.

```json
CONSTANTS = {
  "notification": {
    "debug": false,
    "id": 6724531,
    "day": 3,
    "hour": 19,
    "every": "week"
  }
}
```

> Create the `constant.js` file if it does not exit.

The parameters are as follows:

- **id**: The identifier of the notification. Allow to be unique in the notification stack and avoid conflicts with other notifications. Make sure that this id is not used by another notification.

- **debug**: if it is true, it will repeat itself every minute from the connection to the application.
  If it is false it will take into account the parameters "day", "hour", "minute" and "every".

- **hour**: The hour of the first notification (0-23)

- **minute**: The minute of the first notification (0-59)

- **day**: The day of the first notification: 
  - 0 => Sunday 
  - 1 => Monday 
  - 2 => Tuesday 
  - 3 => Wednesday 
  - 4 => Thursday 
  - 5 => Friday 
  - 6 => Saturday

- **every**: the interval at which to reschedule the reminder notification. The possible values ​​(text) are as follows: 
  - second 
  - minute 
  - hour 
  - day 
  - week 
  - month 
  - year

  Checkout the [official plugin documentation](https://github.com/katzer/cordova-plugin-local-notifications) for more details.

  ### Step 3 - Create a new method for the notification scheduling

  To begin, add the following content to `www/js/splash/startprefs.js`.

  ```js
  startprefs.setReminderNotification = function () {
    // Set weekly notification
    Logger.log("_NOTIF: SET WEEKLY NOTIF");
    Logger.log("_NOTIF: " + localStorage.getItem("reminderNotification"));
    if (!localStorage.getItem("reminderNotification")) {
      var notif = CONSTANTS.notification;
      var first_at, every;
      if (notif.debug === true) {
        first_at = new Date(new Date().getTime() + 20 * 1000);
        every = "minute";
      } else {
        first_at = startprefs.getNextDayOfWeek(new Date(), notif.day);
        first_at.setHours(notif.hour, notif.minute, 0);
        every = notif.every;
      }
      $window.cordova.plugins.notification.local.schedule([
        {
          id: notif.id,
          text: $translate.instant("trips-reminder"),
          firstAt: first_at,
          every: every,
        },
      ]);

      $window.cordova.plugins.notification.local.on("click", function (
        notification,
        state,
        data
      ) {
        $state.go("root.main.diary");
      });
      $window.cordova.plugins.notification.local.on("trigger", function (
        notification,
        state,
        data
      ) {
        var reminderPopup = $ionicPopup
          .alert({
            template: $translate.instant("trips-reminder"),
          })
          .then(function (res) {
            $state.go("root.main.diary");
          });
      });
      Logger.log("_NOTIF: END SET WEEKLY NOTIF");
      localStorage.setItem("reminderNotification", 1);
    }
  };
  ```
  Then invoke it anywhere you like based on your use case. In our case the 
  `startprefs.getNextState()` is a suitable place.
