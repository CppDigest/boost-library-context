# #300 - Eliminate unnecessary Appveyor jobs [Closed]

> Username: jeking3  
> Created at: 2025-01-03 21:12:01 UTC  
> Updated at: 2025-04-15 07:38:53 UTC  
> Closed at: 2025-04-15 07:38:52 UTC  
> Url: https://github.com/boostorg/unordered/issues/300  

boost::unordered appveyor jobs are really long, consuming about 4 hours of build time, for example:  
https://ci.appveyor.com/project/cppalliance/unordered/builds/51261314  
  
consider reducing dependence on appveyor and using more GitHub Actions jobs which allows for more parallelism  
The CMT repositories use Boost.CI and have been pruned down to 4 AppVeyor jobs per build.  
  
It looks like this repository is tracking Boost.CI fairly closely.  Some of the existing .appveyor.yml configurations may be redundant to the GHA jobs.

---

## Comment 1

> Username: joaquintides  
> Created at: 2025-04-15 07:38:52 UTC  
> Url: https://github.com/boostorg/unordered/issues/300#issuecomment-2804118516  

AppVeyor trimmed in #311.
