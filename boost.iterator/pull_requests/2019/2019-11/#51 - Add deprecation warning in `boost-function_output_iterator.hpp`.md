# #51 Add deprecation warning in `boost/function_output_iterator.hpp` [Merged]

> Username: Kojoley  
> Created at: 2019-11-23 13:48:36 UTC  
> Updated at: 2020-10-15 21:02:22 UTC  
> Merged at: 2020-05-01 23:01:08 UTC  
> Closed at: 2020-05-01 23:01:08 UTC  
> Url: https://github.com/boostorg/iterator/pull/51  



---

## Comment 1

> Username: gast128  
> Created_at: 2020-10-15 20:33:24 UTC  
> Updated_at: 2020-10-15 20:34:17 UTC  
> Url: https://github.com/boostorg/iterator/pull/51#issuecomment-709575012  

Just got Boost 1.74 and now I got a spam of these deprecated headers. I don't include them myselves. For example signals2 is still using this header in 'null_output_iterator.hpp' which is included in 'connection.hpp'. Couldn't Boost first clean up their sources with this deprecated inclusion?

---

## Comment 2

> Username: Lastique  
> Created_at: 2020-10-15 21:02:22 UTC  
> Url: https://github.com/boostorg/iterator/pull/51#issuecomment-709588298  

Boost.Signals2 should be fixed by https://github.com/boostorg/signals2/commit/fd27423fea5537bc857c1fa14bb0c25b994f77b3. There is no single Boost team, every library gets updated by its own maintainers, so it is possible that some things don't get updated in a timely manner before the release. Sorry for the inconvenience.

---
