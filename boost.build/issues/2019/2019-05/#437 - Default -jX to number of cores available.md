# #437 - Default -jX to number of cores available. [Closed]

> Username: grafikrobot  
> Created at: 2019-05-12 15:17:57 UTC  
> Updated at: 2021-03-31 01:09:09 UTC  
> Closed at: 2019-06-05 12:12:19 UTC  
> Url: https://github.com/boostorg/build/issues/437  

Given the prevalence of multi-core it's time we make use of them by default. Make the default, if no `-jX` is specified, to be as if `-j<core-threads>` was specified. To do this implement a `sysinfo.h` header with a `b2::system_information` class that can at minimum interrogate the number of cores and number of core-threads available.
