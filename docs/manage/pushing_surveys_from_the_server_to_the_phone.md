# Pushing Surveys from the Server to the Phone
---

In addition to periodic surveys that are generated through local processing on the phone (https://github.com/e-mission/e-mission-phone/wiki/How-to-embed-an-external-survey-in-the-app), the server can also push surveys to the phone. These pushes can be periodic (e.g. every `x` hours, at certain times, e.g. at 21:00 UTC every day, or based on travel patterns from users).

The basic components of a server-generated survey push are:
1. the selection of users who receive the survey
2. the survey

Once the selection and the survey specs have been determined, the survey can be pushed to users using `bin/push/send_survey.py`

### Selection ###
The `bin/push/send_survey.py` help message lists the ways to select users. In addition to the standard email and uuid options, you can also specify a `query_spec` that identifies users based on their travel patterns. The existing codebase includes three sample queries (`emission/net/ext_service/push/sample.specs/query`):

1. `platform`: which matches queries for a particular platform (`android` v/s `ios`)
2. `point_count`: which counts the number of times a particular user has traversed a georegion
3. `trip_metrics`: which uses trip related information such as distance, count and duration

The code corresponding to these specs is at `emission/net/ext_service/push/query` - we welcome contributions of other query specs that you have found useful.

### Survey ###
The survey spec is used to specify the survey to push. The spec is similar to the one used on the phone (https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-phone/how_to_embed_an_external_survey_in_the_app.md) although a fun fact is that this came first :) Again, you specify the URL of the survey and the field that the uuid is populated into. 
As before, the examples are at `emission/net/ext_service/push/sample.specs/push`
