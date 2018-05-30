Change summary and consent
---

All e-mission projects so far have wanted to change the summary and consent to reflect their use of the platform. The summary and consent are part of the onboarding module. The HTML for the module is at `www/templates/intro` and the corresponding javascript is at `www/js`. Summary and consent changes replace the displayed text, and are HTML-only changes.

All the changes here assume that you have finished the install, and your installation satisfies *all* the success criteria.

### Summary changes ###

1. Open `www/templates/intro/summary.html`
1. Change `E-Mission: Data driven carbon emission reduction` to `My Cool App: Get suggestions on your commute`
1. Save the file
1. The change is detected and the UI reloads.
    ```
    [phonegap] file changed /Users/shankari/e-mission/e-mission-phone/www/templates/intro/summary.html
    [phonegap] Running command: /Users/shankari/e-mission/e-mission-phone/hooks/after_prepare/010_add_platform_class.js /Users/shankari/e-mission/e-mission-phone
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
1. Log out (Profile -> Email) to restart the onboarding process
1. The summary title is now `My Cool App`

| How to logout | New title (my cool app) |
| ------------- | --------- |
| ![how to logout](../../assets/tutorials/simple_client_changes/how_to_logout.png) | ![new title (my cool app)](../../assets/tutorials/simple_client_changes/new_title_my_cool_app.png) |
