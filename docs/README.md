# Welcome to the e-mission Documentation!

[![Documentation Status](https://readthedocs.org/projects/e-mission/badge/?version=latest)](https://e-mission.readthedocs.io/en/latest/?badge=latest)

This repo contains all the documentation for the e-mission project, and [almost ALL THE ISSUES](https://github.com/e-mission/e-mission-docs/issues/). More information on the rationale behind the change can be found in the [migration reasons](contribute_to_the_doc/migration_reason.md).

There are some specialized READMEs [in the individual repositories](https://github.com/e-mission), but they are gradually being moved in here. This means that if you have any questions, you should first search here and if you don't find any [existing issues](https://github.com/e-mission/e-mission-docs/issues/), you should [file an issue here](https://github.com/e-mission/e-missiond-docs/issue).

### [e-mission web site](https://e-mission.eecs.berkeley.edu/)   
### [CODE on GitHub](https://github.com/e-mission)     
### [LICENCE BSD-3](https://github.com/e-mission/e-mission-docs/blob/master/docs/LICENSE.md)  
### Thanks to [@PatGendre](https://github.com/PatGendre) from [FabMob](https://github.com/fabmob/), you can now [Read The Docs](https://e-mission.readthedocs.io/en/latest/)

## What is e-mission
E-mission is an open source mobility platform developed at the [RISE](http://rise.cs.berkeley.edu/) and [BETS](https://bets.cs.berkeley.edu/) labs in the UC Berkeley EECS Department.  
E-mission includes a mobile application for Android and iOS, with user consent, automatically collects the user's travel patterns and sends them to the server so as to derive personal mobility information and analyses; depending on user consent for sharing his/her data, the data can also be used for aggregate mobility data studies. The application is also a tool for collecting information filled in by the user (such as incidents, ground truth information about his/her trip purpose and transportation mode, or answers to questions asked in external surveys).  
The server is a python web application, the data is stored in a mongoDB database; 
the client is a Cordova application for both Android and iOS.  
The application has been initially designed to be reused in research and academic projects either for conducting and as a good learning project for CS students. It is also freely reusable for any other user cases (LINK to list to be added here). 

## Gallery (a first glimpse at the app UI)
E-mission has been reused in several projects, here are [short videos showing different versions of the UI](https://nextcloud.damajash.org/s/MTE4y3tJeX4g6sG), which have been presented at the last TRB conference in January 2020 :   
- E-mission provides [personal mobility metrics including CO2 emissions](https://nextcloud.damajash.org/s/aFPRpfLYy9GPoe2) (MeCO2 a CO2 coach developed in Berlin by DFKI's OSLab)   
- E-mission can [let the end-user complete the mode and purpose](https://nextcloud.damajash.org/s/f2z28gK7e84WPWS) after trip completion   
- E-mission can [embark third-pary online surveys](https://nextcloud.damajash.org/s/CRZg4zQY2gKWMnF) (a feature developed by the University of New South Wales)   
- E-mission can be adapted to [mobility nudge experimentations like the Tripaware project](https://nextcloud.damajash.org/s/n2ESGrRYdCmSaX6) conducted at UC Berkley in 2018   

## Overview
Read these papers for understand context.

- [TRB paper describing how to use e-mission functionality](https://people.eecs.berkeley.edu/~shankari/emission_trb_2017_paper.pdf)  
- [The in-review paper on the e-mission architecture, please do not distribute](https://people.eecs.berkeley.edu/~shankari/em-arch.pdf)  

## roadmap
The next features and enhancement can be guessed from the [ISSUES](https://github.com/e-mission/e-mission-docs/issues)  
Project organisation and funding (to be completed)  
The current are described in [Develop/Future](dev/future/overview.md)   
