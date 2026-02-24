# #40 - Error in voronoi_diagram.hpp with recent Microsoft compiler [Open]

> Username: mw-michaelr  
> Created at: 2020-05-25 12:43:43 UTC  
> Updated at: 2020-09-14 13:39:30 UTC  
> Url: https://github.com/boostorg/polygon/issues/40  

The current MS Visual Studio (v16.6.0) compiler reports an error in voronoi_diagram.hpp:  
```  
boost/polygon/voronoi_diagram.hpp(266,17): error C7626: unnamed class used in typedef name cannot declare members other than non-static data members, member enumerations, or member classe  
```  
See https://docs.microsoft.com/en-us/cpp/overview/cpp-conformance-improvements?view=vs-2019#unnamed-classes-in-typedef-declarations  
  
As suggested in the link above, giving an arbitrary name to the struct would eliminate the error.

---

## Comment 1

> Username: malytomas  
> Created at: 2020-05-26 10:37:49 UTC  
> Url: https://github.com/boostorg/polygon/issues/40#issuecomment-633946746  

The commit 5cfcb6cad08dd5413f4a97af6a4fe7c35361de67 seems to have addressed this, but is not yet released?

---

## Comment 2

> Username: pwiecz  
> Created at: 2020-09-14 13:39:30 UTC  
> Url: https://github.com/boostorg/polygon/issues/40#issuecomment-692059395  

The commit works for me.  
  
Can a maintainer (providing there is one) please merge the above-mentioned commit to the master branch, please?  
Or can someone comment here with info why this cannot be merged?  
  
Thanks!
