We were contacted by DFKI to create an emission tracking app for Android and iOS that While first also considering development of a new code base, we finally stayed on the path of contributing to the existing repository because it wouldn't be a big difference in time needed for development but we could contribute to a greater project and maybe also profit from future community updates.

The project is based on Cordova with Ionic, though it uses very old version and also lots of deprecated npm packages which makes a lot of newer Ionic features unavailable.

Documentation is located in e-mission-docs repository.
4 are the relevant repositories for the DFKI project:
1. e-mission-phone: Most of our development happens here. This is the code for all the frontend phone stuff. Since most of the logic happens on the server side or in dedicated Cordova plugins,that have their own respective repository, this really is mostly frontend.
2. E-mission-server: this is the server that processes all trips in the pipeline. We have our own instance of this server running on digital ocean but there isn't a DFKI specific version for this. The code for the server to be use is from the gis-mode-detection branch.
3. e-mission-docs: a central documentation repository for all e-mission projects and parts.
4. E-mission-devapp: the modification of phonegap devapp that will track location in background even when there is no app connected.

