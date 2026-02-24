# #67 - Retire built-in and aging time zone database in favor of using IANA [Open]

> Username: jeking3  
> Created at: 2018-01-06 16:22:41 UTC  
> Updated at: 2021-08-21 08:33:51 UTC  
> Url: https://github.com/boostorg/date_time/issues/67  

### Problem ###  
  
If you apply timezone to a time in the past and that timezone changed the timezone specification to behave differently after a certain point, the existing timezone database does not encapsulate the older formats.  Therefore the result is wrong for times in the past where that timezone changed rules.  
  
### Solution ###  
  
The IANA time zone database is the most complete database available:  
  
https://www.iana.org/time-zones  
  
They have data, code, or combined releases.  Their time zone information contains the start and end time for each zone change - something that the built-in csv file in the data directory does not have.  
  
Recommend retiring the built-in time zone database in favor of using the IANA database and modifying the time zone code to apply time zones based on the valid time ranges for those zones.  Sounds like a fair amount of work.  
  
### Background ###  
  
https://svn.boost.org/trac10/ticket/10087  
https://svn.boost.org/trac10/ticket/596

---

## Comment 1

> Username: dpelle  
> Created at: 2018-03-21 17:16:02 UTC  
> Url: https://github.com/boostorg/date_time/issues/67#issuecomment-375023860  

@jeking3  wrote:  
  
> Sounds like a fair amount of work.  
  
Indeed. At least, before we retire the libs/date_time/data/date_time_zonespec.csv  
builtin file, can we at least provide script or instructions on how to generate that csv  
file from the latest IANA time zone database? That is probably an easier step  
than changing boost to read the IANA time zone database directly.  
  
I find it odd that it's up to every users to figure out how to update the  
the libs/date_time/data/date_time_zonespec.csv file.

---

## Comment 2

> Username: mclow  
> Created at: 2018-03-21 17:21:49 UTC  
> Url: https://github.com/boostorg/date_time/issues/67#issuecomment-375026019  

I, for one, would welcome such a script.

---

## Comment 3

> Username: JeffGarland  
> Created at: 2018-03-21 21:21:41 UTC  
> Url: https://github.com/boostorg/date_time/issues/67#issuecomment-375099950  

I agree the script is the better approach.  I realize that Howard and the standard are moving toward IANA, but I think it's useful to be able to subset the database for some environments that may not have access to IANA.  Many systems only need a handful of timezones and not the entire massive world database.  
  
Part of this gets into what will happen with date-time long term as Howard's core moves toward standardization.  My primary thought is that useful things that don't get standardized should reside here -- being able to work with a stripped down set of timezones is an example.

---

## Comment 4

> Username: jeking3  
> Created at: 2018-03-22 00:35:10 UTC  
> Url: https://github.com/boostorg/date_time/issues/67#issuecomment-375140416  

Regarding a script, there's already one!  See the Boost Trac tickets I referenced (that's partially why I referenced them).  Someone made one and linked it.  I did not check the script in to avoid any licensing issues.   I used the script to regenerate the database to get it into 1.67.0 though and it fixed a couple backlog items in Trac too.  The problem with this solution is it is woefully incomplete.  The database here has no temporal rules for changes.  For example when a country changes their daylight savings time on a given date, this database can't handle it.  If you ask date_time for a date it might tell you that a time in the past is EST when it really should be EDT, for example.  
  
We REALLY need to be able to parse AND fully understand the IANA database to be correct.

---

## Comment 5

> Username: dpelle  
> Created at: 2018-03-22 07:05:57 UTC  
> Url: https://github.com/boostorg/date_time/issues/67#issuecomment-375199758  

@jeking3 wrote:  
  
> We REALLY need to be able to parse AND fully understand the IANA database to be correct.  
  
Understood.  I found https://github.com/HowardHinnant/date after posting my previous comment. Howard's library looks like a very good replacement (I still have to use it). But for software already written with boost date_time, having an up-to-date (yet flawed) date_time_zonespec.csv file is still welcome. Many applications don't care about historical time zones, but only care about time zones now at current time and in the near future. So thanks for regenerating it.

---

## Comment 6

> Username: mclow  
> Created at: 2018-03-22 13:00:42 UTC  
> Url: https://github.com/boostorg/date_time/issues/67#issuecomment-375296264  

A proposal (by Howard) based on his library has been adopted for inclusion in C++20.  
This happened just last week.

---

## Comment 7

> Username: mheiskan  
> Created at: 2018-04-16 14:04:37 UTC  
> Url: https://github.com/boostorg/date_time/issues/67#issuecomment-381610754  

For those who cannot switch to Howard's library just yet, see also https://github.com/fmidev/smartmet-timezones and the "make csv" command in particular.

---

## Comment 8

> Username: JeffGarland  
> Created at: 2018-04-16 20:11:47 UTC  
> Url: https://github.com/boostorg/date_time/issues/67#issuecomment-381733642  

Mika -  
  
Any objection to pulling your code into boost date-time?  Not sure I'll need it, but I'd like to start charting a course for boost date-time users for C++20 and this would be part of it.  I"m also looking at things like whether posix_timezone can be supported by Howard's library (suspect there's an issue there but haven't had time to investigate).  Anyway, we really should be able to use these other types of timezones besides the tz database....  
  
Jeff

---

## Comment 9

> Username: mheiskan  
> Created at: 2021-08-21 08:33:51 UTC  
> Url: https://github.com/boostorg/date_time/issues/67#issuecomment-903082716  

Oh, I see I have missed your question! You're of course welcome to copy the code.
