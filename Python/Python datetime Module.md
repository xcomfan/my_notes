
# Python datetime basics

Python standard date and time libraries:

* `calendar`
* `time`
* `datetime`

```python
>>> # Time
>>> import time
>>> time.time()
1740747736.7742121
>>> # Date & Time
>>> import datetime
>>> datetime.date(year=2012, month=12, day=21)
datetime.date(2012, 12, 21)
>>> datetime.time(hour=13, minute=25, second=42)
datetime.time(13, 25, 42)
>>> datetime.datetime(year=2012, month=12, day=21, hour=13, minute=25, second=42)
datetime.datetime(2012, 12, 21, 13, 25, 42)
>>> today = datetime.date.today()
>>> today
datetime.date(2025, 2, 28)
>>> now = datetime.datetime.now()
>>> now
datetime.datetime(2025, 2, 28, 5, 5, 47, 806524)
>>> when = datetime.time(now.hour, now.minute, now.second)
>>> datetime.datetime.combine(today, when)
datetime.datetime(2025, 2, 28, 5, 5, 47)
>>> # Formatted Output
>>> then = "12-21-2012 13:25:42"
>>> format_string = "%m-%d-%Y %H:%M:%S"
>>> datetime.datetime.strptime(then, format_string)
datetime.datetime(2012, 12, 21, 13, 25, 42)
```

Datetime parsing formatters

| Component                 | Code | Value |
| ------------------------- | ---- | ----- |
| Year, four digits         | `%Y` | 2022  |
| Month, zero padded        | `%m` | 01    |
| Date, zero padded         | `%d` | 31    |
| Hour, zero-padded 24 hour | `%H` | 14    |
| Minute, zero-padded       | `%M` | 42    |
| Second, zero-padded       | `%S` | 37    |
A popular library for parsing dates is `dateparser` (not a standard library)

```python
>>> import dateparser
>>> dateparser.parse("yesterday")
datetime.datetime(2025, 2, 27, 5, 17, 19, 705447)
>>> dateparser.parse("morgen") # tomorrow in german
datetime.datetime(2025, 3, 1, 5, 18, 16, 852936)
```

# Dealing with Time Zones

Python `datetime` objects come in two varieties. **Naive** which lack time zone information and **Aware** which do have time zone information. You need to be careful of which one you are dealing with as they don't mix and match nicely.

As of Python 3.09 the `zoneinfo` module is the place to go for time zone data.

```python
>>> from datetime import datetime
>>> now = datetime.now()
AttributeError: 'datetime.datetime' object has no attribute 'astimezeon'
>>> local_now = now.astimezone() # Will use your local timezone
>>> local_now
datetime.datetime(2025, 2, 28, 5, 24, 38, 994779, tzinfo=datetime.timezone(datetime.timedelta(days=-1, seconds=57600), 'PST'))
>>> local_tz = local_now.tzinfo
>>> tzname = local_tz.tzname(local_now)
>>> tzname
'PST'
>>> # Zone Info Database (Python >= 3.9)
>>> from zoneinfo import ZoneInfo
>>> london_tz = ZoneInfo("Europe/London")
>>> london_now = datetime.now(tz=london_tz)
>>> london_now # this is an aware object
datetime.datetime(2025, 2, 28, 13, 29, 59, 394904, tzinfo=zoneinfo.ZoneInfo(key='Europe/London'))
>>> london_now.tzname()
'GMT'
>>> datetime.utcnow() # don't use this its a legacy way
datetime.datetime(2025, 2, 28, 13, 31, 56, 466426)
>>> datetime.now(tz=ZoneInfo('UTC')) # this is the correct way
datetime.datetime(2025, 2, 28, 13, 32, 21, 915398, tzinfo=zoneinfo.ZoneInfo(key='UTC'))
>>> # Time zones are messy
>>> from datetime import timedelta
>>> hour = timedelta(hours=1)
>>> ny = ZoneInfo("America/New_York")
>>> ny.utcoffset(datetime(2006, 10, 31, 19, 00)) / hour
-5.0
>>> ny.utcoffset(datetime(2007, 10, 31, 19, 00)) / hour
-4.0```

# Date and Time Arithmetic

Python `datetime` module includes `timedelta` for simple data arithmetic. It is however limited to days, hours, minutes and seconds.

There are better third party libraries. One popular one is `dateutil`. You can install it with `python -m pip install python-dateutil`. It provides features such as:
* Relative deltas
* Recurrence
* Parsing
* Timezone
* Easter

```python
>>> # Using timedelta
>>> from datetime import datetime, timedelta
>>> now = datetime.now()
>>> now
datetime.datetime(2025, 2, 28, 5, 42, 42, 205114)
>>> tomorrow = timedelta(days=+1) # the + here is optional but helps with code readability
>>> now + tomorrow
datetime.datetime(2025, 3, 1, 5, 42, 42, 205114)
>>> yesterday = timedelta(days=-1)
>>> now + yesterday
datetime.datetime(2025, 2, 27, 5, 42, 42, 205114)
>>> delta = timedelta(days=+3, hours=-4)
>>> now + delta
datetime.datetime(2025, 3, 3, 1, 42, 42, 205114)
>>> now - delta
datetime.datetime(2025, 2, 25, 9, 42, 42, 205114)
>>> # biggest unit timedelta can handle is days
>>> # Using dateutil
>>> from dateutil.relativedelta import relativedelta
>>> tomorrow = relativedelta(days=+1)
>>> now + tomorrow
datetime.datetime(2025, 3, 1, 5, 42, 42, 205114)
>>> delta = relativedelta(years=+5, days=+9, hours=-4, minutes=-30)
>>> now + delta
datetime.datetime(2030, 3, 9, 1, 12, 42, 205114)
>>> # can give relativedetla 2 argumetns to calculate a difference
>>> relativedelta(now, now + delta)
relativedelta(years=-5, days=-8, hours=-19, minutes=-30)
```

# Further Info

Good references to review

* [Your Callendar Fallay Is...](https://yourcalendricalfallacyis.com)
* [Storing UTC is not a silver bullet](https://codeblog.jonskeet.uk/2019/03/27/storing-utc-is-not-a-silver-bullet/)
* [The complexity of time data programming](https://www.mojotech.com/blog/the-complexity-of-time-data-programming/)

Other third party date time libraries

* [pytz](https://pypi.org/project/pytz/)
* [Arro](https://arrow.readthedocs.io/en/latest/)
* [Pendulum](https://pendulum.eustace.io/)
* [Maya](https://github.com/timofurrer/maya)
* [dateparser](https://dateparser.readthedocs.io/en/latest/)
* [Delorean](https://pypi.org/project/Delorean/)
* [pywhen](https://pywhen.readthedocs.io/en/latest/)


# Copy paste of stack overflow with best practices for working with dates and time.

## Summary of answers and other data: (please add yours)

**Do:**

- Whenever you are referring to an exact moment in time, persist the time according to a unified standard that is not affected by daylight savings. (GMT and UTC are equivalent with this regard, but it is preferred to use the term UTC. Notice that UTC is also known as _Zulu_ or _Z_ time.)
- If instead you choose to persist a (past) time using a local time value, include the local time offset for this particular time from UTC (this offset may change throughout the year), such that the timestamp can later be interpreted unambiguously.
- In some cases, you may need to store _both_ the UTC time and the equivalent local time. Often this is done with two separate fields, but some platforms support a `datetimeoffset` type that can store both in a single field.
- When storing timestamps as a numeric value, use [Unix time](http://en.wikipedia.org/wiki/Unix_time) - which is the number of whole seconds since `1970-01-01T00:00:00Z` (excluding leap seconds). If you require higher precision, use milliseconds instead. This value should always be based on UTC, without any time zone adjustment.
- If you might later need to modify the timestamp, include the original time zone ID so you can determine if the offset may have changed from the original value recorded.
- When scheduling future events, usually local time is preferred instead of UTC, as it is common for the offset to change.  [See answer](https://stackoverflow.com/a/19627330/634824), and [blog post](http://www.creativedeletion.com/2015/03/19/persisting_future_datetimes.html).
- When storing whole dates, such as birthdays and anniversaries, do not convert to UTC or any other time zone.
    - When possible, store in a date-only data type that does not include a time of day.
    - If such a type is not available, be sure to always ignore the time-of-day when interpreting the value. If you cannot be assured that the time-of-day will be ignored, choose 12:00 Noon, rather than 00:00 Midnight as a more safe representative time on that day.
- Remember that time zone offsets are not always an integer number of hours (for example, Indian Standard Time is UTC+05:30, and Nepal uses UTC+05:45).
- If using Java, use [_java.time_](http://docs.oracle.com/javase/8/docs/api/java/time/package-summary.html) for Java 8 and later.
- Much of that _java.time_ functionality is back-ported to Java 6 & 7 in the [_ThreeTen-Backport_](https://www.threeten.org/threetenbp/)library.
- Further adapted for early Android (< 26) in the [_ThreeTenABP_](https://github.com/JakeWharton/ThreeTenABP) library.
- These projects officially supplant the venerable [Joda-Time](https://www.joda.org/joda-time/), now in [maintenance-mode](https://en.wikipedia.org/wiki/Maintenance_mode). Joda-Time, ThreeTen-Backport, [ThreeTen-Extra](https://www.threeten.org/threeten-extra/), _java.time_ classes, and [JSR 310](https://jcp.org/en/jsr/detail?id=310) are led by the same man, [Stephen Colebourne](https://stackoverflow.com/users/38896/jodastephen).
- If using [.NET](http://en.wikipedia.org/wiki/.NET_Framework), consider using [Noda Time](http://nodatime.org/).
- If using .NET without Noda Time, consider that `DateTimeOffset` is often a better choice than `DateTime`.
- If using Perl, use [DateTime](https://metacpan.org/module/DateTime).
- If using Python 3.9 or later, use the built-in [zoneinfo](https://docs.python.org/3/library/zoneinfo.html) for working with time zones. Otherwise, use [dateutil](https://dateutil.readthedocs.org/en/latest/) or [arrow](https://arrow.readthedocs.io/). The older [pytz](http://pytz.sourceforge.net/) library can generally be avoided.
- If using JavaScript, avoid using the older [moment.js](http://momentjs.com/) or [moment-timezone](http://momentjs.com/timezone/) libraries, as they are no longer actively maintained. See the [Moment.js project status](https://momentjs.com/docs/#/-project-status/) for more details. Instead, consider [Luxon](https://moment.github.io/luxon/#/), [date-fns](https://date-fns.org/), [day.js](https://day.js.org/), or [js-joda](https://js-joda.github.io/js-joda/).
- If using PHP > 5.2, use the native time zones conversions provided by `DateTime`, and `DateTimeZone` classes. Be careful when using `DateTimeZone::listAbbreviations()` - [see answer](https://stackoverflow.com/questions/2532729/daylight-saving-time-and-time-zone-best-practices/21198159#21198159). To keep PHP with up to date Olson data, install periodically [the timezonedb](http://pecl.php.net/package/timezonedb)PECL package; [see answer](https://stackoverflow.com/questions/2532729/daylight-saving-time-and-timezone-best-practices/7338519#7338519).
- If using C++, be sure to use a library that uses the properly implements the [IANA timezone database](http://www.iana.org/time-zones). These include [cctz](https://github.com/google/cctz), [ICU](http://site.icu-project.org/), and [Howard Hinnant's "tz" library](http://howardhinnant.github.io/date/tz.html). In C++20 the latter is adopted into the standard `<chrono>` library.
    - Do not use [Boost](http://www.boost.org/) for time zone conversions. While [its API](http://www.boost.org/doc/libs/1_44_0/doc/html/date_time/local_time.html#date_time.local_time.tz_database) claims to support standard IANA (aka "zoneinfo") identifiers, it [crudely maps them](https://github.com/boost-vault/date_time/blob/master/date_time_zonespec.csv) to POSIX-style data, without considering the rich history of changes each zone may have had. (Also, the file has fallen out of maintenance.)
- If using Rust, use [chrono](https://github.com/chronotope/chrono).
- Most business rules use civil time, rather than UTC or GMT. Therefore, plan to convert UTC timestamps to a local time zone before applying application logic.
- Remember that time zones and offsets are not fixed and may change. For instance, historically US and UK used the same dates to 'spring forward' and 'fall back'. However, in 2007 the US changed the dates that the clocks get changed on. This now means that for 48 weeks of the year the difference between London time and New York time is 5 hours and for 4 weeks (3 in the spring, 1 in the autumn) it is 4 hours. Be aware of items like this in any calculations that involve multiple zones.
- Consider the type of time (actual event time, broadcast time, relative time, historical time, recurring time) what elements (timestamp, time zone offset and time zone name) you need to store for correct retrieval - see "Types of Time" in [this answer](https://stackoverflow.com/questions/2532729/daylight-saving-time-and-timezone-best-practices/3269325#3269325).
- Keep your OS, database and application tzdata files in sync, between themselves and the rest of the world.
- On servers, set hardware clocks and OS clocks to UTC rather than a local time zone.
- _Regardless of the previous bullet point,_ server-side code, including web sites, should _never_ expect the local time zone of the server to be anything in particular.  [see answer](https://stackoverflow.com/a/30897258/634824).
- Prefer working with time zones on a case-by-case basis in your application code, rather than globally through config file settings or defaults.
- Use [NTP](http://en.wikipedia.org/wiki/Network_Time_Protocol) services on all servers.
- If using [FAT32](http://en.wikipedia.org/wiki/File_Allocation_Table#FAT32), remember that timestamps are stored in local time, not UTC.
- When dealing with recurring events (weekly TV show, for example), remember that the time changes with DST and will be different across time zones.
- Always query date-time values as [lower-bound inclusive, upper-bound exclusive](https://sqlblog.org/2011/10/19/what-do-between-and-the-devil-have-in-common) (`>=`, `<`).

**Don't:**

- Do not confuse a "time zone", such as `America/New_York` with a "time zone offset", such as `-05:00`. They are two different things. See [the timezone tag wiki](https://stackoverflow.com/tags/timezone/info).
- Do not use JavaScript's `Date` object to perform date and time calculations in older web browsers, as ECMAScript 5.1 and lower has [a design flaw](https://stackoverflow.com/a/16951442/634824) that may use daylight saving time incorrectly. (This was fixed in ECMAScript 6 / 2015).
- Never trust the client's clock. It may very well be incorrect.
- Don't tell people to "always use UTC everywhere". This widespread advice is shortsighted of several valid scenarios that are described earlier in this document. Instead, use the appropriate time reference for the data you are working with. (_Timestamping_ can use UTC, but future time scheduling and date-only values should not.)

**Testing:**

- When testing, make sure you test countries in the Western, Eastern, Northern and [Southern](https://www.betaarchive.com/wiki/index.php?title=Microsoft_KB_Archive/913551) hemispheres (in fact in each quarter of the globe, so 4 regions), with both DST in progress and not (gives 8), and a country that does not use DST (another 4 to cover all regions, making 12 in total).
- Test transition of DST, i.e. when you are currently in summer time, select a time value from winter.
- Test boundary cases, such as a timezone that is UTC+12, with DST, making the local time [UTC+13 in summer and even places that are UTC+13 in winter](http://en.wikipedia.org/wiki/UTC%2B13:00)
- Test all third-party libraries and applications and make sure they handle time zone data correctly.
- Test half-hour time zones, at least.

**Reference:**

- [The detailed `timezone` tag wiki page on Stack Overflow](https://stackoverflow.com/tags/timezone/info)
- [Olson database, aka Tz_database](http://www.iana.org/time-zones)
- [IETF draft procedures for maintaining the Olson database](https://datatracker.ietf.org/doc/html/draft-lear-iana-timezone-database-02.html)
- [Sources for Time Zone and DST](http://www.twinsun.com/tz/tz-link.htm)
- [ISO format (ISO 8601)](http://en.wikipedia.org/wiki/ISO_8601)
- [Mapping between Olson database and Windows Time Zone Ids, from the Unicode Consortium](http://www.unicode.org/cldr/charts/supplemental/zone_tzid.html)
- [Time Zone page on Wikipedia](http://en.wikipedia.org/wiki/Tz_database)
- [StackOverflow questions tagged `dst`](https://stackoverflow.com/questions/tagged/dst)
- [StackOverflow questions tagged `timezone`](https://stackoverflow.com/questions/tagged/timezone)
- [Dealing with DST - Microsoft DateTime best practices](http://msdn.microsoft.com/en-us/library/ms973825.aspx#datetime_topic6)
- [Network Time Protocol on Wikipedia](http://en.wikipedia.org/wiki/Network_Time_Protocol)

**Other:**

- Lobby your representative to end the abomination that is DST. We can always hope...
- Lobby for [Earth Standard Time](http://xkcd.com/1061/)