# Welcome to the e-mission Documentation!

This repo contains all the documentation for the e-mission project, and [almost all the issues](https://github.com/e-mission/e-mission-docs/issues/). More information on the rationale behind the change can be found in the [migration reasons](https://github.com/e-mission/e-mission-docs/blob/master/docs/migration_reason.md).

This page is an entry point to all the doc in the repo. An HTML version of the doc is to be generated in the coming weeks (Summer 2019).

There are some specialized READMEs in the individual repositories, but they are gradually being moved in here. This means that if you have any questions, you should first search here and if you don't find any existing issues, you should file an issue here.

## What is e-mission
E-mission is an open source software developed at UC Berkeley EECS Lab.  
E-mission includes a mobile application for Android and iOS collects automatically the users tracks and sends them to the server so as to derive personal mobility information and analyses ; depending on user consent for sharing his/her data, the data can also be used for aggregate mobility data studies. The application is also a tool for collecting information filled in by the user (such as incidents, ground truth information about his/her trip purpose and transportation mode, or answers to questions asked in external surveys).  
The server is a python web application, the data is stored in a mongoDB database; 
the client is a Cordova application for both Android and iOS.  
The application has been initially designed to be reused in research and academic projects either for conducting and as a good learning project for CS students. It is also freely reusable for any other user cases (LINK to list to be added here).  

e-mission web site : https://e-mission.eecs.berkeley.edu/   
code : https://github.com/e-mission   

### LICENCE
https://github.com/e-mission/e-mission-docs/blob/master/LICENSE  (BSD-3)

# OVERVIEW
Read these papers for understand context.
- [TRB paper describing how to use e-mission functionality](https://people.eecs.berkeley.edu/~shankari/emission_trb_2017_paper.pdf)  
- [The in-review paper on the e-mission architecture, please do not distribute](https://people.eecs.berkeley.edu/~shankari/em-arch.pdf)  

# INSTALL
MANUAL INSTALL : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-server/manual_install.md   
DEPLOYING YOUR OWN SERVER TO PRODUCTION : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-server/deploying_your_own_server_to_production.md  
CONFIGURING AUTHENTICATION : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-both/configuring_authentication.md  
MULTI LAYER STACK : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-server/multi_layer_stack.md  

# USE THE SOFTWARE (for the end-user)  
## how to install and use the app  
See the web site : https://e-mission.eecs.berkeley.edu/
Also, a draft app doc in French : https://docs.google.com/document/d/1X_FwiXjmWEFCLNhEXNa3-cD0FCjOURlLClCUiUoQ6PM/  

## use cases   
- list of project reusing e-mission  (to be added here)
- running instances  (to be added here)

# MANAGE THE APP AND DATA (for the "admin" or "support team")  
TROUBLESHOOTING : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-server/troubleshooting_tips_FAQ.md  

EVALUATE TRAVEL DATA COLLECTION:
https://github.com/e-mission/e-mission-docs/blob/master/docs/em-benchmark/evaluation_procedure.md  
REQUESTING DATA AS A COLLABORATOR
https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-server/requesting_data_as_a_collaborator.md  


# CONTRIBUTE TO THE DOC  
If you want to help improve the documentation, please check the CONTRIBUTING guide and submit a pull request. All the documentation is in markdown, so you can edit and generate pull requests directly from the browser. No CLI expertise needed!
https://github.com/e-mission/e-mission-docs/edit/master/CONTRIBUTING.md   

# ISSUES  
https://github.com/e-mission/e-mission-docs/issues  

# DEVELOPER
## architecture  
Back-end  
MODULE STRUCTURE : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-server/module_structure.md  
DATA MODEL : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-both/data_model.md  
MODE INFERENCE PIPELINE DESIGN : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-server/mode_inference_pipeline_design.md  
PIPELINE DETAILS : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-server/pipeline_details.md  

## tutorial  
WORKSHOP intro : https://github.com/e-mission/e-mission-docs/blob/master/docs/workshops/fall_2018/hands-on-overview.md  
WORKSHOP FALL 2018 : https://github.com/e-mission/e-mission-docs/blob/master/docs/workshops/fall_2018/logistics.md  

## front-end dev : how to improve the application   
PHONE MODULE STRUCTURE   
https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-phone/phone_module_structure.md  
CREATE A NEW CUSTOM CLIENT : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-phone/create_a_new_custom_client.md  
AFTER CREATING A NEW UI BRANCH : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-phone/you_have_a_branch_for_your_custom_client_now_what.md  

TROUBLESHOOTING : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-phone/troubleshooting_tips_faq.md   
(the last § relate concern more the admin/support than the dev, could be moved)  

SET-UP AN ENVIRONMENT FOR UI-ONLY CHANGES : https://github.com/e-mission/e-mission-docs/blob/master/docs/overview/quickstart.md  
FRONT-END FAQ : https://github.com/e-mission/e-mission-docs/blob/master/docs/overview/high_level_faq.md  

SMALL CHANGES  
1) CHANGING TEXT TRHU THE GITHUB UI : https://github.com/e-mission/e-mission-docs/blob/master/docs/overview/easiest_way_to_change_text.md
2) CHANGES NEEDED WHEN THE SET OF TABS CHANGES
https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-phone/changes_needed_when_the_set_of_tabs_changes.md
3) EMBED AN EXTERNAL SURVEY IN THE APP
https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-phone/how_to_embed_an_external_survey_in_the_app.md
4) SIMPLE CLIENT CHANGES : https://github.com/e-mission/e-mission-docs/blob/master/docs/tutorials/simple_client_changes.md
5) CHANGE SUMMARY AND CONSENT : https://github.com/e-mission/e-mission-docs/blob/master/docs/tutorials/summary_and_consent.md
6) CHANGE TRIP END PROMPT : https://github.com/e-mission/e-mission-docs/blob/master/docs/tutorials/change_trip_end_prompt.md
7) REMOVE TABS : https://github.com/e-mission/e-mission-docs/blob/master/docs/tutorials/remove_tab.md

HEATMAP AND INCIDENT PUBLIC web API : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-server/accessing_public_heatmap_and_incident_information.md  

## back-end dev :   

SMALL CHANGES  
https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-server/pushing_surveys_from_the_server_to_the_phone.md  

LARGE CHANGES (back+front)  
ADDING A NEW DATA TYPE : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-both/adding_a_new_data_type.md  
SUPPORTING USER INPUT : https://github.com/e-mission/e-mission-docs/blob/master/docs/e-mission-both/supporting_user_inputs.md  

## future work  
OVERVIEW  : http://github.com/e-mission/e-mission-docs/tree/master/docs/future_work  
1) PRIVACY : https://github.com/e-mission/e-mission-docs/blob/master/docs/future_work/privacy.md  
PRIVACY LITT. REVIEW : https://github.com/e-mission/e-mission-docs/blob/master/docs/future_work/differential_privacy_deep_dive_lit_review.md  
SECURE EXECUTION OPTIONS : https://github.com/e-mission/e-mission-docs/blob/master/docs/future_work/secure_execution_options.md  
2) ANALYSIS : https://github.com/e-mission/e-mission-docs/blob/master/docs/future_work/analysis.md  
3) SCALABALITY : https://github.com/e-mission/e-mission-docs/blob/master/docs/future_work/scalability.md  

UNANSWERED QUESTIONS : https://github.com/e-mission/e-mission-docs/blob/master/docs/future_work/unanswered_questions.md (split privacy and secure exec parts?)  

OTHERS  (discussion to be added here)
e-mission's API  
a mobile app mobility tracking SDK ?  
GIS integration  

# roadmap (to be added)
next features   
funding, next steps...  
