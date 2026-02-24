# #308 - Regression in `equivalent` in boost 1.85.0 over 1.83.0 (or am I doing something wrong?) [Closed]

> Username: emmenlau  
> Created at: 2024-04-29 08:37:24 UTC  
> Updated at: 2024-04-29 12:09:33 UTC  
> Closed at: 2024-04-29 11:26:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/308  

## Issue  
Since upgrading to boost 1.85.0 from 1.83.0 I get an exception in `equivalent`, and I am slightly at a loss as to why this is happening. Here is my code:  
```  
const bool vWeaklyCanonicalPathAExists = boost::filesystem::exists(vWeaklyCanonicalPathA);  
const bool vWeaklyCanonicalPathBExists = boost::filesystem::exists(vWeaklyCanonicalPathB);  
((vWeaklyCanonicalPathAExists || vWeaklyCanonicalPathBExists) && boost::filesystem::equivalent(vWeaklyCanonicalPathA, vWeaklyCanonicalPathB));  
```  
This used to work fine in boost 1.83.0 and several earlier versions. Since boost 1.85.0, I get an exception from `equivalent`. I can see that the origin of the exception is in `operations.cpp` in line 3823, where `err` is set to `2`:  
```  
int err = invoke_statx(AT_FDCWD, p1.c_str(), AT_NO_AUTOMOUNT, STATX_INO, &s1);  
```  
  
which is strange for me, because the path `p1` corresponds to `vWeaklyCanonicalPathA` which is `/data/Debug/ThriftHTTPWSServerTestDir/public/minus-.txt`, and which seems to exist, and is not a symbolic link:  
```  
#> ls -1 "/data/Debug/ThriftHTTPWSServerTestDir/public/minus-.txt"  
/data/Debug/ThriftHTTPWSServerTestDir/public/minus-.txt  
```  
  
The path `vWeaklyCanonicalPathB` does not exist, in case this is relevant. I was under the impression that `equivalent` can be used if at least one of the paths exists. My code starts working again if I require both paths to exist. This would be sufficient for my use case, since only then the equivalence-check is meaningful.  
  
But from the documentation I was under the impression that my current code should work, and I'm concerned as to why I get an exception. Am I doing something wrong, or does this error make sense?  
  
## Context  
The problem appears for me on Ubuntu 22.04.4 when boost is built from source with clang 18.1.3, and on Windows 11 when boost is built from source with clang-cl 16.0.6. I did not test other combinations.

---

## Comment 1

> Username: Lastique  
> Created at: 2024-04-29 11:26:03 UTC  
> Url: https://github.com/boostorg/filesystem/issues/308#issuecomment-2082470118  

> The path `vWeaklyCanonicalPathB` does not exist, in case this is relevant. I was under the impression that `equivalent` can be used if at least one of the paths exists.  
  
This was a change in Boost.Filesystem v4 (not v3) in 1.85.0, and is mentioned in [release notes](https://www.boost.org/users/history/version_1_85_0.html).  
  
In v4, `equivalent` indeed requires both files to exist, which is consistent with std::filesystem. In v3, when one file exists and the other one doesn't, `equivalent` would succeed and return `false`.

---

## Comment 2

> Username: emmenlau  
> Created at: 2024-04-29 12:09:31 UTC  
> Url: https://github.com/boostorg/filesystem/issues/308#issuecomment-2082562420  

Dear @Lastique , thanks a lot for the clarification, I see it in the v4 breaking changes document now! Sorry for not checking more carefully first!
