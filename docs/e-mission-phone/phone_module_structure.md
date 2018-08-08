# Phone module structure
---

The phone code is based on ionic1 (https://ionicframework.com/docs/v1/), which in turn is based on angular1 (https://docs.angularjs.org/tutorial/).

In order to work with this code, you should be familiar with ionic1 and angular1. There are a ton of resources for working with both ionic1 and angular1 on the internet (e.g. https://codepen.io/search/pens?q=ionic&limit=all&type=type-pens) and we will not reproduce them here.

In addition, you can look at the branches, in particular, `cci-berkeley`, `opentoall` and `interscity` for code examples that you may want to incorporate. You can cherry-pick particular changes or ranges of changes, or just copy the changes manually. You should be familiar with basic git commands before you start this process.

In general, the structure of the phone code is modular, with directories delineating tabs or common functionality.
Both the templates and the javascript has individual directories. As of Nov 2017, these directories and rough mappings are:

- `common`: common trips, tour model 
- `control`: all the stuff around controlling data collection = the profile screen. Note that some of these files are copied over from plugins
- `diary`: the basic travel diary
- `goals`: the game
- `incident`: the end of trip prompt + particular location selection code
- `intro`: onboarding screens
- `metrics`: dashboard
- `recent`: debugging code, shows up in the profile
- `splash`: startup code

Note that we use services quite a bit in the javascript, so not all the code will be in the controllers.

Some tabs are special in the onboarding process - e.g. the metrics screen is the first one, the heatmap tab is where the user is redirected if she rejects consent. More details on how best to mess around with the tabs are in https://github.com/e-mission/e-mission-phone/wiki/Changes-needed-when-the-set-of-tabs-changes


