# How to embed an external survey in the app
---

The phone app supports embedding an external survey in the app. This survey is
launched in a separate window - the user can hit "Done" to return to the main
app at any time. Certain fields in the survey can be automatically filled out
by the app. This allows researchers to correlate the survey to any
automatically sensed data.

The app currently supports two kinds of auto-filled information. It has been
tested in qualtrics, but the basic idea should work for any survey platform
that identifies answer fields by ids.

### Auto-fill UUID ###
The app can fill in the user's UUID into a field of the survey. This ensures
that sensitive information (such as email) is not leaked to the survey
platform, but the survey results can still be correlated with other data
collected.

Here's an example qualtrics survey during construction and while in use. Note
that Q3 is automatically filled out by the app when the user launches the
survey. The auto-filled element MUST be in the first page of the survey.

| Creation | Filled out |
|-------------- | ---------- |
| ![](https://github.com/e-mission/e-mission-server/blob/master/webapp/www/img/intro/qualtrics_creation_example.png) | ![](https://github.com/e-mission/e-mission-server/blob/master/webapp/www/img/intro/survey_response.png) |

This functionality is controlled by a survey spec, which specifies the URL to load the survey from, and the id of the element that needs to be filled out with the UUID.

The launch spec for the survey above is:

```
{
      "url": "https://berkeley.qualtrics.com/SE/?SID=SV_9t6KnGlK1qxOGGN",
      "uuidElementId": "QR~QID3"
}
```

The UUID element ID can be determined by opening the survey URL in a web
browser and inspecting the DOM. The steps for opening the DOM in several common browsers is:
- Firefox: Tools -> Web Developer -> Inspector,
- Safari: Develop -> Show Web Inspector (may need to enable developer menu),
- Chrome: View -> Developer -> Developer Tools.

The DOM element in the browser looks like this. Note that the `<input>` tag has
`id="QR~QID3` that is the id that needs to be put into the spec.

```
<div class="Inner BorderColor SL"gcdiv class="InnerInner BorderColor">
<fieldset>
<h2 class="noStyle"><label for="QR~QID3" class="QuestionText BorderColor">This will be auto-filled with your unique user id. This allows us to link your answers with the passively collected data without giving out your email to qualtrics.</label></h2><div class="QuestionBody">
<div class="ChoiceStructure">
<input autocomplete="off" id="QR~QID3" value="hello" class="InputText QR-QID3 QWatchTimer" name="QR~QID3~TEXT" data-runtime-textvalue="runtime.Value" type="TEXT">
</div></div>
</fieldset>
</div>
</div>
```

Once the spec is ready, launch the survey (either on button click or on navigate) using:
https://github.com/e-mission/e-mission-phone/blob/ad0344286751f62de1cf515f599f9dd7dfce4614/www/js/goals.js#L897

e.g.


```
angular.module('...',[..., 'emission.survey.launch', ....]
.controller('..;.', function(..., SurveyLaunch, ...) {
    $scope.startSurvey = function () {
      // arguments are (URL, elementID)
      SurveyLaunch.startSurvey('https://berkeley.qualtrics.com/SE/?SID=SV_9t6KnGlK1qxOGGN', 'QR~QID3');
    }
})
```

and in HTML

```
<button class="button button-icon ion-clipboard" ng-click="startSurvey()">Survey</button>
```

### Auto-fill UUID and trip information ###
The app can also fill in trip start and end timestamps and formatted times.
Note that although start and end locations are not leaked, start and end times
are, which means that this is not quite as privacy preserving as the previous approach.

In this case, your survey needs to have 5 special fields that you need to
obtain element IDs for (`uuid`, `start_ts`, `start_fmt_time`, `end_ts`,
`end_fmt_time`)

Then, you launch the survey using

```
SurveyLaunch.startSurveyForCompletedTrip("https://berkeley.qualtrics.com/jfe/form/SV_80Sj1xdMHDrV4vX",
      "QR~QID12", "QR~QID13~1", "QR~QID13~2", "QR~QID13~3", "QR~QID13~4",
      start_ts, end_ts);

```

An example of a commit that implemented such a change is 
https://github.com/e-mission/e-mission-phone/commit/46eb53952d379808f994903dbd9a9d5a935b127a
