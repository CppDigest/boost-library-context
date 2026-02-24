# #15 Move tests [Merged]

> Username: danieljames  
> Created at: 2016-07-01 08:11:44 UTC  
> Updated at: 2016-07-01 15:24:46 UTC  
> Merged at: 2016-07-01 15:24:46 UTC  
> Closed at: 2016-07-01 15:24:46 UTC  
> Url: https://github.com/boostorg/dynamic_bitset/pull/15  

The test system now requires that tests are run from a 'test' subdirectory, so this moves them into a subdirectory. The Jamfile in the root directory is there because the current `status/Jamfile.v2` still refers to it and will break if there isn't a file there, so once that has been updated, the file can be removed.

---
