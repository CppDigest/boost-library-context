# #186 - Request: introduce conversion between ptime and chrono system time [Open]

> Username: arthur-tacca  
> Created at: 2021-04-08 10:43:37 UTC  
> Updated at: 2021-04-08 10:53:36 UTC  
> Url: https://github.com/boostorg/date_time/issues/186  

Many thanks for this fantastic library!  
  
I think it would be useful if it included conversion functions between `std::chrono::system_clock::timepoint` and `boost::posix_time::ptime`. They're conceptually the same thing so it ought to be easy for application code to convert between them. It's straightforward for application writers to their own conversion functions of course, but that code has to go somewhere so ends up being a bit less convenient than if it were in the library.   
  
Personally I don't think the exact form of the functions is particularly important e.g. they could be proper conversion functions (constructor from time point and `operator timepoint()`), or methods (static `from_chrono_timepoint()` and non-static `to_chrono_timepoint()`) or free functions. My slight preference would be constructor and `to_chrono_timepoint()`. But any conversion operator should probably be explicit for backwards compatibility (e.g. for user code passing times to a function overloaded by ptime vs chrono time point).  
  
There are probably other types that could potentially be converted - certainly at least posix time interval and chrono's `system_clock::duration`. But I think the most important would be the time point itself.  
  
[Here is a related changeset from 2009](https://lists.boost.org/boost-commit/2009/04/15209.php) but I assume it's a bit out of date by now.
