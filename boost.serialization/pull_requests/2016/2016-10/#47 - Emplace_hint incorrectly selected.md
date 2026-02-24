# #47 Emplace_hint incorrectly selected [Closed]

> Username: aminiussi  
> Created at: 2016-10-17 16:25:39 UTC  
> Updated at: 2017-05-23 22:12:56 UTC  
> Closed at: 2017-05-23 22:12:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/47  

There is no clear connection between the availability or unordered map and the presence of emplace_hint in map.  
At least one platform (intel compiler 15.0.x in CentOS 6.5 has the former and not the later.  
  
I am not sure what the existing code is trying to achieve, but revert to plain 'insert' until it is sorted out.

---

## Comment 1

> Username: robertramey  
> Created_at: 2016-11-02 21:44:44 UTC  
> Url: https://github.com/boostorg/serialization/pull/47#issuecomment-258008533  

according to http://en.cppreference.com/w/cpp/container/unordered_map emplace_hint should be supported by a conformation library implementation.  It's purpose is to improve performance when deserialization of unordered_map.  A better fix would be to make your change conditional on the intel compiler.

---

## Comment 2

> Username: robertramey  
> Created_at: 2016-11-02 22:29:30 UTC  
> Url: https://github.com/boostorg/serialization/pull/47#issuecomment-258019122  

I looked at this again.  I realize now that I misunderstood the appropriate usage of emplace.  It would be a good thing to use in the library from a performance standpoint.  But to make use of it one would have to change the library too much - and perhaps even the api.  So I'm replacing all usages of emplace with the traditional copy versions.  I don't expect this to be slower but I do expect it to improve portability for some non-conforming compilers.

---

## Comment 3

> Username: aminiussi  
> Created_at: 2016-11-03 08:53:23 UTC  
> Url: https://github.com/boostorg/serialization/pull/47#issuecomment-258089575  

Just to be clear, my problem was that emplace_hint was used on a map (used during the instantiation), not on an unordered map.  
As a side note, the issue is not with the Intel compiler per se. On Linux, the Intel compiler uses the runtime and libraries/header of the installed g++, so on some current platform  that runtime can be quite old (CentOS 6.5 a very common platform for HPC cluster, it comes with gcc4.4.x, even recent versions like CentOS7 comes with 4.8.x, and Boost.MPI is mostly used in HPC, so...). In a perfect world, b2 would determined the available functionalities at configuration time (like CMake, AutoTool and I guess most similar tools) and produce a platform configuration header specific to a given configuration. But this is not specifically related with this library, it's just another b2 limitation.  
  
Thanks a lot !  
Regards.

---
