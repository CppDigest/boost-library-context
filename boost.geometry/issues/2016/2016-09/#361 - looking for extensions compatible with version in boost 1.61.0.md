# #361 - looking for extensions compatible with version in boost 1.61.0 [Closed]

> Username: georgevanvenrooij  
> Created at: 2016-09-07 08:52:12 UTC  
> Updated at: 2017-04-04 14:51:45 UTC  
> Closed at: 2017-04-04 14:51:45 UTC  
> Url: https://github.com/boostorg/geometry/issues/361  

Hi,  
  
  I recently upgraded Boost to 1.61.0 and since my software uses some extensions, I came here looking for the matching set of extension files. For Boost 1.60.0 there was a develop-1.60.0 branch but this branch seems missing for Boost 1.61.0. Could you please point me to where I can find the correct set of extension files?

---

## Comment 1

> Username: awulkiew  
> Created at: 2016-09-22 11:33:53 UTC  
> Url: https://github.com/boostorg/geometry/issues/361#issuecomment-248878180  

You have to do some legwork for that unfortunately, i.e. find the latest commit in boost-1.61.0 tag:  
https://github.com/boostorg/geometry/commits/boost-1.61.0  
which is:  
https://github.com/boostorg/geometry/commit/4883808e589a04882aeacb3c1d8d7a34fb9d69c7  
and use its SHA to get the tree:  
https://github.com/boostorg/geometry/tree/4883808e589a04882aeacb3c1d8d7a34fb9d69c7  
Here are the extensions:  
https://github.com/boostorg/geometry/tree/4883808e589a04882aeacb3c1d8d7a34fb9d69c7/include/boost/geometry/extensions

---

## Comment 2

> Username: georgevanvenrooij  
> Created at: 2016-09-23 13:13:12 UTC  
> Url: https://github.com/boostorg/geometry/issues/361#issuecomment-249188877  

I tried this approach but when attempting to use the "dissolve" method, I got compile errors. I tried compiling my existing code based on the Boost 1.60.0 version of geometry and its extensions (from develop 1.60.0) with Boost 1.61.0 and each version of extensions since then and all of them fail to compile, unfortunately. I'm working on Windows 7 64-bit using VS2015 and compiling x64 code.

---

## Comment 3

> Username: awulkiew  
> Created at: 2017-03-06 01:08:38 UTC  
> Updated at: 2017-03-06 01:11:13 UTC  
> Url: https://github.com/boostorg/geometry/issues/361#issuecomment-284279738  

It's possible that there was a bug in the extensions. In the history of dissolve.hpp:  
  
https://github.com/boostorg/geometry/commits/develop/include/boost/geometry/extensions/algorithms/dissolve.hpp  
  
there is a fix some time after 1.61 release:  
  
https://github.com/boostorg/geometry/commit/ae2a127e9169c513fd0c8e973c1297dc6452bc52  
  
So it's possible that if you used this state of extensions it'd work for you:  
  
https://github.com/boostorg/geometry/tree/ae2a127e9169c513fd0c8e973c1297dc6452bc52/include/boost/geometry/extensions
