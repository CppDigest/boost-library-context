# #605 - Investigate opportunities for faster compilation [Open]

> Username: grisumbras  
> Created at: 2021-08-07 15:19:05 UTC  
> Updated at: 2021-08-07 15:19:05 UTC  
> Url: https://github.com/boostorg/json/issues/605  

[This experiment](https://github.com/s9w/cpp-lit) claims that including `boost/json.hpp` results in _build_ time of ~1500 ms, while including `nlohmann/json.hpp` — only ~900 ms. We could investigate this to see if there's anything we can do to decrease our compilation time footprint.  
  
Note: the tests were conducted on Windows and NTFS is notoriously slow at opening files. This might be the main culprit, as Boost.Json was tested in header-only non-standalone mode, which means that all of Json headers, all of Json implementation files, and many Boost.Container, Boost.Config etc. headers where opened. Meanwhile, nlohman was a single header.  
  
Meanwhile [these tests](https://artificial-mind.net/projects/compile-health/) claims that `boost/json.hpp"  _increases_ compilation by 271–553 ms while `nlohmann/json.hpp` by 526–852 ms. The tests are conducted on Linux and also don't link (and don't include implementation files).  
  
My current intuition is that the only thing we can actually do is create a special single merged header during builds.
