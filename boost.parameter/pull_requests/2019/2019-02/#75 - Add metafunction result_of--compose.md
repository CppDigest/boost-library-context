# #75 Add metafunction result_of::compose [Merged]

> Username: CromwellEnage  
> Created at: 2019-02-19 15:56:36 UTC  
> Updated at: 2019-04-27 18:42:59 UTC  
> Merged at: 2019-04-27 11:57:49 UTC  
> Closed at: 2019-04-27 11:57:49 UTC  
> Url: https://github.com/boostorg/parameter/pull/75  

Some Boost.Graph algorithms return one of their optional named function parameters.  If the user doesn't specify the parameter, then the default return type must necessarily be different.  This metafunction is needed to help facilitate computation of such a return type without resorting to decltype(), which not all compilers support.

---
