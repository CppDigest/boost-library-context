# #1088 Update required CMake version to 3.8 [Merged]

> Username: Flamefire  
> Created at: 2025-10-01 09:42:41 UTC  
> Updated at: 2025-10-01 15:31:53 UTC  
> Merged at: 2025-10-01 15:12:15 UTC  
> Closed at: 2025-10-01 15:12:15 UTC  
> Url: https://github.com/boostorg/boost/pull/1088  

Most libraries are C++11 and require CMake 3.8 for the CMake feature or depend on libraries that do.  
Drop support for earlier CMake versions in the super project to reduce the number of versions to verify.  
  
This comes from my experiments in the tools/cmake repo testing a range of CMake versions.  
  
The list of libraries not supporting CMake < 3.8 keeps on growing so I'd say it doesn't make sense anymore to support that going forward. I'm currently at over 60 libraries that require CMake 3.8, there are others (around a dozen or so) that require even higher versions.  
  
(Incomplete) list of libraries incompatible with CMake 3.7:  
> outcome;asio;atomic;beast;bind;bloom;callable_traits;charconv;chrono;compat;container_hash;contract;crc;date_time;endian;fiber;filesystem;format;function;geometry;gil;graph;hana;hash2;heap;hof;iostreams;json;lambda2;lockfree;log;mqtt5;mysql;numeric/ublas;process;program_options;property_tree;random;ratio;redis;safe_numerics;serialization;smart_ptr;static_string;stl_interfaces;system;test;thread;type_erasure;typeof;unordered;url;uuid;variant2;wave;yap;hana;nowide;gil;sort;locale;cobalt;histogram;msm;parser;python;pfr;mysql  
  
What do you say @pdimov ?

---

## Comment 1

> Username: pdimov  
> Created_at: 2025-10-01 13:10:02 UTC  
> Url: https://github.com/boostorg/boost/pull/1088#issuecomment-3356225030  

Increasing to 3.8 makes sense to me. But do you really insist on claiming copyright just because you changed 3.5 to 3.8?

---

## Comment 2

> Username: Flamefire  
> Created_at: 2025-10-01 13:29:47 UTC  
> Url: https://github.com/boostorg/boost/pull/1088#issuecomment-3356336917  

Not insisting no, was mostly out of habit. Removed

---
