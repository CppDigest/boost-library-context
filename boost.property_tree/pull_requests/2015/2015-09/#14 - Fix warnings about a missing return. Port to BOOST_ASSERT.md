# #14 Fix warnings about a missing return. Port to BOOST_ASSERT. [Merged]

> Username: Lastique  
> Created at: 2015-09-22 15:36:08 UTC  
> Updated at: 2016-02-10 10:52:59 UTC  
> Merged at: 2016-02-10 10:48:22 UTC  
> Closed at: 2016-02-10 10:48:22 UTC  
> Url: https://github.com/boostorg/property_tree/pull/14  

The pull request does the following fixes:  
- Silences gcc warnings "control reaches end of non-void function"  
- Ports code to `BOOST_ASSERT` instead of plain `assert`.  
- Adds a few missing includes.  
- Converts tabs to spaces.

---
