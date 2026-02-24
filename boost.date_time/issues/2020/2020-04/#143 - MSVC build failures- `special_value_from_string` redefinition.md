# #143 - MSVC build failures: `special_value_from_string` redefinition [Closed]

> Username: Lastique  
> Created at: 2020-04-03 23:46:03 UTC  
> Updated at: 2020-04-05 14:27:28 UTC  
> Closed at: 2020-04-05 14:27:28 UTC  
> Url: https://github.com/boostorg/date_time/issues/143  

Current develop (revision 4a4647635846e7aa56a0faa83b18afdec6907a76) fails to compile with any MSVC version, as can be seen [here](https://ci.appveyor.com/project/Lastique/log/builds/31940154/job/cpmjn5l979q1hfkf#L862):  
  
```  
.\boost/date_time/gregorian/parsers.hpp(26) : error C2375: 'boost::gregorian::special_value_from_string' : redefinition; different linkage  
        .\boost/date_time/gregorian/greg_month.hpp(142) : see declaration of 'boost::gregorian::special_value_from_string'  
```

---

## Comment 1

> Username: JeffGarland  
> Created at: 2020-04-03 23:57:04 UTC  
> Updated at: 2020-04-04 00:00:18 UTC  
> Url: https://github.com/boostorg/date_time/issues/143#issuecomment-608853648  

Ok I'll look at it, but if this is the case our system is broken -- the regressions have been running clean for 2 weeks...not to mention that that change ran clean on all the msvc compilers before committed.

---

## Comment 2

> Username: Lastique  
> Created at: 2020-04-04 09:12:33 UTC  
> Updated at: 2020-04-04 09:18:55 UTC  
> Url: https://github.com/boostorg/date_time/issues/143#issuecomment-609000364  

This problem is also present in master.  
  
It reproduces only when you include both `parsers.hpp` and `greg_month.hpp` in one TU. If it doesn't happen in Boost.DateTime, and `special_value_from_string` does not get called when `parsers.hpp` is included, then the issue remains undetected.  
  
I would suggest adding a test generator for header self-sufficiency ([here](https://github.com/boostorg/log/blob/ea0dd6171be10e068e0693f74a9afd0c520fa740/test/Jamfile.v2#L86) is an example and the associated [test template](https://github.com/boostorg/log/blob/develop/test/compile/self_contained_header.cpp)) and a test that includes all library headers at once. The latter would have shown this problem, the former would trigger if there are any missing includes. If you trust `boost/date_time.hpp` to truly include everything then the former will cover the latter.

---

## Comment 3

> Username: JeffGarland  
> Created at: 2020-04-04 14:59:03 UTC  
> Url: https://github.com/boostorg/date_time/issues/143#issuecomment-609041187  

Thanks for taking this on before I got to it :) This did need fixing before 1.73 and was clearly induced by my recent changes.
