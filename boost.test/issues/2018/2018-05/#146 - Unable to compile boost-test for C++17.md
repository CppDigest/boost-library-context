# #146 - Unable to compile boost/test for C++17 [Closed]

> Username: k15tfu  
> Created at: 2018-05-22 10:05:54 UTC  
> Updated at: 2019-03-14 12:22:03 UTC  
> Closed at: 2018-11-12 00:03:33 UTC  
> Url: https://github.com/boostorg/test/issues/146  

```  
clang-linux.compile.c++.without-pth bin.v2/linux.static.x64/boost/bin.v2/libs/test/build/clang-linux-6.0.0/release/link-static/threading-multi/debug.o  
In file included from libs/test/src/debug.cpp:16:  
In file included from ./boost/test/impl/debug.ipp:53:  
./boost/test/utils/algorithm.hpp:118:42: error: no member named 'bind1st' in namespace 'std'  
        if( std::find_if( first2, last2, BOOST_TEST_BIND1ST( pred, *first1 ) ) == last2 )  
                                         ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
./boost/test/utils/algorithm.hpp:25:38: note: expanded from macro 'BOOST_TEST_BIND1ST'  
#define BOOST_TEST_BIND1ST(F,A) std::bind1st( (F), (A) )  
```

---

## Comment 1

> Username: raffienficiaud  
> Created at: 2018-05-22 11:18:57 UTC  
> Url: https://github.com/boostorg/test/issues/146#issuecomment-390954641  

Please file an issue on Trac, see the contribution file.

---

## Comment 2

> Username: k15tfu  
> Created at: 2018-05-24 12:59:00 UTC  
> Url: https://github.com/boostorg/test/issues/146#issuecomment-391705254  

https://svn.boost.org/trac10/ticket/13574

---

## Comment 3

> Username: jeking3  
> Created at: 2018-11-10 15:51:01 UTC  
> Url: https://github.com/boostorg/test/issues/146#issuecomment-437593281  

I am running into this while trying to add CI support.  The Visual Studio 2017 jobs with cxxstd=17 or cxxstd=latest are failing because of this...

---

## Comment 4

> Username: jeking3  
> Created at: 2018-11-10 15:58:39 UTC  
> Url: https://github.com/boostorg/test/issues/146#issuecomment-437593839  

It's an easy fix.

---

## Comment 5

> Username: raffienficiaud  
> Created at: 2018-11-10 17:56:53 UTC  
> Url: https://github.com/boostorg/test/issues/146#issuecomment-437603196  

Boost.test uses [BOOST_NO_CXX98_BINDERS](https://www.boost.org/doc/libs/1_67_0/libs/config/doc/html/boost_config/boost_macro_reference.html#boost_config.boost_macro_reference.macros_that_describe_features_that_have_been_removed_from_the_standard_), so it should work. What is the fix that you are referring to?

---

## Comment 6

> Username: raffienficiaud  
> Created at: 2018-11-10 17:59:14 UTC  
> Url: https://github.com/boostorg/test/issues/146#issuecomment-437603394  

Also, I use `b2 --abbreviate-paths address-model=64 toolset=msvc-14.1 cxxflags="/std:c++latest"` for my VS 2017 CI.

---

## Comment 7

> Username: jeking3  
> Created at: 2018-11-11 22:44:56 UTC  
> Url: https://github.com/boostorg/test/issues/146#issuecomment-437712387  

The issue I found was in the example code - the other instances seem to have been resolved.

---

## Comment 8

> Username: raffienficiaud  
> Created at: 2018-11-12 00:03:33 UTC  
> Url: https://github.com/boostorg/test/issues/146#issuecomment-437718317  

Closing this one and working on #182 instead

---

## Comment 9

> Username: k15tfu  
> Created at: 2019-03-14 12:22:03 UTC  
> Url: https://github.com/boostorg/test/issues/146#issuecomment-472830109  

Thank you! I see that it works with C++17 now.
