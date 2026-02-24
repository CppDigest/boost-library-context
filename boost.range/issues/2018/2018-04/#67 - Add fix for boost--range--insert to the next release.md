# #67 - Add fix for boost::range::insert to the next release [Closed]

> Username: elsid  
> Created at: 2018-04-11 11:39:28 UTC  
> Updated at: 2018-07-16 22:07:09 UTC  
> Closed at: 2018-07-16 22:07:09 UTC  
> Url: https://github.com/boostorg/range/issues/67  

Why [fix](https://github.com/boostorg/range/commit/474c62ab16e72153535883fd83c78dab62fc7967) for missing return in [boost::range::insert](https://github.com/boostorg/range/blob/b1d5d235148eb1736ce87eb34921a670c7cecf81/include/boost/range/algorithm_ext/insert.hpp#L36-L42) is still not in master? It was made 3 years ago, and it's kind of critical, can produce SIGILL on linux. Please, merge it into master to include into the next Boost release version.
