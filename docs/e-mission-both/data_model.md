# Data model #

One of the frequently asked questions by projects that use e-mission is "where
is the data model"? The answer is that it is stored in code. 

The main reason is that there is no dedicated documentation team to keep the
code and documentation up to date, and I didn't want to cause confusion by
having obsolete docs.

So the data model is represented by wrapper classes, stored in `emission/core/wrapper`.
- Each python wrapper class contains the list of fields, along with a brief description.
- Classes can inherit from other classes (e.g. `cleaned_trip` inherits from `trip`)
- `Entry` is a special class that represents an entry in the timeseries,
  including both `data` and `metadata`.
- A full list of classes, including a brief description of each type, is in
  `emission/core/wrapper/entry.py`
- The class represents the `data` part of an `entry`

The whole wrapper mechanism is based on the `attrdict` module, with some
extensions for validating not just which fields exist in the object, but
which are valid for a particular class.

```
In [1]: import emission.core.get_database as edb
Connecting to database URL localhost

In [2]: import emission.core.wrapper.entry as ecwe

In [3]: entry_dict = edb.get_timeseries_db().find_one()

In [4]: entry_dict["metadata"]["key"]
Out[4]: 'stats/server_api_time'

In [5]: entry = ecwe.Entry(entry_dict)

In [6]: entry.metadata.key
Out[6]: 'stats/server_api_time'

In [7]: type(entry)
Out[7]: emission.core.wrapper.entry.Entry

In [8]: type(entry.data)
Out[8]: emission.core.wrapper.statsevent.Statsevent

In [9]: entry.data.name
Out[9]: 'POST_/usercache/get'

In [10]: entry.data.reading
Out[10]: 0.41276121139526367
```

The wrappers also so some basic validation of attributes so that errors can be
caught at compile time. Even if that is a bit un-pythonic :)

```
In [11]: entry.beta
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-11-6d2537c58b13> in <module>()
----> 1 entry.beta

/Users/shankari/e-mission/e-mission-server/emission/core/wrapper/wrapperbase.py in __getattr__(self, key)
     73         return self._build(key, self[key])
     74     else:
---> 75         raise AttributeError("property %s is not defined for %s" % (key, self.__class__.__name__))
     76
     77   def _writable(self, key):

AttributeError: property beta is not defined for Entry

In [12]: entry.data.beading
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-12-500ae1bd7984> in <module>()
----> 1 entry.data.beading

/Users/shankari/e-mission/e-mission-server/emission/core/wrapper/wrapperbase.py in __getattr__(self, key)
     73         return self._build(key, self[key])
     74     else:
---> 75         raise AttributeError("property %s is not defined for %s" % (key, self.__class__.__name__))
     76
     77   def _writable(self, key):

AttributeError: property beading is not defined for Statsevent
```

Note that, in order to be reproducible, our data model is designed to be
**read-only**. This means that if you work on some data and generate some
output, that output is a separate object which you should store separately.
This ensures that we can blow away all analysis results at any time and
recreate them. Because of this, the wrappers are designed to be **read-only**
as well.

```
In [13]: entry.data.reading = 50000
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-13-53ad43c60534> in <module>()
----> 1 entry.data.reading = 50000

/Users/shankari/e-mission/e-mission-server/emission/core/wrapper/wrapperbase.py in __setattr__(self, key, value)
     95                 return super(WrapperBase, self).__setattr__(key, value)
     96         else:
---> 97             raise AttributeError("property %s is read-only" % key)
     98     else:
     99         raise AttributeError("property %s is not defined for %s" % (key, self.__class__.__name__))

AttributeError: property reading is read-only
```

In order to create a new entry, you can use createEntry with a data object.
There are examples of creating entries all over the analysis pipeline, but
here's an example similar to the one above.

We first create the data object

```
In [14]: import emission.core.wrapper.statsevent

In [15]: new_data = emission.core.wrapper.statsevent.Statsevent()

In [16]: new_data.name = "modified"

In [17]: new_data.reading = 5000

In [19]: new_data.ts = 12345678

In [20]: new_data.fmt_time = "this is the formatted_time"

In [21]: new_data
Out[21]: Statsevent({'name': 'modified', 'reading': 5000, 'ts': 12345678, 'fmt_time': 'this is the formatted_time'})
```

We can't set the old data!!

```
In [22]: entry.data
Out[22]: Statsevent({'reading': 0.41276121139526367, 'name': 'POST_/usercache/get', 'ts': 1481533136.761428})

In [23]: entry.data = new_data
---------------------------------------------------------------------------
AttributeError                            Traceback (most recent call last)
<ipython-input-23-bb4e1aee5054> in <module>()
----> 1 entry.data = new_data

/Users/shankari/e-mission/e-mission-server/emission/core/wrapper/wrapperbase.py in __setattr__(self, key, value)
     95                 return super(WrapperBase, self).__setattr__(key, value)
     96         else:
---> 97             raise AttributeError("property %s is read-only" % key)
     98     else:
     99         raise AttributeError("property %s is not defined for %s" % (key, self.__class__.__name__))

AttributeError: property data is read-only
```

We create a new entry using `create_entry`

```
In [24]: new_entry = ecwe.Entry.create_entry(entry.user_id, entry.metadata.key, new_data
    ...: )

In [25]: new_entry.data
Out[25]: Statsevent({'name': 'modified', 'reading': 5000, 'ts': 12345678, 'fmt_time': 'this is the formatted_time'})
```

