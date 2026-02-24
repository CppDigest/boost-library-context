# #24 boost::make_shared: use Constructor Forwarding on C++03 [Merged]

> Username: muggenhor  
> Created at: 2016-07-25 13:00:30 UTC  
> Updated at: 2016-09-10 11:35:52 UTC  
> Merged at: 2016-09-10 11:33:56 UTC  
> Closed at: 2016-09-10 11:33:56 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/24  

Currently, boost::make_shared doesn't perform constructor forwarding unless C++11 r-value references are supported. This prevents using of MOVABLE_BUT_NOT_COPYABLE types as arguments to constructors.  
  
The Boost.Move documentation describes [how to perform (limited) constructor forwarding](http://www.boost.org/doc/libs/1_59_0/doc/html/move/construct_forwarding.html). It's example is perfectly applicable to boost::make_shared.  
  
This PR implements constructor forwarding for make_shared as documented in Boost.Move's documentation.  
  
With this change the difference between the code supporting r-value references but not supporting variadic templates became the type signature and the call to boost::detail::sp_forward and boost::forward. The first difference was eliminated by using the macro BOOST_FWD_REF, the second by using boost::forward in both places, making the two pieces of code textually equal and thus I also removed the duplicate.

---

## Comment 1

> Username: pdimov  
> Created_at: 2016-09-10 11:35:52 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/24#issuecomment-246107082  

Nice simplification, thanks. I've merged it, but it would be nice if we have a test for the new functionality enabled by this change.

---
