
# Time Zone Library

By default `datetime` objects have no time zone information.

You can add time zone to a date using the `tz` attribute in date/time creation methods.

```python
>>> from datetime import datetime, timezone
>>> datetime.now()
datetime.datetime(2025, 2, 23, 5, 8, 16, 403533)
>>> datetime.now(tz=timezone.utc)
datetime.datetime(2025, 2, 23, 13, 8, 31, 342555, tzinfo=datetime.timezone.utc)
```

You can create your own time zone by subclassing and specifying an offset. From Python 3.9 onwards you can use `zoneinfo.ZoneInfo` to access your computer's time zone database and specify the available time zones by name. The library will also mange when your time zone switches into daylight savings time.

```python
>>> from datetime import datetime, timezone
>>> from zoneinfo import ZoneInfo
>>> ZoneInfo("America/Vancouver")
zoneinfo.ZoneInfo(key='America/Vancouver')
>>> datetime.now(tz=ZoneInfo("Europe/Oslo"))
datetime.datetime(2025, 2, 23, 14, 13, 55, 401586, tzinfo=zoneinfo.ZoneInfo(key='Europe/Oslo'))
```

The `.astimezone()` function allows you to convert a `datetime` between time Zones

```python
>>> from datetime import datetime, timezone
>>> from zoneinfo import ZoneInfo
>>> release_date = datetime(2020, 10, 5, 3, 9, tzinfo=ZoneInfo("America/Vancouver"))
>>> release_date.astimezone(ZoneInfo("Europe/Oslo"))
datetime.datetime(2020, 10, 5, 12, 9, tzinfo=zoneinfo.ZoneInfo(key='Europe/Oslo'))
```

`zoneinfo` utilizes the IANA time zones defined on the host OS so it may vary from system to system. Example below if rom a Mac. The reason for the unusually high number is becuase the time zone database includes historical information. Some computers don't have this database in which case you will get an `ZoneInfoNotFoundError` exception. If you need to install the Python maintained version of this database you can use `python -m pip install tzdata`. If you are on Python pre 3.9 you can install it form the `backports` package for Python 3.6 to 3.8 using `python -m pip install backports.zoneinfo`

```python
>>> import zoneinfo
>>> len(zoneinfo.available_timezones())
597
```

# Best Practices When Using Time Zones

**Civil times** such as meetings, train schedules, concerts should use a time zone. It allows you to store a location along with the time stamp and support knowing what time zone user is in if they are traveling.

**Timestamps** should be naive and based on UTC. You should not have to figure out whether daylight savings time was in effect when looking at server logs.