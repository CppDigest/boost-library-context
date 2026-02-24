# #6 Don't include stuff inside #pragma warning push/pop, include boost/config.hpp before testing BOOST_ macros. [Merged]

> Username: pgroke-dt  
> Created at: 2017-02-15 14:52:53 UTC  
> Updated at: 2018-09-25 17:56:32 UTC  
> Merged at: 2018-09-25 17:56:32 UTC  
> Closed at: 2018-09-25 17:56:32 UTC  
> Url: https://github.com/boostorg/throw_exception/pull/6  

I've moved some #include lines around + added #include <boost/config.hpp> lines to some files to avoid...  
- testing BOOST_* macros before config.hpp was included  
- including other header files inside #pragma warning push/pop pairs  
  
We're building with warnings=errors, and including e.g. <boost/config.hpp> for the first time inside a #pragma warning push/pop pair means all #pragma warning disable that are done in config.hpp will be undone by the pop. Which means our builds will fail because of some upgraded warning. Also I think it's generally not a good idea to include files inside a #pragma warning push/pop pair.  
  
And of course every file that tests/uses some BOOST_* macro should include <boost/config.hpp> first, to e.g. give Boost.Config and/or <config/user.hpp> the change to define that macro.

---
