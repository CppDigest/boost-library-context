# #58 Remove dependency on MPL [Merged]

> Username: pdimov  
> Created at: 2018-09-12 17:05:59 UTC  
> Updated at: 2018-09-12 20:39:53 UTC  
> Merged at: 2018-09-12 20:39:53 UTC  
> Closed at: 2018-09-12 20:39:53 UTC  
> Url: https://github.com/boostorg/optional/pull/58  

As far as I can see, `BOOST_OPTIONAL_CONFIG_USE_OLD_IMPLEMENTATION_OF_OPTIONAL` is never defined by default, and when I define it the tests fail anyway because they test functionality that is missing in the old implementation; but I confirmed that no new failures were introduced by this patch. :-)

---
