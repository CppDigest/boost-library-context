# #62 Fix ptree_serialization failing to build with boost1.74. [Closed]

> Username: xnox  
> Created at: 2020-12-13 14:29:18 UTC  
> Updated at: 2021-05-29 17:28:31 UTC  
> Closed at: 2021-05-29 17:28:31 UTC  
> Url: https://github.com/boostorg/property_tree/pull/62  

Otherwise bagel software fails to build from source, since boost::archive no longer defines library_version_type. Moved to boost::serialization.

---

## Comment 1

> Username: mbanck  
> Created_at: 2021-01-22 08:05:41 UTC  
> Url: https://github.com/boostorg/property_tree/pull/62#issuecomment-765218630  

Bagel had a fix for this applied today: https://github.com/qsimulate-open/bagel/commit/4c0ab21158dbe1ef4373cce5735cc1392146386c

---
