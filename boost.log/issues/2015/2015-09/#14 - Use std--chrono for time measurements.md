# #14 - Use std::chrono for time measurements [Closed]

> Username: ukreator  
> Created at: 2015-09-07 11:13:51 UTC  
> Updated at: 2015-09-07 11:22:58 UTC  
> Closed at: 2015-09-07 11:22:58 UTC  
> Url: https://github.com/boostorg/log/issues/14  

It would be great to get rid of boost::date_time or at least use it as an optional component. For programs using std::chrono from c++11 or boost::chrono this boost::date_time dependency is just a waste of space in final product binaries.

---

## Comment 1

> Username: Lastique  
> Created at: 2015-09-07 11:22:58 UTC  
> Url: https://github.com/boostorg/log/issues/14#issuecomment-138274148  

The dependency on Boost.DateTime won't be removed any time soon as it is used for obtaining timestamps. Chrono does not deal with that and as such it is not a suitable replacement.
