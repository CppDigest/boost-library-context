# #9 Move tests [Merged]

> Username: danieljames  
> Created at: 2016-07-01 07:52:13 UTC  
> Updated at: 2016-07-01 15:23:52 UTC  
> Merged at: 2016-07-01 15:23:52 UTC  
> Closed at: 2016-07-01 15:23:52 UTC  
> Url: https://github.com/boostorg/concept_check/pull/9  

The test system now requires that tests are run from a 'test' subdirectory, so this moves them into a subdirectory. The Jamfile in the root directory is there because the current `status/Jamfile.v2` still refers to it and will break if there isn't a file there, so once that has been updated, the file can be removed.

---
