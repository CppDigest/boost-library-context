# #94 - to_posix_string() returns invalid timezone string [Open]

> Username: svekon-admin  
> Created at: 2018-12-28 22:55:48 UTC  
> Updated at: 2024-02-04 10:58:02 UTC  
> Url: https://github.com/boostorg/date_time/issues/94  

The following code  
`  
tz_database tz_db;  
tz_db.load_from_file("./date_time_zonespec.csv");  
time_zone_ptr nyc = tz_db.time_zone_from_region("Europe/Stockholm");  
auto s = nyc->to_posix_string();   
setenv("TZ", s.c_str(), 1);  
`  
will set TZ to "CET+01CEST+01,M3.5.0/02:00,M10.5.0/03:00" which will cause Stockholm local time to be 10:00:00 when UTC(GMT/London) time is 11:00:00 which clearly is incorrect.  
  
ANSI/IEEE Std 1003.1 section **8.3 Other Environment Variables** reads for **TZ** variable  
  
Offset             Indicates the value added to the local time to arrive at Coordinated Universal Time  
  
[gnu.org](https://www.gnu.org/software/libc/manual/html_node/TZ-Variable.html) reads the same  
It also says regarding offset  
> This is positive if the local time zone is west of the Prime Meridian and negative if it is east.  
>  
  
But Boost 1.69 [date-time](https://www.boost.org/doc/libs/1_69_0/doc/html/date_time/local_time.html) says  
  
> A posix_time_zone is unique in that the object is created from a Posix time zone string (IEEE Std 1003.1). A POSIX time zone string takes the form of:   
      "std offset dst [offset],start[/time],end[/time]" (w/no spaces).  
'std' specifies the abbrev of the time zone. 'offset' is the offset from UTC.  
     "PST-8PDT01:00:00,M4.1.0/02:00:00,M10.1.0/02:00:00"  
      "PST-8PDT,M4.1.0,M10.1.0"  
These two are actually the same specification (defaults were used in the second string). This zone lies eight hours west of GMT …”  
>  
If I add -8 to a PST local time I will NOT get GMT=UTC time!! It seems like somewhere here (most likely in to_posix_string()) is a huge bug since the Posix/GNU and Boost interpretation of the Posix timezone strings contradict each other.

---

## Comment 1

> Username: mclow  
> Created at: 2018-12-29 01:12:14 UTC  
> Url: https://github.com/boostorg/date_time/issues/94#issuecomment-450451041  

I suspect that this is another aspect of https://github.com/boostorg/date_time/pull/29, where what Boost.DateTime calls "POSIX" is really ISO 8601 which is the opposite of POSIX.  Even though this is clearly wrong, we have refrained from changing it due to concerns about breaking existing code.

---

## Comment 2

> Username: svekon-admin  
> Created at: 2018-12-29 07:03:58 UTC  
> Updated at: 2018-12-29 07:04:41 UTC  
> Url: https://github.com/boostorg/date_time/issues/94#issuecomment-450471388  

I fully understand the concern of breaking existing code which, since the function clearly returns bogus data, must have some workaround to revert the bug, as feeding the function result into a Posix system result in an incorrect time translation.  
But then another method needs to be added (i.e to_posix_string_v2()) which does what it should, and the documentation needs to be updated to reflect the fact that to_posix_string() does NOT return a Posix compliant timezone string. Rather an ISO8601'ich string.

---

## Comment 3

> Username: svekon-admin  
> Created at: 2019-03-10 10:34:51 UTC  
> Url: https://github.com/boostorg/date_time/issues/94#issuecomment-471275782  

And yet another issue with to_posix_strings()   
  
The 1.66 date_time_zonespec.csv returns for Europe/Stockholm  
> CET+01CEST+01,M3.5.0/02:00,M10.5.0/03:00  
  
But it’s not only the sign of the offset which is incorrect, the DST offset is also incorrect.  
  
Acc.to  [POSIX Environment variables for TZ](http://pubs.opengroup.org/onlinepubs/9699919799/basedefs/V1_chap08.html#tag_08)  
> offset Indicates the value added to the local time to arrive at Coordinated Universal Time  
  
So, the DST offset is NOT an offset to be added to the standard (STD) offset. It should be the complete offset to be added to local time to get UTC.  
  
A correct POSIX string should therefore read  
  
> CET-01CEST-02,M3.5.0/02:00,M10.5.0/03:00

---

## Comment 4

> Username: matswebjorn  
> Created at: 2024-02-04 10:58:01 UTC  
> Url: https://github.com/boostorg/date_time/issues/94#issuecomment-1925701811  

I'd like to rise this issue again with local_time::time_zone_ptr::to_posix_string() returning an incorrect Posix time zone string. Acc. to the 1.84.0 documentation this string is described as   
  
`A posix_time_zone is unique in that the object is created from a Posix time zone string (IEEE Std 1003.1). A POSIX time zone string takes the form of:  
      "std offset dst [offset],start[/time],end[/time]" (w/no spaces).  
'std' specifies the abbrev of the time zone. 'offset' is the offset from UTC. 'dst' specifies the abbrev of the time zone during daylight savings time. The second offset is how many hours changed during DST. 'start' and 'end' are the dates when DST goes into (and out of) effect. 'offset' takes the form of:  
      [+|-]hh[:mm[:ss]] {h=0-23, m/s=0-59}  
'time' and 'offset' take the same form. 'start' and 'end' can be one of three forms:  
`  
So let's see if this really works on a Linux system which has local-time set to "Europe/London" just to eliminate local time offsets, and then use Boost date-time to get a Posix timezone string and assign it to TZ to see how well it works  
  
1. Get current time settings in system  
```  
$ timedatectl  
               Local time: Sun 2024-02-04 08:49:42 GMT  
           Universal time: Sun 2024-02-04 08:49:42 UTC  
                 RTC time: Sun 2024-02-04 08:49:42  
                Time zone: Europe/London (GMT, +0000)  
System clock synchronized: yes  
              NTP service: active  
          RTC in local TZ: no  
$ echo $TZ  
  
$ date  
Sun Feb  4 08:50:21 UTC 2024  
  
```  
2. Load the latest date_time_zonespec.csv from Boost with tz_database::load_from_file()  
3. Get a time-zone object local_time::time_zone_ptr with tz_database::time_zone_from_region("Europe/Stockholm")  
4. Extract a Posix time-zone string with local_time::time_zone_ptr::to_posix_string() resulting in  
 `CET+01CEST+01,M3.5.0/02:00,M10.5.0/03:00`  
 Acc.to doc above Stockholm local-time should be UTC+1, and daylight saving UTC+1  
5. Assign string to environment variable TZ and check date  
```  
$ echo $TZ  
CET+01CEST+01,M3.5.0/02:00,M10.5.0/03:00  
  
$ date  
Sun Feb  4 07:51:43 CET 2024  
  
```  
So, UTC=08:49:42 and shortly after is localtime 07:51:43 meaning that **Stockholm is somewhere west of England out in the Atlantic** which clearly is quite incorrect. Daylight saving offset is also incorrect as Sweden definitely has daylight saving.  
  
This issue has not been fixed because "we have refrained from changing it due to concerns about breaking existing code." acc.to comment above.  
  
Is it better to leave an API which clearly is bogus and not do anyting? And not even mention this in the documentation???  
If the API can't be changed due to backward compatibility with users who has patched their systems with workarounds to get it working, then this API should be marked as depreciated, and another to_posix_string_v2() should be created and documentation updated accordingly.  
  
Boost can't have an API that clearly produces incorrect results regardless if old users has workarounds for this. Documentation needs to be updated and a new API added which produces expected result.  
  
All the changes required (attached)  
  
[boost-1.84-posix-time-zone.zip](https://github.com/boostorg/date_time/files/14156425/boost-1.84-posix-time-zone.zip)
