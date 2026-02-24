# #461 - Optimization: don't use std::string in metadata [Closed]

> Username: anarthal  
> Created at: 2025-03-28 19:03:11 UTC  
> Updated at: 2025-04-05 17:46:44 UTC  
> Closed at: 2025-04-05 17:46:44 UTC  
> Url: https://github.com/boostorg/mysql/issues/461  

`metadata` uses internally several instances of `std::string` to hold its string members. When performing benchmarks that query few rows with many fields, default-constructing these strings shows up in the profiler. Note that with `metadata_mode::minimal` (the default), no string copying takes place. Nonetheless, the cost is non-zero. We can change the strings to a `std::vector` and multiple offsets with no API change.
