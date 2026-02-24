# #28 - Consider cleaning up BOOST_STL_INTERFACES_DOXYGEN usage [Closed]

> Username: badair  
> Created at: 2019-12-26 07:00:40 UTC  
> Updated at: 2020-01-01 19:30:06 UTC  
> Closed at: 2020-01-01 19:30:06 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/28  



---

## Comment 1

> Username: tzlaine  
> Created at: 2020-01-01 19:30:06 UTC  
> Url: https://github.com/boostorg/stl_interfaces/issues/28#issuecomment-570077436  

I'm going to leave these as they are.  This is the way several other Boost libs do things, too.  The replacement would be to use Doxygen-specific markup to exclude some things.  However, this does not work when you want to *include* some things conditionally.
