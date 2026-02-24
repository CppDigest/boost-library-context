# #43 - build break in graph_parallel - because of the removed libs/system/build/Jamfile.v2 [Open]

> Username: peter-joo  
> Created at: 2025-08-14 21:05:40 UTC  
> Updated at: 2025-10-13 23:04:13 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/43  

Problem comes using `Boost 1.89`.  
  
The build error is this:  
```  
error: Unable to find file or target named  
error: 'libs/system/build//boost_system'  
error: referred to from project at error:  
    '../libs/graph_parallel/test'  
error: could not resolve project reference  
    'libs/system/build'  
```  
  
I know that boost_system is header-only since `Boost 1.69`.  
Also the `libs/system/build/Jamfile.v2` exists in `Boost 1.88`, for compatibility reasons, I presume, but now it has been removed in `Boost 1.89`.  
  
So the reference of  
    `<library>../../system/build//boost_system`  
in  
    `libs/graph_parallel/test/Jamfile.v2`  
**now points to a nonexistent target**.  
  
I found two such invalid references in `Boost 1.89`:  
```  
libs/graph_parallel/test/Jamfile.v2:12:    : requirements <library>../build//boost_graph_parallel <library>../../system/build//boost_system ;  
libs/graph_parallel/example/Jamfile.v2:9:    <library>../../system/build//boost_system  
```  
  
**Summary**: `libs/system/build/Jamfile.v2` has been removed, without checking dependent Jamfiles.  
But any automated non-regression build covering `graph_parallel/test` would have caught it instantly :(

---

## Comment 1

> Username: pdimov  
> Created at: 2025-10-12 17:05:52 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/43#issuecomment-3394944564  

Looks like this can be fixed by merging develop to master.

---

## Comment 2

> Username: grafikrobot  
> Created at: 2025-10-12 21:48:57 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/43#issuecomment-3395391655  

https://github.com/boostorg/graph_parallel/issues/41

---

## Comment 3

> Username: jeremy-murphy  
> Created at: 2025-10-13 22:45:42 UTC  
> Url: https://github.com/boostorg/graph_parallel/issues/43#issuecomment-3399300896  

#42
