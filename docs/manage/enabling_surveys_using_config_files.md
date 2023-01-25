# Enabling Surveys using Config Files
---

The config files found in e-mission/nrel-openpath-deploy-configs are the basis for adding survey support for buttons

For this guide, we assume:
- Familiarity with cloning/forking/branching for github
- Familiarity with docker and docker-compose
- Familiarity with iPhone emulator and iPhone emulator must be setup already
- Familiarity with e-mission-devapp (https://github.com/e-mission/e-mission-devapp) and getting the devapp to run on your emulator
- Familiarity with the e-mission-server / e-mission-phone stack and starting and connecting both e-mission-server and e-mission-phone

## Before we begin:

1. Make sure to set your e-mission/e-mission-phone branch to `places-survey` (https://github.com/sebastianbarry/e-mission-phone/tree/places-survey).

2. Make sure to set your e-mission/e-mission-server branch to `add-trip-place-additions` (https://github.com/shankari/e-mission-server/tree/add_trip_place_additions).

3. For e-mission/nrel-openpath-deploy-configs, fork the nrel-openpath-deploy-configs branch `surveys-info-and-surveys-data` (https://github.com/sebastianbarry/nrel-openpath-deploy-configs/tree/surveys-info-and-surveys-data) so that you can create your own config files.
    - Place any Enketo survey JSON files you would like to use in your Study/Program, into your forked directory `nrel-openpath-deploy-configs/survey-resources/data-json`.

4. If you are going to create/test your own version of the config files, you must make sure that your config file will be read in by e-mission-phone by doing the following:

_From the `e-mission-phone` root directory, navigate to `www/js/config/dynamic_config.js`. From here in the `DynamicConfig` Factory, there is a function named readConfigFromServer which contains the declaration for variable `const downloadURL = "..."`_ **Change the value of `downloadURL` to be the filepath for your config directory**.
```javascript
// The URL prefix from which config files will be downloaded and read.
// Change this if you supply your own config files.
const downloadURL = "https://raw.githubusercontent.com/sebastianbarry/nrel-openpath-deploy-configs/surveys-info-and-surveys-data/configs/"+label+".nrel-op.json"
```

5. The `label` variable used by `downloadURL` is defined in the `label=` field of the "config login link". _**How do you get to the config login screen?:**_ You only see this screen the very first time you open the app or after you have uninstalled/reinstalled the `em-devapp.app` _**How do you get past the config login screen?:**_ You use the config login link either by clicking/navigating to the link from a browser, or scanning the Study/Program QR code which is coded to the config login link (this step does not work on iPhone emulators as there is no camera to scan the QR code).

### Manual option

(a). Create a "config login link" using this format (filling in `YOUR_STUDY_OR_PROGRAM_LABEL` with your own label)

```h
emission://join_study?label=YOUR_STUDY_OR_PROGRAM_LABEL&source=github
```

(b). Open Safari in the emulator and paste in your link, and it should automatically open up e-mission


Create this link above, and then copy and paste it into Safari/Google Chrome to get past the config login step of the app. _If you make changes to the config file after you have already loaded it into the app, you **MUST uninstall the app from your emulator and reinstall it to re-load the changes you made to the config.** If you only make a change to the survey file however, you do not need to uninstall/reinstall the app because the `formPath` is downloaded dynamically._

6. Start the emulator and uninstall/reinstall your `em-devapp.app` so that after we make the config in the next seciton, we can load in the new config file. Copy and paste your `emission://join_study?label=YOUR_STUDY_OR_PROGRAM_LABEL&source=github`

### Docker-Compose option

No steps yet.

--- 

## How-to

In your config file:
  
  1. In the Main JSON object, add a key object named `"survey_info"`

```json
{
    "version": "...",
    "ts": "...",
    "intro": {
        "..."
    },
    "survey_info": {
      //HERE
    },
    "display_config": {
        "..."
    },
    "profile_controls": {
        "..."
    }
}
```
  2. Create 2 new key objects as children of `"survey_info"` named `"surveys"` and `"buttons"`, and create 1 new key-value pair as a child of `"survey_info"` named `"trip-labels"`

```json
{
    ...
    "survey_info": {
        "surveys": {
            //HERE
        },
        "buttons": {
            //HERE
        },
        "trip-labels": "" //HERE
    },
    ...
}
```

  3. For each unique survey you wish to include in your Study/Program, add a key object as a child of `"surveys"`. The name you give your survey object will be referenced later in our `"buttons"` object. _As a standard, use PascalCase to name your survey objects_

```json
{
    ...
    "survey_info": {
        "surveys": {
            "NameOfFirstSurvey": { 
                //HERE
            },
            "NameOfSecondSurvey": {
                //HERE
            }
        },
        "buttons": {
            
        },
        "trip-labels": ""
    },
    ...
}
```

#### _Note: The unique survey name `"UserProfileSurvey"` defines the survey used to collect user information/demographic information when a user logs in with an OPcode for the first time. This is not used by any buttons but is still required to be present in your config; see below **"Basic Config Template _(without any buttons or surveys)_"** under section **"Examples of configs with varying surveys _(for reference)_"**_

  4. The keys contained inside of each survey object are **important**. Here is the template to follow, and we will explain what each field is for:

```json
"NameOfFirstSurvey": { 
    "formPath": "", 
    "version": 1.0,
    "compatibleWith": 1,
    "dataKey": "",
    "labelVars": {
        "labelVar1": {
            "key": "",
            "type": ""
        },
        "labelVar2": {
            "key": "",
            "type": ""
        }
    },
    "labelTemplate": {
        "en": "{labelVar1} and {labelVar2}",
        "es": "{labelVar1} y {labelVar2}"
    }
}
```

- **`"formPath":`** defines the directory path to your survey's JSON file.
  - This may either be a local directory path, i.e. `surveys/mySurvey.json`
    - Keep in mind this path is read from `e-mission-phone/www/js/survey/enketo/service.js`, so if your `mySurvey.json` file is in `e-mission-phone/www/json` you would name this field `"formPath": "../../../json/mySurvey.json"`
  - This can also be an online path, i.e. `const downloadURL = "https://raw.githubusercontent.com/sebastianbarry/nrel-openpath-deploy-configs/surveys-info-and-surveys-data/surveys/mySurvey.json"`
    - Keep in mind that if this is an online path, if you do not have internet connection when the app reads in the survey it will not have the survey data.
  - As as standard, put your survey JSON files in your forked path `nrel-openpath-deploy-configs/surveys-info-and-surveys-data/surveys`
- **`"version":`** defines the version number of your survey
- **`"compatibleWith":`** should be set to 1 (SB: I am not sure what compatibleWith is or how it is used by our app)
- **`"dataKey":`** defines the data key, which is the location where the survey results data will be saved when the survey results are pushed to e-mission-server
- **`"labelVars": {...}`** contains the objects for the labelVars you wish to use inside your `"labelTemplate"`
  - **`"labelVar1": {...} and "labelVar2": {...}`** these named objects are the names of the variables you would like to request in `"labelTemplate"`. For example, if your survey had the question "What modes of transportation have you used today?" and you wanted to track the number of modes a user selects in their response, you could name your labelVar `"modes": {...}`
    - **`"key":`** should be set to the exact key of the question in your `mySurvey.json` file. i.e. if your "What modes of transportation have you used today?" key in `mySurvey.json` is `"What_modes_of_transportation_have_you_used_today"`, you would set `"key": "What_modes_of_transportation_have_you_used_today"`
    - **`"type":`** As of right now, the only "type" that is supported by e-mission-phone is `"length"` which counts the number of responses that were given in response to the question
- **`"labelTemplate": {...}`** contains the internationalizations for the templates that define the output when a survey response is submitted
  - **`"en":` / `"es":`/ etc.** contains the label template which you choose. This follows the messageformat standard found here: http://messageformat.github.io/messageformat/guide/. For example, if you wanted to go off of our previous example where labelVar was `modes`, some examples of responses you may want could be  `"No modes"` / `"1 mode"` / `"5 modes"` / etc. To get this, you would set the `"en":`(for english) field to `"{modes, plural, =0 {No modes} one {1 mode} other {# modes}}"` and the `"es":`(for spanish) field to `"{modes, plural, =0 {Sin modos} one {1 modo} other {# modos}}"`

5. Now that the survey definitions are done, we can assign them to the buttons where we want them to be used. OpenPATH currently supports the following buttons:

| Button Name | `key-name` | Location on app | Special notes |
:---:|:---:|:---:|:---:
| Trip Button | `"trip-button": {...}` | This button shows up at the bottom of the `<infinite-scroll-trip-item>`| This button will not show up in the user-interface UNLESS you define `"trip-button": {...}` as a key within the `"buttons": {...}` key object |
| Place Button | `"place-button": {...}` | This button shows up between 2 trips as a `<infinite-scroll-place-item>` | The entire `<infinite-scroll-place-item>` element will not show up in the user-interface UNLESS you define `"place-button": {...}` as a key within the `"buttons": {...}` key object |

6. For each button you wish to include in your Study/Program, add a key object as a child of `"buttons"`. The name you give your button object MUST match that of the `key-name` in the table above.

```json
"buttons": {
    "key-name1": {
        //HERE
    },
    "key-name2": {
        //HERE
    }
}
```

7. The keys contained inside of each survey object are **important**. Here is the template to follow, and we will explain what each field is for:

```json
"key-name1": {
    "surveyName": "",
    "not-filled-in-label": {
        "en": "",
        "es": ""
    },
    "filled-in-label": {
        "en": "",
        "es": ""
    }
}
```

- **`"surveyName":`** defines the survey that will display when this button is clicked
- **`"not-filled-in-label":`** contains the internationalizations for the text displayed on the button when the survey **has not** been filled out yet.
  -  **`"en":` / `"es":`/ etc.** contains the text which will be displayed on the button when the survey **has not** been filled out yet. For example, this might read `"Take my survey"`
- **`filled-in-label":`** contains the internationalizations for the text displayed on the button when the survey **has** been filled out.
  -  **`"en":` / `"es":`/ etc.** contains the text which will be displayed on the button when the survey **has** been filled out. For example, this might read `"Response submitted"`

8. Finally, you will define how the trip-labels (e.g. the labels for mode and purpose of each trip) will be collected. There are only 2 options currently supported by OpenPATH: `MULTILABEL` and `ENKETO`.

```json
...
"trip-labels": "MULTILABEL"
...
```
or
```json
...
"trip-labels": "ENKETO"
...
```

9. Now that your config file has been completed, you can compare it to some of our examples below (in the section _Examples of configs with varying surveys (for reference)_) to make sure you have written it correctly:

# Wrap up:

1. Publish your 


# Examples of configs with varying surveys _(for reference)_

## Basic Config Template _(without any buttons or surveys, and MULTILABEL for trip-labels)_
----
`[your program name].nrel-op.json`
```json
{
    "version": "...",
    "ts": "...",
    "intro": {
        "program_or_study": "...",
        "start_month": "...",
        "start_year": "...",
        "program_admin_contact": "...",
        "deployment_partner_name": "...",
        "translated_text": {
            "en": {
                "deployment_name": "...",
                "summary_line_1": "...",
                "summary_line_2": "...",
                "summary_line_3": "...",
                "short_textual_description": "...",
                "why_we_collect": "...",
                "research_questions": ["...", "..."]
            }
        }
    },
    "survey_info": {
      "surveys": {
        "UserProfileSurvey": {
          "formPath": "json/demo-survey-v2.json",
          "version": 1,
          "compatibleWith": 1,
          "dataKey": "manual/demographic_survey",
          "labelTemplate": {
            "en": "Answered",
            "es": "Contestada"
          }
        }
      },
      "trip-labels": "MULTILABEL"
    },
    "display_config": {
        "use_imperial": false
    },
    "profile_controls": {
        "support_upload": true,
        "trip_end_notification": true
    }
}
```

## TimeuseSurvey Config Template _(using the timeuse survey for trip-notes and place-notes, and MULTILABEL for trip-labels)_
----
`[your program name].nrel-op.json`
```json
{
    "version": "...",
    "ts": "...",
    "intro": {
        "program_or_study": "...",
        "start_month": "...",
        "start_year": "...",
        "program_admin_contact": "...",
        "deployment_partner_name": "...",
        "translated_text": {
            "en": {
                "deployment_name": "...",
                "summary_line_1": "...",
                "summary_line_2": "...",
                "summary_line_3": "...",
                "short_textual_description": "...",
                "why_we_collect": "...",
                "research_questions": ["...", "..."]
            }
        }
    },
    "survey_info": {
      "surveys": {
        "UserProfileSurvey": {
          "formPath": "json/demo-survey-v2.json",
          "version": 1,
          "compatibleWith": 1,
          "dataKey": "manual/demographic_survey",
          "labelTemplate": {
            "en": "Answered",
            "es": "Contestada"
          }
        },
        "TimeUseSurvey": {
          "formPath": "https://raw.githubusercontent.com/sebastianbarry/nrel-openpath-deploy-configs/surveys-info-and-surveys-data/survey-resources/data-json/time-use-survey-form-v9.json",
          "version": 9,
          "compatibleWith": 1,
          "labelVars": {
            "pca": {
              "key": "Personal_Care_activities",
              "type": "length"
            },
            "erea": {
              "key": "Employment_related_a_Education_activities",
              "type": "length"
            },
            "da": {
              "key": "Domestic_activities",
              "type": "length"
            },
            "ral": {
              "key": "Recreation_and_leisure",
              "type": "length"
            },
            "vwaca": {
              "key": "Voluntary_work_and_care_activities",
              "type": "length"
            },
            "other": {
              "key": "Other",
              "type": "length"
            }
          },
          "labelTemplate": {
            "en": "{ pca, plural, =0 {} other {# Personal Care, }}{ erea, plural, =0 {} other {# Employment/Education, } }{ da, plural, =0 {} other {# Domestic, } }{ ral, plural, =0 {} other {# Recreation/Leisure, } }{ vwaca, plural, =0 {} other {# Voluntary Work, } }{ other, plural, =0 {} other {# Other} }",
            "es": "{ pca, plural, =0 {} other {# Cuidado Personal, }}{ erea, plural, =0 {} other {# Empleo/Educación, } }{ da, plural, =0 {} other {# Domésticas, } }{ ral, plural, =0 {} other {# Recreación/Ocio, } }{ vwaca, plural, =0 {} other {# Voluntariado, } }{ other, plural, =0 {} other {# Otro} }"
          }
        }
      },
      "buttons": {
        "trip-notes": {
          "surveyName": "TimeUseSurvey",
          "not-filled-in-label": {
            "en": "Add Activity",
            "es": "Añadir actividad"
          }
        },
        "place-notes": {
          "surveyName": "TimeUseSurvey",
          "not-filled-in-label": {
            "en": "Add Activity",
            "es": "Añadir actividad"
          }
        }
      },
      "trip-labels": "MULTILABEL"
    },
    "display_config": {
        "use_imperial": false
    },
    "profile_controls": {
        "support_upload": true,
        "trip_end_notification": true
    }
}
```

## TripConfirm Config Template _(not using trip-notes or place-notes, and ENKETO for trip-labels)_
----
`[your program name].nrel-op.json`
```json
{
    "version": "...",
    "ts": "...",
    "intro": {
        "program_or_study": "...",
        "start_month": "...",
        "start_year": "...",
        "program_admin_contact": "...",
        "deployment_partner_name": "...",
        "translated_text": {
            "en": {
                "deployment_name": "...",
                "summary_line_1": "...",
                "summary_line_2": "...",
                "summary_line_3": "...",
                "short_textual_description": "...",
                "why_we_collect": "...",
                "research_questions": ["...", "..."]
            }
        }
    },
    "survey_info": {
      "surveys": {
        "TripConfirmSurvey": {
          "formPath": "https://raw.githubusercontent.com/sebastianbarry/nrel-openpath-deploy-configs/surveys-info-and-surveys-data/survey-resources/data-json/trip-end-survey-multiple-select.json",
          "version": 1.2,
          "compatibleWith": 1,
          "dataKey": "manual/trip_user_input",
          "labelVars": {
            "modes": {
              "key": "travel_mode",
              "type": "length"
            },
            "purposes": {
              "key": "destination_purpose",
              "type": "length"
            }
          },
          "labelTemplate": {
            "en": "${purposes} purpose${purposes > 1 ? 's': ''}, ${modes} mode${modes > 1 ? 's': ''}",
            "es": "${purposes} propósito${purposes > 1 ? 's': ''}, ${modes} modo${modes > 1 ? 's': ''}"
          }
        },
        "UserProfileSurvey": {
          "formPath": "json/demo-survey-v2.json",
          "version": 1,
          "compatibleWith": 1,
          "dataKey": "manual/demographic_survey",
          "labelVars": {
            
          },
          "labelTemplate": {
            "en": "Answered",
            "es": "Contestada"
          }
        }
      },
      "buttons": {
      },
      "trip-labels": "ENKETO"
    },
    "display_config": {
        "use_imperial": false
    },
    "profile_controls": {
        "support_upload": true,
        "trip_end_notification": true
    }
}
```

## Timeuse + TripConfirm Config Template _(using the timeuse survey for trip-notes and place-notes, and ENKETO for trip-labels)_
----
`[your program name].nrel-op.json`
```json
{
    "version": "...",
    "ts": "...",
    "intro": {
        "program_or_study": "...",
        "start_month": "...",
        "start_year": "...",
        "program_admin_contact": "...",
        "deployment_partner_name": "...",
        "translated_text": {
            "en": {
                "deployment_name": "...",
                "summary_line_1": "...",
                "summary_line_2": "...",
                "summary_line_3": "...",
                "short_textual_description": "...",
                "why_we_collect": "...",
                "research_questions": ["...", "..."]
            }
        }
    },
    "survey_info": {
      "surveys": {
        "TripConfirmSurvey": {
          "formPath": "https://raw.githubusercontent.com/sebastianbarry/nrel-openpath-deploy-configs/surveys-info-and-surveys-data/survey-resources/data-json/trip-end-survey-multiple-select.json",
          "version": 1.2,
          "compatibleWith": 1,
          "dataKey": "manual/trip_user_input",
          "labelVars": {
            "modes": {
              "key": "travel_mode",
              "type": "length"
            },
            "purposes": {
              "key": "destination_purpose",
              "type": "length"
            }
          },
          "labelTemplate": {
            "en": "{ purposes, plural, =0 {No purposes} one {1 purpose} other {# purposes} }, { modes, plural, =0 {No modes} one {1 mode} other {# modes} }",
            "es": "{ purposes, plural, =0 {No propósitos} one {1 propósito} other {# propósitos} }, { modes, plural, =0 {No modos} one {1 modo} other {# modos} }"
          }
        },
        "UserProfileSurvey": {
          "formPath": "json/demo-survey-v2.json",
          "version": 1,
          "compatibleWith": 1,
          "dataKey": "manual/demographic_survey",
          "labelTemplate": {
            "en": "Answered",
            "es": "Contestada"
          }
        },
        "TimeUseSurvey": {
          "formPath": "https://raw.githubusercontent.com/sebastianbarry/nrel-openpath-deploy-configs/surveys-info-and-surveys-data/survey-resources/data-json/time-use-survey-form-v9.json",
          "version": 9,
          "compatibleWith": 1,
          "labelVars": {
            "pca": {
              "key": "Personal_Care_activities",
              "type": "length"
            },
            "erea": {
              "key": "Employment_related_a_Education_activities",
              "type": "length"
            },
            "da": {
              "key": "Domestic_activities",
              "type": "length"
            },
            "ral": {
              "key": "Recreation_and_leisure",
              "type": "length"
            },
            "vwaca": {
              "key": "Voluntary_work_and_care_activities",
              "type": "length"
            },
            "other": {
              "key": "Other",
              "type": "length"
            }
          },
          "labelTemplate": {
            "en": "{ pca, plural, =0 {} other {# Personal Care, }}{ erea, plural, =0 {} other {# Employment/Education, } }{ da, plural, =0 {} other {# Domestic, } }{ ral, plural, =0 {} other {# Recreation/Leisure, } }{ vwaca, plural, =0 {} other {# Voluntary Work, } }{ other, plural, =0 {} other {# Other} }",
            "es": "{ pca, plural, =0 {} other {# Cuidado Personal, }}{ erea, plural, =0 {} other {# Empleo/Educación, } }{ da, plural, =0 {} other {# Domésticas, } }{ ral, plural, =0 {} other {# Recreación/Ocio, } }{ vwaca, plural, =0 {} other {# Voluntariado, } }{ other, plural, =0 {} other {# Otro} }"
          }
        }
      },
      "buttons": {
        "trip-notes": {
          "surveyName": "TimeUseSurvey",
          "not-filled-in-label": {
            "en": "Add Activity",
            "es": "Añadir actividad"
          }
        },
        "place-notes": {
          "surveyName": "TimeUseSurvey",
          "not-filled-in-label": {
            "en": "Add Activity",
            "es": "Añadir actividad"
          }
        }
      },
      "trip-labels": "ENKETO"
    },
    "display_config": {
        "use_imperial": false
    },
    "profile_controls": {
        "support_upload": true,
        "trip_end_notification": true
    }
}
```

----