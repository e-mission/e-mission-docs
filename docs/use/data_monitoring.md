# How to monitor data from an OpenPATH deployment

## Public dashboard
For basic summaries of the data, the public dashboard will be the easiest access point. The public dashboard is accessible to anyone on the internet, and contains a wide set of data visualizations updated daily. To view the dashboard, navigate to https://[your-deployment]-openpath.nrel.gov/public/. To remove charts from the display click the "x". To add charts, select the chart and month from the menu then click "add". Charts can be resized by dragging the lower right corner. Click the "more info" button where present to view the data shown in the chart in a table. Charts can be saved by right clicking and copying or saving the image to your local computer. 

## Admin dashboard
For more detailed representations and data export, the admin dashboard is provided to program administrators. You should have received and email inviting you to activate your account when the deployment was initialized, and you will be required to use two factor authentication. The dashboard may take a while to load, depending on the size of the deployment, but we are actively working on optimizations.  
There are a few sections in the administrator dashboard:
- landing page: shows recent data regarding onboarding and active users
- data: page which supports viewing and export of raw data, split into several tabs:
  - users: shows user data, including first and most recent trip date, number of trips, and number of labeled trips - very useful for monitoring progress and participation
  - trips: shows trip data, including distance, duration, and user labels - useful for trip analysis (see notes on data model)
  - trajectories: shows segments of trips - useful for highly detailed analysis including routes (see notes on data model)
- heat maps: shows areas of frequent travel on a map

## Manually
Data exported from the admin dashboard or from an individual's phone (json dump) can be analyzed independently. Feel free to look through the public dashboard repo for examples of working with the csvs from the admin dashboard, though there is not yet formalized support for this process. 

## Notes on data model
Below are a few notes on the data model highlighting the meaning and purpose of columns from frequently asked questions.

What are the different modes?
Note that user provided modes are likely to be the most accurate, but other mode types can be useful in filling data gaps.
- sensed mode: algorithmic mode determination based on data from phone sensors, includes walk, bike, in vehicle, and air/hsr
- predicted mode: algorithmic mode determination based on data from phone sensors and map matching, includes more specific vehicles such as bus, car, and train
- ble sensed mode: only applies to fleet deployments which have configured BLE beacons and shows the name associated with the beacon detected for the trip
- inferred mode/purpose: predicted mode and purpose based on an individual user's previous behavior (ie the user usually labels this location and speed as bike to work, so infer bike to work)
- confirmed mode/purpose: mode and purpose provided by the user