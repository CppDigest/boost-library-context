# #40 `noexcept` specifications for `BOOST_STRONG_TYPEDEF` [Merged]

> Username: praetorian20  
> Created at: 2016-05-04 06:40:53 UTC  
> Updated at: 2016-05-30 01:44:36 UTC  
> Merged at: 2016-05-29 17:26:27 UTC  
> Closed at: 2016-05-29 17:26:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/40  

`BOOST_STRONG_TYPEDEF` now detects whether the type being wrapped is  
nothrow default/copy constructible and nothrow assignable. The wrapped  
type is given its own `noexcept` specifications accordingly.

---

## Comment 1

> Username: praetorian20  
> Created_at: 2016-05-04 06:42:12 UTC  
> Url: https://github.com/boostorg/serialization/pull/40#discussion_r61994760  

I'm not sure whether I was supposed to add to the copyright or not. If this isn't the right thing to do, then let me know and I'll change it.

---

## Comment 2

> Username: robertramey  
> Created_at: 2016-05-04 06:57:57 UTC  
> Url: https://github.com/boostorg/serialization/pull/40#discussion_r61995699  

I'll merge it in with your copyright so that you can take the blame if   
it breaks something.  
  
Robert Ramey  
  
On 5/3/16 11:42 PM, Ashish Sadanandan wrote:  
  
> In include/boost/serialization/strong_typedef.hpp   
> https://github.com/boostorg/serialization/pull/40#discussion_r61994760:  
>   
> > @@ -10,6 +10,7 @@  
> >  // strong_typedef.hpp:  
> >   
> >  // (C) Copyright 2002 Robert Ramey - http://www.rrsd.com .  
> > +// (C) Copyright 2016 Ashish Sadanandan  
>   
> I'm not sure whether I was supposed to add to the copyright or not. If   
> this isn't the right thing to do, then let me know and I'll change it.  
>   
> —  
> You are receiving this because you are subscribed to this thread.  
> Reply to this email directly or view it on GitHub   
> https://github.com/boostorg/serialization/pull/40/files/e32c5c5e2888b7226438c67b4038a81ae31266c3#r61994760  
  
##   
  
Robert Ramey  
www.rrsd.com  
(805)569-3793

---

## Comment 3

> Username: praetorian20  
> Created_at: 2016-05-05 03:57:24 UTC  
> Url: https://github.com/boostorg/serialization/pull/40#discussion_r62146499  

:) Fair enough

---

## Comment 4

> Username: robertramey  
> Created_at: 2016-05-29 19:43:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/40#issuecomment-222378805  

Did you actually run your test?  It's failing for me  
  
`-Users-robertramey-WorkingProjects-modular-boost-bin.v2-libs-serialization-test-test_strong_typedef.test-darwin-5.1~03-debug-link-static  
  
Compiler output:  
  
In file included from test_strong_typedef.cpp:12:0:  
test_strong_typedef.cpp:24:1: error: invalid application of â€˜sizeofâ€™ to incomplete type â€˜boost::STATIC_ASSERTION_FAILURE<false>â€™  
 BOOST_STATIC_ASSERT(boost::has_nothrow_default_constructor<strong_int>::value);  
 ^  
test_strong_typedef.cpp:24:1: error: template argument 1 is invalid  
 BOOST_STATIC_ASSERT(boost::has_nothrow_default_constructor<strong_int>::value);  
 ^  
test_strong_typedef.cpp:25:1: error: invalid application of â€˜sizeofâ€™ to incomplete type â€˜boost::STATIC_ASSERTION_FAILURE<false>â€™  
 BOOST_STATIC_ASSERT(boost::has_nothrow_copy_constructor<strong_int>::value);  
 ^  
test_strong_typedef.cpp:25:1: error: template argument 1 is invalid  
 BOOST_STATIC_ASSERT(boost::has_nothrow_copy_constructor<strong_int>::value);  
 ^  
test_strong_typedef.cpp:26:1: error: invalid application of â€˜sizeofâ€™ to incomplete type â€˜boost::STATIC_ASSERTION_FAILURE<false>â€™  
 BOOST_STATIC_ASSERT(boost::has_nothrow_assign<strong_int>::value);  
 ^  
test_strong_typedef.cpp:26:1: error: template argument 1 is invalid  
 BOOST_STATIC_ASSERT(boost::has_nothrow_assign<strong_int>::value);  
 ^  
test_strong_typedef.cpp:39:1: error: invalid application of â€˜sizeofâ€™ to incomplete type â€˜boost::STATIC_ASSERTION_FAILURE<false>â€™  
 BOOST_STATIC_ASSERT(boost::has_nothrow_default_constructor<type1>::value);  
 ^  
test_strong_typedef.cpp:39:1: error: template argument 1 is invalid  
 BOOST_STATIC_ASSERT(boost::has_nothrow_default_constructor<type1>::value);  
 ^  
test_strong_typedef.cpp:40:1: error: invalid application of â€˜sizeofâ€™ to incomplete type â€˜boost::STATIC_ASSERTION_FAILURE<false>â€™  
 BOOST_STATIC_ASSERT(boost::has_nothrow_copy_constructor<type1>::value);  
 ^  
test_strong_typedef.cpp:40:1: error: template argument 1 is invalid  
 BOOST_STATIC_ASSERT(boost::has_nothrow_copy_constructor<type1>::value);  
 ^  
test_strong_typedef.cpp:41:1: error: invalid application of â€˜sizeofâ€™ to incomplete type â€˜boost::STATIC_ASSERTION_FAILURE<false>â€™  
 BOOST_STATIC_ASSERT(boost::has_nothrow_assign<type1>::value);  
 ^  
test_strong_typedef.cpp:41:1: error: template argument 1 is invalid  
 BOOST_STATIC_ASSERT(boost::has_nothrow_assign<type1>::value);  
 ^  
test_strong_typedef.cpp: In function â€˜int main()â€™:  
test_strong_typedef.cpp:65:12: error: â€˜EXIT_SUCCESSâ€™ was not declared in this scope  
     return EXIT_SUCCESS;  
            ^  
  
```  
"/hpc-5.1/usr/local/bin/g++"  -ftemplate-depth-128 -std=c++03 -O0 -fno-inline -Wall -g -gdwarf-2 -fexceptions -arch x86_64  -DBOOST_ALL_NO_LIB=1 -DBOOST_FILESYSTEM_STATIC_LINK=1 -DBOOST_SYSTEM_STATIC_LINK=1  -I"../../.." -c -o "../../../bin.v2/libs/serialization/test/test_strong_typedef.test/darwin-5.1~03/debug/link-static/test_strong_typedef.o" "test_strong_typedef.cpp"  
```  
  
`

---

## Comment 5

> Username: praetorian20  
> Created_at: 2016-05-30 01:13:09 UTC  
> Url: https://github.com/boostorg/serialization/pull/40#issuecomment-222394416  

Well, crap, I was testing with VS2015, which has `noexcept` support and the test passes in that case. Without it, `BOOST_NOEXCEPT_IF(...)` translates to empty, so all the static asserts fail. I'll fix it soon, and this time test with gcc and `-std=c++03`. Sorry for the screw up.

---

## Comment 6

> Username: robertramey  
> Created_at: 2016-05-30 01:44:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/40#issuecomment-222396671  

Welcome to the world of boost library development.  
  
I've checked in your change to the develop branch so you should see the   
results your self on on platforms  
  
http://www.boost.org/development/tests/develop/developer/serialization.html  
  
On 5/29/16 6:13 PM, Ashish Sadanandan wrote:  
  
> Well, crap, I was testing with VS2015, which has |noexcept| support   
> and the test passes in that case. Without it, |BOOST_NOEXCEPT_IF(...)|   
> translates to empty, so all the static asserts fail. I'll fix it soon,   
> and this time test with gcc and |-std=c++03|. Sorry for the screw up.  
>   
> —  
> You are receiving this because you modified the open/close state.  
> Reply to this email directly, view it on GitHub   
> https://github.com/boostorg/serialization/pull/40#issuecomment-222394416,   
> or mute the thread   
> https://github.com/notifications/unsubscribe/AB4R3D0XcpOObL3VVEXG8-5iN_XQEhGkks5qGjmlgaJpZM4IW3r4.  
  
##   
  
Robert Ramey  
www.rrsd.com  
(805)569-3793

---
