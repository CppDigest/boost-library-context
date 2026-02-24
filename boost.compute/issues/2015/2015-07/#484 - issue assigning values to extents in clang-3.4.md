# #484 - issue assigning values to extents in clang-3.4 [Closed]

> Username: calebwherry  
> Created at: 2015-07-19 22:32:24 UTC  
> Updated at: 2017-04-09 18:04:29 UTC  
> Closed at: 2017-04-09 18:04:29 UTC  
> Url: https://github.com/boostorg/compute/issues/484  

We (myself and @robertdfrench) are unable to get clang-3.4 to compile extents assignments as demonstrated on line 59 of `test/test_extents.cpp`. We can assign directly using the `[]` operator, but `compute::dim(10,10)` gives a cast error.  
  
```  
In file included from /home/robert/Projects/GARTH-a-lisa/ext/boost.compute/include/boost/compute/utility.hpp:14:  
/home/robert/Projects/GARTH-a-lisa/ext/boost.compute/include/boost/compute/utility/dim.hpp:33:12: error: no matching conversion for functional-style cast from 'void' to  
      'extents<sizeof...(Args)>'  
    return extents<sizeof...(Args)>({ static_cast<size_t>(args)... });  
           ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~  
/home/robert/Projects/GARTH-a-lisa/src/garth.cpp:80:36: note: in instantiation of function template specialization 'boost::compute::dim<int, int>' requested here  
  bc::extents<2> globalRange = bc::dim(10,10);  
                                   ^  
/home/robert/Projects/GARTH-a-lisa/ext/boost.compute/include/boost/compute/utility/extents.hpp:33:7: note: candidate constructor (the implicit copy constructor) not  
      viable: cannot convert initializer list argument to 'const boost::compute::extents<2>'  
class extents  
      ^  
/home/robert/Projects/GARTH-a-lisa/ext/boost.compute/include/boost/compute/utility/extents.hpp:33:7: note: candidate constructor (the implicit move constructor) not  
      viable: cannot convert initializer list argument to 'boost::compute::extents<2>'  
class extents  
      ^  
/home/robert/Projects/GARTH-a-lisa/ext/boost.compute/include/boost/compute/utility/extents.hpp:59:14: note: candidate constructor not viable: cannot convert initializer  
      list argument to 'size_t' (aka 'unsigned long')  
    explicit extents(size_t value)  
             ^  
/home/robert/Projects/GARTH-a-lisa/ext/boost.compute/include/boost/compute/utility/extents.hpp:48:5: note: candidate constructor not viable: requires 0 arguments, but 1  
      was provided  
    extents()  
    ^  
```  
  
This is ubuntu14.04 with the latest boost.compute/master (377e509).  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-07-20 05:03:36 UTC  
> Url: https://github.com/boostorg/compute/issues/484#issuecomment-122763253  

Interesting, it looks like the `extents(std::initalizer_list<size_t>` constructor ([utility/extents.hpp:67](https://github.com/boostorg/compute/blob/master/include/boost/compute/utility/extents.hpp#L67)) is not being called. Can you check that the `BOOST_NO_CXX11_HDR_INITIALIZER_LIST` and `BOOST_NO_0X_HDR_INITIALIZER_LIST` config macros are not defined for your compiler/platform?

---

## Comment 2

> Username: jszuppe  
> Created at: 2015-07-20 14:29:03 UTC  
> Updated at: 2015-07-20 15:00:28 UTC  
> Url: https://github.com/boostorg/compute/issues/484#issuecomment-122902722  

Nevertheless, we should add `#ifndef` check before running tests that relate on this macros not being defined.  
  
@calebwherry Which Boost version do you use?  
  
It maybe connected to this issue https://svn.boost.org/trac/boost/ticket/10523 in `boost/config/stdlib/libstdcpp3.hpp`, but it's just a fast guess. In 1.57 `libstdcpp3.hpp` seems to be rewritten, so it's probably fixed since 1.57.

---

## Comment 3

> Username: jszuppe  
> Created at: 2015-07-25 12:37:19 UTC  
> Url: https://github.com/boostorg/compute/issues/484#issuecomment-124843279  

I have no problem compiling `test/test_extents.cpp` with clang-3.4, but when I `BOOST_COMPUTE_USE_CPP11` is on then I have the same error.   
  
@calebwherry, did you compile it with c++11 flag or without it?  
  
What's interesting with `BOOST_COMPUTE_USE_CPP11`  macros `BOOST_NO_CXX11_HDR_INITIALIZER_LIST` and `BOOST_NO_0X_HDR_INITIALIZER_LIST` are still **defined**. I'm pretty sure that's all because of this https://svn.boost.org/trac/boost/ticket/10523 issue in Boost itself. I checked and clang++ 3.4, 3.5 and 3.6 version all define `__GNUC__` as 4 and `__GNUC_MINOR__` as 2 resulting in defining many `BOOST_NO_CXX11_` (as described in linked issue).  
  
Tested clang++ version: 3.4, 3.5, 3.6  
Tested Boost versions: 1.55  
  
IMO, using Boost 1.57 (or newer) will solve this problem. I don't know if we should make workaround for this.

---

## Comment 4

> Username: jszuppe  
> Created at: 2017-04-08 16:23:11 UTC  
> Url: https://github.com/boostorg/compute/issues/484#issuecomment-292728405  

1) This bug/problem does not occur when using Boost.Compute with 1.58 (or newer) since `boost/config/stdlib/libstdcpp3.hpp` in Boost.Config was rewritten (I mentioned this above).  
2) Fix for tests: https://github.com/boostorg/compute/pull/703

---

## Comment 5

> Username: jszuppe  
> Created at: 2017-04-09 18:04:29 UTC  
> Url: https://github.com/boostorg/compute/issues/484#issuecomment-292801753  

Fixed by https://github.com/boostorg/compute/pull/703.
