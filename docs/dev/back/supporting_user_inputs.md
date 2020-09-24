# Supporting user inputs #

One of the characteristics of a mobilityscope is that it should support user
inputs in addition to sensed data and the associated inferences. The data that
we have seen for this typically falls into three categories.

1. **Data not directly related to sensed values**: e.g. incident reports
1. **Data providing additional information about sensed values**: e.g. information on how a particular trip was planned
1. **Data providing ground truth for analysis performed from sensed data**: e.g. confirmation of mode or purpose

## High level design ##

For (3) in particular, it is interesting to think through how to store the values. The naive approach would be to store the results into the analysed values - for example, a trip whose mode is _corrected_ by a user can have its mode field set. But this conflicts with the need to allow the analysis results to be re-generated for reproducibility. If the mode for a trip is corrected, and then the trip is deleted, then the mode information is also lost. More details on the debate around the decision are in [#516](https://github.com/e-mission/e-mission-server/issues/516).

Therefore, we decided that all user inputs are immutable. They will be stored as separate objects and can be combined with analysis results as needed.

## External Surveys v/s Custom Objects ##

Given that user inputs are to be stored separately from analysis results anyway, there are two options for collecting them.

### External surveys ###
e-mission supports hooking up to surveys created using web survey platforms such as qualtrics, survey monkey or google forms. In order to link the results to the sensed data, selected fields in the survey can be populated with the user and trip information. If only the user UUID is populated, no PII is sent to the web survey platform. If both user and trip fields are populated, trip start and end times are sent. These do not appear to be highly privacy sensitive in the absence of location information, but you should probably disclose it in your consent document.

For more details on how to hook up external surveys, either in response to a button press, or in response to a notification, see [the more detailed instructions](../front/how_to_embed_an_external_survey_in_the_app.md)

- **Pro:** _Ease of use_. External surveys are easy to create since they have nice survey building tools
- **Con:** _Less flexibility_. Unless the survey platform provides an API to query for results, the communication is one-way. The survey results typically cannot be reflected back on the phone UI.

### Custom objects ###
e-mission supports storing user results directly on the server. They are stored as `manul/*` objects. Existing examples are `manual/mode_confirm` and `manual/purpose_confirm`. Since the objects are separate from any trips, you need to add information to the objects that allow them to be matched with any analysis results. You can look at the `mode_confirm` and `purpose_confirm` addition [issue](https://github.com/e-mission/e-mission-server/issues/532) and [pull request](https://github.com/e-mission/e-mission-server/pull/563) for more details. As you can see, for a given trip, the matching user input objects can be retrieved using `get_user_input_for_trip_object` in `emission/storage/decorations/trip_queries.py`.


If you need to add a new object type that is more complex than mode and purpose, you need to do book-keeping to register the object for use. The changes are pretty small in isolation, but there are quite a few of them. [This guide](adding_a_new_data_type.md) covers the details of adding new object types on both the phone and the server; you probably want to just add the objects as JSON.

- **Pro:** _Flexibility:_ Have the survey depend on the sensed value in complex forms. Show the completed trip, skip certain questions based on motion activity results/time of day, etc. Stored values can directly be used in analysis results and reflected back to the user.
- **Con:** _Complex to use:_ You have to support the objects on both the server and the phone and change the UI to read the values
