# #38 - Incorrect result of operator<< for time_point when GMT time zone is set [Open]

> Username: Pele44  
> Created at: 2019-02-26 09:53:03 UTC  
> Updated at: 2019-10-11 09:22:32 UTC  
> Url: https://github.com/boostorg/chrono/issues/38  

I discovered that Boost Chrono IO can occasionally add an incorrect time value (represented by a time _point object) to the stream. The problem occurs for the hour in which the local time shifts from standard to DST (summer time). Because the time is always displayed in GMT, it should not be affected by any changes in the local time zone, but in this case -- it is.  
Example app:  
```cpp  
#include <iostream>  
  
#include <boost/chrono/chrono.hpp>  
#include <boost/chrono/io/time_point_io.hpp>  
  
using namespace boost::chrono;  
  
static void print(const system_clock::time_point& tp)  
{  
  std::cout << time_fmt(timezone::utc) <<  
duration_cast<seconds>(tp.time_since_epoch()).count() << ": " << tp <<  
std::endl;  
}  
  
int main(int /*argc*/, char** /*argv*/)  
{  
  const time_t ProblematicTP = 1048989600; // 03/30/2003 @ 2:00am (UTC)  
  
  print(system_clock::from_time_t(ProblematicTP - 1));  
  print(system_clock::from_time_t(ProblematicTP));  
  print(system_clock::from_time_t(ProblematicTP + 1));  
  print(system_clock::from_time_t(ProblematicTP + 3600));  
  
  return 0;  
}  
```  
Compiled with boost 1.69, gcc 821 on fedora linux 29. Output:  
```  
1048989599: 2003-03-30 01:59:59.000000000 +0000  
1048989600: 2003-03-30 03:00:00.000000000 +0000  
1048989601: 2003-03-30 03:00:01.000000000 +0000  
1048993200: 2003-03-30 03:00:00.000000000 +0000  
```  
I tested this also on VS2013 on Windows 10 and the problem occurred there as well, but instead of jumping to 3 o’clock, it jumped to 1 o’clock. It seems that the incorrect time display is affected by the local time zone. Therefore the above example may give correct results in some time zones and incorrect in others.  
After using the debugger you can see that the problem arises from the use of the mktime function, which should operate only on local time, and in the code it does not: (boost 1.69, time_point_io.hpp:968).  
```  
if (gmtime_r(&t, &tm) == 0) failed = true;  
tm.tm_isdst = -1;  
(void)mktime(&tm);  
```  
After the call to gmtime_r, the tm struct has correct values (hour == 2), but after calling mktime its hour value equal to 3

---

## Comment 1

> Username: pwaehnert  
> Created at: 2019-06-06 11:20:38 UTC  
> Url: https://github.com/boostorg/chrono/issues/38#issuecomment-499454314  

The line 75 in the patch boost_modfile_be_161.txt provided by IBM for AIX (e.g. [here](https://www-01.ibm.com/support/docview.wss?uid=swg27042921)) sounds suspiciously relevant to this issue. Would you give it a try?

---

## Comment 2

> Username: viboes  
> Created at: 2019-06-08 06:33:32 UTC  
> Url: https://github.com/boostorg/chrono/issues/38#issuecomment-500100551  

Hi, I would like to review a pool request.   
  
Please, could you do it?

---

## Comment 3

> Username: pwaehnert  
> Created at: 2019-06-11 08:06:31 UTC  
> Url: https://github.com/boostorg/chrono/issues/38#issuecomment-500734187  

I'm not entirely sure whether I'm legally allowed to extract a patch from those files provided by IBM. On the other hand, it's only an one line diff. What's your stance about this issue?

---

## Comment 4

> Username: ccorn  
> Created at: 2019-10-10 12:54:03 UTC  
> Url: https://github.com/boostorg/chrono/issues/38#issuecomment-540562094  

From `<boost/chrono/io/time_point_io.hpp>`:  
~~~ c++  
if (gmtime_r(&t, &tm) == 0) failed = true;  
tm.tm_isdst = -1;  
(void)mktime(&tm);  
~~~  
The last two lines seem semantically wrong to me, particularly since [`mktime`] is for local time.  
Since the timezone to use is UTC, `tm.tm_isdst` should be set to zero, and the appropriate function would be [`timegm`] instead of `mktime`. See also #42.  
  
What was this `mktime`/`timegm` call supposed to achieve anyway? Ideally, `gmtime_r` provides all fields, and no `timegm` call should be necessary afterwards.  
  
If the purpose is to calculate values of `struct tm` fields neglected by some `gmtime_r` implementations, doing the calculations in a dedicated function (specialized for UTC and free of side effects) seems the better approach to me. `<boost/chrono/io/time_point_io.hpp>` already provides functions like `days_from_1jan(year, month, day)` for that.  
  
The `mktime` call has been added with the changeset 5eaf2be07364c3e2dd26c0811231160d742ba387 (SVN r86410) in 2013. Is there an associated issue thread somewhere?  
  
[`mktime`]: https://linux.die.net/man/3/mktime  
[`timegm`]: https://linux.die.net/man/3/timegm

---

## Comment 5

> Username: ccorn  
> Created at: 2019-10-11 09:22:31 UTC  
> Url: https://github.com/boostorg/chrono/issues/38#issuecomment-540988337  

Found [Trac \#9276 Output from a system_clock::time_point get a time_point that is one day later than expected](https://svn.boost.org/trac10/ticket/9276) associated with [SVN r86410](https://svn.boost.org/trac10/changeset/86410).
