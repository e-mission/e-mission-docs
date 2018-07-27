# Requesting Data as a Collaborator
---

The consent document for e-mission (https://e-mission.eecs.berkeley.edu/consent) allows the platform owner (@shankari in this case) to share de-linked raw data with other collaborators for research.

> Time-delayed subsets of individual trajectory data, associated with their UUIDs but not email addresses, may by shared with collaborators, or released as research datasets to the community from time to time. If this is done, the time delay for sharing with collaborators will be at least one month, and the time delay for releasing to the community will be at least one year. Both collaborators and researchers will be asked to agree that they will publish only aggregate, non personally identifiable results, and will not re-share the data with others. 

This document provides the procedure to request access to such data.

## Setup GPG ##

We will send and receive data encrypted/signed using GPG.
1. The steps for creating a GPG keypair are at https://www.gnupg.org/gph/en/manual/c14.html.
1. Create a keypair and export it.
1. Send me (@shankari, shankari@eecs.berkeley.edu) the public key via email.

## Data request ##

Next, you need to formally request access by filling out a pdf form.

1. I will send you an encrypted version of the form you need to fill out and a copy of *my* public key.
1. Decrypt it using https://www.gnupg.org/gph/en/manual/x110.html.
1. Fill it out and sign it physically.
1. Also sign it electronically https://www.gnupg.org/gph/en/manual/x135.html
1. Encrypt it using my public key https://www.gnupg.org/gph/en/manual/x110.html and send it to me

If all of this works, we know that we have bi-directional encrypted communication over email. Make sure to encrypt any privacy sensitive information (e.g. subsets of data for debugging) that you send to me in the future.

## Data retrieval ##

As you can see from the consent document, you can get access to data that is time-delayed by 1 months.

1. I will upload an encrypted zip file with ~ 3 months of data to google drive and send you a link.
1. You need to decrypt it just like you decrypted the pdf form.
1. When unzipped, the data consists of multiple json files, one per user.
1. The data will typically contain both raw sensed data (e.g. `background/location`) and processed data (e.g. `analysis/cleaned_trip`)
1. Data formats for the json objects are at `emission/core/wrapper` (e.g. `emission/core/wrapper/location.py` and `emission/core/wrapper/cleanedtrip.py`)

Note that this data is very privacy-sensitive, so think through the answers carefully on the request form carefully and make sure that you follow them. Treat the data as you would like your data to be treated.

## Data analysis ##

While it is possible to analyse the raw data, it is large, so you may want to load it into a database to work with. That will also allow you to write code that is compatible with the server, so that we can more easily incorporate your analysis into the standard e-mission server.

### Install the server ###
Follow the README and install e-mission server locally on your own laptop.

### Load the data ###
Load the data into your local database. Since this data contains information from mutiple users, and you presumably want to retain the uuids, to correlate with other surveys that you might have performed, you should use the `load_multi_timeline_for_range.py` script. Since there are multiple files, the timeline will typically be a directory, and you should pass in the prefix. For example, if the user files are `all_users_sep_dec_2016/dump_0109c47b-e640-411e-8d19-e481c52d7130`, `all_users_sep_dec_2016/dump_026f8d13-4d7a-4f8f-8d35-0ec22b0f8f8b, ...,` you should run the following command line.

```
./e-mission-py.bash bin/debug/load_multi_timeline_for_range.py all_users_sep_dec_2016/dump_
```
As discussed earlier, these timelines also contain analysis results, so there is no need to re-run the intake pipeline. The script will automatically detect this and print the following line.

```
timeline contains analysis results, no need to run the intake pipeline
```

You can also remove the data by using `bin/purge_database_json.py`, which will delete everything, or `bin/debug/purge_multi_timeline_for_range.py`, which will only delete the entries in that timeline - e.g.

```
./e-mission-py.bash bin/debug/purge_multi_timeline_for_range.py all_users_sep_dec_2016
```

### Play with the data ###

An example ipython notebook that shows data access parameters is at
https://github.com/e-mission/e-mission-server/blob/master/Timeseries_Sample.ipynb

It has examples on how to access raw data, processed data, and plot points.
Please use the timeseries interfaces as opposed to direct mongodb queries wherever possible.
That will make it easier to migrate to other, more scalable timeseries later.

Again, data formats are at 
https://github.com/e-mission/e-mission-server/tree/master/emission/core/wrapper

Let me (@shankari) know if you have any further questions...
