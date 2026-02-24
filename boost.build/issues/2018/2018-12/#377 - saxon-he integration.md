# #377 - saxon-he integration [Closed]

> Username: vinniefalco  
> Created at: 2018-12-19 17:28:35 UTC  
> Updated at: 2019-02-23 17:34:05 UTC  
> Closed at: 2019-02-23 17:34:04 UTC  
> Url: https://github.com/boostorg/build/issues/377  

It would be nice to have a saxon-he.jam configuration file similar to xsltproc.jam

---

## Comment 1

> Username: grafikrobot  
> Created at: 2018-12-19 18:45:15 UTC  
> Url: https://github.com/boostorg/build/issues/377#issuecomment-448702428  

Although possible and simple to have a saxon-he.jam tool it's not going to address building the BoostBook documentation; as that uses xsltproc.jam for building. You need to alter xsltproc.jam to support calling saxon-he instead of xsltproc.

---

## Comment 2

> Username: vinniefalco  
> Created at: 2018-12-19 19:11:26 UTC  
> Updated at: 2018-12-19 19:11:51 UTC  
> Url: https://github.com/boostorg/build/issues/377#issuecomment-448710588  

> it's not going to address building the BoostBook documentation  
  
This isn't meant to replace the current workflow for building the BoostBook documentation, it is only meant as an additional tool which a Boost library can opt-in to by invoking it explicitly from its own Jamfile. I guess I should have been more clear about that.  
  
Or to put it another way, I would like to use saxon-he instead, where I am **currently invoking xsltproc manually** from libs/beast/doc/Jamfile:  
https://github.com/boostorg/beast/blob/develop/doc/Jamfile#L125  
  
This should not affect any existing libraries

---

## Comment 3

> Username: grafikrobot  
> Created at: 2018-12-19 19:15:18 UTC  
> Url: https://github.com/boostorg/build/issues/377#issuecomment-448711702  

Thank you for the clarification and the example use case. One warning though.. Since saxan-he requires a JRE it means also supporting configuration support for a JRE. So it will take considerable time to come up with something in that regard.

---

## Comment 4

> Username: grafikrobot  
> Created at: 2019-02-23 17:34:04 UTC  
> Url: https://github.com/boostorg/build/issues/377#issuecomment-466671773  

Done in https://github.com/boostorg/build/commit/f1f02d7a2650c857f2c3236d33cbfba60702463e
