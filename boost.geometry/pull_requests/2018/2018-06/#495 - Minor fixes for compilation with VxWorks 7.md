# #495 Minor fixes for compilation with VxWorks 7 [Merged]

> Username: kuhlenough  
> Created at: 2018-06-25 03:20:03 UTC  
> Updated at: 2018-06-28 02:40:30 UTC  
> Merged at: 2018-06-28 02:40:30 UTC  
> Closed at: 2018-06-28 02:40:30 UTC  
> Url: https://github.com/boostorg/geometry/pull/495  

Change \<host-os\> to \<target-os\>  in one Jamfile for cross-compilation.  
And change <proj to fully qualified <boost::geometry::srs::par4::proj  
to avoid confusing clang when compiling with the Dinkum STL  
which has inline proj() in global namespace

---

## Comment 1

> Username: awulkiew  
> Created_at: 2018-06-28 02:39:44 UTC  
> Url: https://github.com/boostorg/geometry/pull/495#issuecomment-400892640  

Thanks!

---
