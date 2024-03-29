# Welcome to the NREL OpenPATH Documentation!

[![Documentation Status](https://readthedocs.org/projects/e-mission/badge/?version=latest)](https://e-mission.readthedocs.io/en/latest/?badge=latest)

This repo contains all the documentation for the e-mission project, and [almost ALL THE ISSUES](https://github.com/e-mission/e-mission-docs/issues/). More information on the rationale behind the change can be found in the [migration reasons](contribute_to_the_doc/migration_reason.md).

There are some specialized READMEs [in the individual repositories](https://github.com/e-mission), but they are gradually being moved in here. This means that if you have any questions, you should first search here and if you don't find any [existing issues](https://github.com/e-mission/e-mission-docs/issues/), you should [file an issue here](https://github.com/e-mission/e-missiond-docs/issue).

## Getting started
- [NREL OpenPATH web site](https://nrel.gov/openpath)   
- [CODE on GitHub](https://github.com/e-mission)     
- [LICENCE BSD-3](https://github.com/e-mission/e-mission-docs/blob/master/docs/LICENSE.md)  
- Thanks to [@PatGendre](https://github.com/PatGendre) from [FabMob](https://github.com/fabmob/), you can now [Read The Docs](https://e-mission.readthedocs.io/en/latest/)

## What is NREL OpenPATH (née e-mission)
NREL OpenPATH (née e-mission) is an open source mobility platform developed at the [RISE](http://rise.cs.berkeley.edu/) and [BETS](https://bets.cs.berkeley.edu/) labs in the UC Berkeley EECS Department. It is currently maintained by the National Renewable Energy Laboratory (NREL) with support from the US Department of Energy (DOE) Vehicle Technologies Office (VTO).

E-mission includes a mobile application for Android and iOS, with user consent, automatically collects the user's travel patterns and sends them to the server so as to derive personal mobility information and analyses; depending on user consent for sharing his/her data, the data can also be used for aggregate mobility data studies. The application is also a tool for collecting information filled in by the user (such as incidents, ground truth information about his/her trip purpose and transportation mode, or answers to questions asked in external surveys).  
The server is a python web application, the data is stored in a mongoDB database; 
the client is a Cordova application for both Android and iOS.  
The application has been initially designed to be reused in research and academic projects either for conducting and as a good learning project for CS students. It is also freely reusable for [any other user cases](use/deployments.md). If you are using e-mission in your work, please submit a PR, or send shankari@eecs.berkeley.edu an email so that you can be added to this list.

### End to end apps
@PatGendre generated this list of closed source apps that are similar to e-mission. Since Patrick is from France, they have a strong European focus. If you would like to add other companies to this list, please [send a pull request](contribute_to_the_doc/CONTRIBUTING.md).

| Company       | Region       | Company     | Region       | Company       | Region     | Company       | Region     |
|---------------|--------------|-------------|--------------|---------------|------------|---------------|------------|
| [MOBIDOT](http://www.mobidot.nl/en/about_mobidot.php) | NL | [MotionTag](https://motion-tag.com/en/mobility/) | DE | [TravelVu](https://en.trivector.se/it-systems/travelvu/) | SE | [RMove](https://rmove.rsginc.com/) | US | 
| [radar.io](https://radar.io/documentation) | US | [BetterPoint](https://www.betterpoints.ltd/about-us/) | UK | [TrackAndKnow](https://trackandknowproject.eu/) | EU-wide |   |    |

### SDKs
Although e-mission is an end-to-end system, it is also modular. The sensing components are cordova plugins and can be incorporated into any app. The server modules are open source and should be able to process any data in the correct format. All the deployments so far, [with the exception of UW](https://github.com/e-mission/e-mission-docs/issues/501), have focused on customizing the custom app UI and have not really modified the plugin list. If you are interested in experimenting with plugin subsets, I am happy to work more closely with you. Please also [let the community know how it goes](https://github.com/e-mission/e-mission-docs/issues/505). @PatGendre also generated this comparative list of background location tracking SDKs. If you would like to add other SDKs to this list, please [send a pull request](contribute_to_the_doc/CONTRIBUTING.md).

| Project       | Open/Closed source | Notes |
|---------------|--------------------|-------|
| [Itinerum](https://www.itinerum.ca/)    | Open source        |app, not SDK. will merge in 2020, pending funding |
| [TransistorSoft Background Geolocation](https://github.com/transistorsoft/react-native-background-geolocation)| Closed source | Much richer set of interfaces, including react native and flutter |
| [Sentiance](https://docs.sentiance.com/sdk/) | Closed source | Includes trip and mode detection |
| [Radar.io](https://radar.io/documentation/sdk) | Closed source | the API seems to be limited to geofences, focused on retail? |
| [LocoKit](https://www.bigpaua.com/locokit/docs/) | Open source | iOS only, "ML based" |
| [moprim](https://www.moprim.com/products/) | Closed source | no public documentation |

## Gallery (a first glimpse at the app UI)
E-mission has been reused in several projects, here are [short videos showing different versions of the UI](https://nextcloud.damajash.org/s/MTE4y3tJeX4g6sG), which have been presented at the last TRB conference in January 2020 :   
- E-mission provides [personal mobility metrics including CO2 emissions](https://nextcloud.damajash.org/s/aFPRpfLYy9GPoe2) (MeCO2 a CO2 coach developed in Berlin by DFKI's OSLab)   
- E-mission can [let the end-user complete the mode and purpose](https://nextcloud.damajash.org/s/f2z28gK7e84WPWS) after trip completion   
- E-mission can [embark third-pary online surveys](https://nextcloud.damajash.org/s/CRZg4zQY2gKWMnF) (a feature developed by the University of New South Wales)   
- E-mission can be adapted to [mobility nudge experimentations like the Tripaware project](https://nextcloud.damajash.org/s/n2ESGrRYdCmSaX6) conducted at UC Berkley in 2018   
- E-mission can be integrated with external survey tools by [automatically filling in survey fields with sensed information](https://nextcloud.damajash.org/s/4CxeNpWDAiqSkpa). The survey prompt can [even be context sensitive](https://nextcloud.damajash.org/s/3qp8Mcwcy36HjSi).

## Overview
Read these papers for understand context.

- [TRB paper describing how to use e-mission functionality]([https://people.eecs.berkeley.edu/~shankari/emission_trb_2017_paper.pdf](https://www.researchgate.net/publication/327115169_e-mission_An_Open-Source_Smartphone_Platform_for_Collecting_Human_Travel_Data))  
- [The in-review paper on the e-mission architecture, please do not distribute](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2019/EECS-2019-88.html)  
- e-mission started as [Shankari's PhD thesis](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2019/EECS-2019-180.html)
- A proof of concept of [user private clouds in @njriasan's thesis](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2020/EECS-2020-130.html)
- A proof of concept for differentially private queries aggregating information over UPC [in @jackcsullivan's thesis](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2019/EECS-2019-69.html)

## roadmap
- The next features and enhancement can be guessed from the [ISSUES](https://github.com/e-mission/e-mission-docs/issues)  
- We have formed a [project commmittee](project_committee.md) to help guide the direction and roadmap
- The current large scale research features are described in [Develop/Future](dev/future/overview.md)   
