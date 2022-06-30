## Trip labelling options

As of e-mission-phone v3.2.3, there are two survey options available for trip labelling:

- [MULTILABEL](https://user-images.githubusercontent.com/2423263/170624159-ada650ce-1c3e-4fad-8104-9fcfad4fc322.mov): displays two buttons under each trip, one for travel mode and the other for purpose. Note that, only one response can be selected in each button.
- [ENKETO](https://user-images.githubusercontent.com/2423263/170631795-5ca2f330-1626-4709-aa7a-0f63370d5f1f.mov): displays one button that launches an Enketo survey form which allows for greater customisation of question types and display logic.

By default, the `MULTILABEL` option is used. To change to `ENKETO`, find all `SurveyOptions.MULTILABEL` under the folder `www/js/diary` of your e-mission-phone and replace them with `SurveyOptions.ENKETO`.

To create your own Enketo survey form, see the instruction in https://github.com/e-mission/e-mission-phone/tree/master/survey-resources.