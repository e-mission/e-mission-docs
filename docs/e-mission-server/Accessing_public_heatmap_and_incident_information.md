There are three main public API calls exposed by the e-mission API server. All
of them share the following characteristics:
- they are `HTTP POST` calls
- they do not currently require an API key for access. If we get popular enough
  to be the target for DDOS attacks, we will revisit this decision.
- they take a time range as input. The time range can be specified either in
  UTC, as a range, or in local time, as a filter.

These APIs are all currently very slow, because the prototype system is running
on a single AWS host. The performance will improve after Nov 1st, when I split the
functionality among multiple hosts.

The three public API calls are:
- `/result/heatmap/pop.route/<time_type>`
- `/result/heatmap/incidents/<time_type>`
- `/result/metrics/<time_type>`

The `<time_type>` is either `timestamp` or `local_date`. The actual query range
is specified in POST fields.

Without any further ado, here are some examples of how to use these
calls. I've chosen python as the language for demonstrating them since most
data scientists are familiar with it, but any language that can send http post
requests will work as well.

### Travel point queries ###

#### Retrieving data from 2nd Oct to 3rd Oct across all modes and the entire world ####

```
$ ipython
Python 2.7.11 |Anaconda 2.0.1 (x86_64)| (default, Dec  6 2015, 18:57:58)
Type "copyright", "credits" or "license" for more information.

IPython 4.2.0 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: import arrow

In [2]: start_ts = arrow.get("2017-10-02").timestamp

In [3]: end_ts = arrow.get("2017-10-03").timestamp

In [4]: post_fields = {
        "modes": None,
        "start_time": start_ts,
        "end_time": end_ts,
        "sel_region": None
}

In [5]: import requests

In [6]: result = requests.post("https://e-mission.eecs.berkeley.edu/result/heatmap/pop.route/timestamp", json=post_fields)

In [7]: result.status_code
Out[7]: 200

In [8]: result.json()['lnglat'][0:5]
Out[8]:
[[-117.07978918462588, 32.769079182443484],
 [-122.26395704827273, 37.87331020231461],
 [-110.77774474290837, 35.15781189961038],
 [-110.77774474290837, 35.15781189961038],
 [-110.77774474290837, 35.15781189961038]]

In [9]: len(result.json()['lnglat'])
Out[9]: 16607
```

#### Modifying the prior query to only retrieve walk and bike data ####

```
In [10]: post_fields["modes"] = ["WALKING", "ON_FOOT", "BICYCLING"]

In [11]: result = requests.post("https://e-mission.eecs.berkeley.edu/result/heatmap/pop.route/timestamp", json=post_fields)

In [12]: result.status_code
Out[12]: 200

In [13]: len(result.json()['lnglat'])
Out[13]: 3312
```

#### Modifying the prior query to only retrieve data around Hearst Street in Berkeley, CA ####

```
In [15]: result = requests.post("https://e-mission.eecs.berkeley.edu/result/heatmap/pop.route/timestamp", json=post_fields)

In [16]: len(result.json()['lnglat'])
Out[16]: 112
```

### Incident queries ###

Incident queries are very similar to heatmap queries, only sent to a different URL. Some other differences are:
- they don't support modes
- since they are user-reported and not collected in the background, there are
  typically few of them.


#### Retrieving all incidents from June to Oct ####

```
$ ipython
Python 2.7.11 |Anaconda 2.0.1 (x86_64)| (default, Dec  6 2015, 18:57:58)
Type "copyright", "credits" or "license" for more information.

IPython 4.2.0 -- An enhanced Interactive Python.
?         -> Introduction and overview of IPython's features.
%quickref -> Quick reference.
help      -> Python's own help system.
object?   -> Details about 'object', use 'object??' for extra details.

In [1]: import arrow

In [2]: start_ts = arrow.get("2017-06-01").timestamp

In [3]: end_ts = arrow.get("2017-10-01").timestamp

In [4]: post_fields = {
        "start_time": start_ts,
        "end_time": end_ts,
        "sel_region": None
}

In [5]: import requests

In [7]: result = requests.post("https://e-mission.eecs.berkeley.edu/result/heatmap/incidents/timestamp", json=post_fields)

In [8]: result.status_code
Out[8]: 200

In [9]: len(result.json()["incidents"])
Out[9]: 42

In [10]: result.json()["incidents"][0:5]
Out[10]:
[{u'fmt_time': u'2017-06-09T10:51:41-07:00',
  u'loc': {u'coordinates': [-122.06568002700806, 37.36993549376299],
   u'type': u'Point'},
  u'local_dt': {u'day': 9,
   u'hour': 10,
   u'minute': 51,
   u'month': 6,
   u'second': 41,
   u'timezone': u'America/Los_Angeles',
   u'weekday': 4,
   u'year': 2017},
  u'stress': 0,
  u'ts': 1497030701},
 {u'fmt_time': u'2017-06-09T10:04:41-07:00',
  u'loc': {u'coordinates': [-122.07375526777469, 37.38112434837758],
   u'type': u'Point'},
  u'local_dt': {u'day': 9,
   u'hour': 10,
   u'minute': 4,
   u'month': 6,
   u'second': 41,
   u'timezone': u'America/Los_Angeles',
   u'weekday': 4,
   u'year': 2017},
  u'stress': 0,
  u'ts': 1497027881},
....
```

This query can be also modified to return incidents for a particular area. Note that incidents cannot be filtered by mode, so any mode field in the query is ignored.