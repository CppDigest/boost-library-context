# #49 [Ticket #12144] Don't use defined in a macro definition. [Closed]

> Username: Romain-Geissler-1A  
> Created at: 2016-11-27 19:11:13 UTC  
> Updated at: 2017-10-05 00:37:06 UTC  
> Closed at: 2017-05-28 19:37:12 UTC  
> Url: https://github.com/boostorg/serialization/pull/49  

Hi,  
  
This should fix https://svn.boost.org/trac/boost/ticket/12144 (tested with clang 3.9 which doesn't throw any warning/error anymore).  
  
Ok for the trunk ?  
  
Cheers,  
Romain

---

## Comment 1

> Username: Romain-Geissler  
> Created_at: 2017-09-20 19:08:03 UTC  
> Url: https://github.com/boostorg/serialization/pull/49#issuecomment-330950921  

@robertramey You decided to close this PR with commit https://github.com/boostorg/serialization/commit/0c8a1d615a300c61c0864c4033c19f4c62895c31, but I don't quite see how this commit fixes the issue described here. Shall we re-open this PR ?

---

## Comment 2

> Username: robertramey  
> Created_at: 2017-10-05 00:37:06 UTC  
> Url: https://github.com/boostorg/serialization/pull/49#issuecomment-334327648  

Hmm - I'm fix this again

---
