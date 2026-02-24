# #5 Fix for MSVC 12.0 std::is_copy_constructible and boost::is_copy_construc... [Merged]

> Username: igaztanaga  
> Created at: 2014-08-17 23:13:10 UTC  
> Updated at: 2014-08-24 08:38:56 UTC  
> Merged at: 2014-08-20 17:16:45 UTC  
> Closed at: 2014-08-20 17:16:45 UTC  
> Url: https://github.com/boostorg/type_traits/pull/5  

MSVC 12.0 (Visual 2013) has problems when the copy constructor has been deleted. See:  
// https://connect.microsoft.com/VisualStudio/feedback/details/800328/std-is-copy-constructible-is-broken  
  
BOOST_NO_CXX11_DELETED_FUNCTIONS based code seems to return false for those types.

---

## Comment 1

> Username: jzmaddock  
> Created_at: 2014-08-18 17:25:56 UTC  
> Url: https://github.com/boostorg/type_traits/pull/5#issuecomment-52525298  

Can you explain what difference this makes - the trait seems to be broken either which way?  
  
Thanks, John.

---

## Comment 2

> Username: igaztanaga  
> Created_at: 2014-08-18 17:44:30 UTC  
> Url: https://github.com/boostorg/type_traits/pull/5#issuecomment-52527886  

Due to a compiler bug, the boost trait (as well as the standard std::is_copy_constructible) returns true for types with deleted copy constructors, as shown in the regression test (lines 87 & 88):  
  
http://www.boost.org/development/tests/develop/developer/output/teeks99-08e-win2012R2-64on64-boost-bin-v2-libs-type_traits-test-is_copy_constructible_test-test-msvc-12-0-debug-threading-multi.html  
  
The non-deleted functions based implementation (visual 2013 is the first MSVC compiler to have deleted functions) at least works with types that inherit from noncopyable or use Boost.Move.  
  
The patch won't workaround the compiler problem, but at least MSVC users using boost::noncopyable or Boost.Move won't get a regression.

---
