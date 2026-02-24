# #78 - enhancement request local_date_time::check_dst [Open]

> Username: jeshualin  
> Created at: 2018-02-28 22:17:58 UTC  
> Updated at: 2018-11-12 13:22:51 UTC  
> Url: https://github.com/boostorg/date_time/issues/78  

Release: 1.64  
At date_time/local_time/local_date_time.hpp:199 we have:  
  
            tz->dst_local_start_time(d.year()).date(),  
            tz->dst_local_start_time(d.year()).time_of_day(),  
            tz->dst_local_end_time(d.year()).date(),  
            tz->dst_local_end_time(d.year()).time_of_day(),  
            tz->dst_offset()  
  
We did some performance evaluation and found that caching d.year(), dst_local_start_time() and dst_local_end_time() improved performance substantially.

---

## Comment 1

> Username: mclow  
> Created at: 2018-02-28 23:15:40 UTC  
> Url: https://github.com/boostorg/date_time/issues/78#issuecomment-369417523  

Can you share your performance evaluation (general call patterns at least, more info is better)?

---

## Comment 2

> Username: jeshualin  
> Created at: 2018-03-01 03:02:59 UTC  
> Url: https://github.com/boostorg/date_time/issues/78#issuecomment-369459246  

Marshall,  
  
   Unfortunately this was done around 08/2015 (on boost 1.58) while we were  
exploring alternatives to RW{Date, Time, Zone}, and  
most performance statistics did not survive other than the following lines  
from my personal work log:  
  
          ** boost/date_time/local_time/local_date_time.hpp:  
      method local_date_time::check_dst  
      - cache some local results improves time2tm by 30%  
  
I believe this is referring to conversion from boost_date_time to struct  
tm.  If I recall correctly we were comparing each  
RW{Date, Time, Zone} method with the boost_date_time "equivalent" and  
were looking  
for ways to improve the performance of the  
latter.  We identified local_date_time::check_dst through gprof.  
  
   I was trying to upgrade to boost 1.64 and thus thought I'd make the  
suggestion before all the work done here on 1.58 is  
thrown down the drain.   In most applications time computations are not  
likely to take up a large chunk of the computation and  
so I suppose this is probably not going to make any difference in reality.  
  
   Since we are at this, I think I'd make another suggestion.  While  
working on the RWZoneSimple replacement, we realized that  
boost::posix_time_zone cannot be used directly as the former can have  
multiple daylight savings rules per time zone  
but the latter allows only one single rule.  To be able to use many of the  
boost::date_time utilities that take posix_time_zone& as  
one of the parameters the RWZoneSimple replacement has to be a derived  
class of posix_time_zone.  To facilitate deriving  
the new class from posix_time_zone, the following modifications were done:  
  
    (1) a new constructor, posix_time_zone_base(time_zone_names const&  
zone_names, time_duration_type const& utc_offset), was added.  
         This was done so that the base can be initialized properly without  
the daylight savings rule which now has to be dealt with  
         differently.  
    (2) made private members protected.  
  
If you are interested in these modifications and consider them worthwhile  
I'd be happy to provide them.  
  
Thank,  
  
- Chih-Long Lin  
  
On Wed, Feb 28, 2018 at 6:15 PM, Marshall Clow <notifications@github.com>  
wrote:  
  
> Can you share your performance evaluation (general call patterns at least,  
> more info is better)?  
>  
> —  
> You are receiving this because you authored the thread.  
> Reply to this email directly, view it on GitHub  
> <https://github.com/boostorg/date_time/issues/78#issuecomment-369417523>,  
> or mute the thread  
> <https://github.com/notifications/unsubscribe-auth/AjObg6_WT_c5dsmVYz66_RoypIymQSmlks5tZd4cgaJpZM4SXcJq>  
> .  
>

---

## Comment 3

> Username: jeking3  
> Created at: 2018-03-06 18:24:20 UTC  
> Url: https://github.com/boostorg/date_time/issues/78#issuecomment-370878922  

You are welcome to submit pull requests for any issue or enhancement.
