# #179  fix signed to unsigned conversion warning [Merged]

> Username: timmaraju  
> Created at: 2021-01-25 20:10:46 UTC  
> Updated at: 2022-02-13 02:17:48 UTC  
> Merged at: 2022-02-12 12:53:38 UTC  
> Closed at: 2022-02-12 12:53:38 UTC  
> Url: https://github.com/boostorg/date_time/pull/179  

MSVC 19.28.29336.0 throws the following warning when  
boost::date_time::gregorian_date is called.  
  
warning C4365: 'initializing': conversion from 'int' to 'unsigned long', signed/unsigned mismatch  
warning C4365: const boost::posix_time::ptime Since1970(boost::gregorian::date(1970U, 1U, 1U));  
warning C4365:                                                                                ^  
<path>\include\boost\date_time\date.hpp(72,35): message : while evaluating constexpr function 'boost::date_time::gregorian_calendar_base<boost::gregorian::greg_year_month_day,unsigned int>::day_number'  
<path>\include\boost\date_time\date.hpp(72,35): message :       : days_(calendar::day_number(ymd_type(y, m, d)))

---

## Comment 1

> Username: jeking3  
> Created_at: 2022-02-12 02:15:34 UTC  
> Url: https://github.com/boostorg/date_time/pull/179#issuecomment-1036948341  

@timmaraju rebase on develop for a complete CI scan

---

## Comment 2

> Username: JeffGarland  
> Created_at: 2022-02-12 12:53:31 UTC  
> Url: https://github.com/boostorg/date_time/pull/179#issuecomment-1037217427  

@jeking3 I think this one is safe enough -- I'm just going to merge it.

---

## Comment 3

> Username: jeking3  
> Created_at: 2022-02-12 21:02:07 UTC  
> Url: https://github.com/boostorg/date_time/pull/179#issuecomment-1037477060  

@JeffGarland yes the only one that didn't pass was Coverity Scan and that's because it came from a fork.  I need to add more logic to prevent it from running unless it's originating in the repo.

---

## Comment 4

> Username: jeking3  
> Created_at: 2022-02-13 02:17:48 UTC  
> Url: https://github.com/boostorg/date_time/pull/179#issuecomment-1037679957  

@JeffGarland sorry my mistake; I forgot to add the required secrets to the repo - done. :)  I restarted the build job.

---
