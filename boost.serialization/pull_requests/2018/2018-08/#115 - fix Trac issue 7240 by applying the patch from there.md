# #115 fix Trac issue 7240 by applying the patch from there [Merged]

> Username: jmairboeck  
> Created at: 2018-08-29 08:21:32 UTC  
> Updated at: 2019-04-08 06:17:05 UTC  
> Merged at: 2018-10-03 18:28:32 UTC  
> Closed at: 2018-10-03 18:28:32 UTC  
> Url: https://github.com/boostorg/serialization/pull/115  

We recently had the problem that is described in Trac ticket #7240 while trying to load a serialized binary archive that was created with boost version 1.40 (format version 5). This patch, which is taken from that Trac ticket, fixes this and allows such archives to be deserialized.

---
