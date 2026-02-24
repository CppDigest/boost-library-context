# #212 - build python binding error on windows 10,msvc14,python3.6. [Open]

> Username: kingctan  
> Created at: 2018-06-13 01:34:24 UTC  
> Updated at: 2018-12-30 14:15:50 UTC  
> Url: https://github.com/boostorg/python/issues/212  

```  
D:\local\boost_1_67_0\boost/python/operators.hpp(223): error C2065: "op_": undeclared identifier  
D:\local\boost_1_67_0\boost/python/operators.hpp(223): error C2059: Syntax error: ">"  
D:\local\boost_1_67_0\boost/python/operators.hpp(223): error C2976: "boost::python::detail::operator_l": Template parameters are too few  
D:\local\boost_1_67_0\boost/python/operators.hpp(40): note: See the declaration of "boost::python::detail::operator_l"  
D:\local\boost_1_67_0\boost/python/operators.hpp(223): error C2913: Explicit specialization; "boost::python::detail::operator_l" is not a specialization of a class template  
D:\local\boost_1_67_0\boost/python/operators.hpp(223): error C2065: "op_": undeclared identifier  
D:\local\boost_1_67_0\boost/python/operators.hpp(223): error C2059: Syntax error: ">"  
D:\local\boost_1_67_0\boost/python/operators.hpp(223): error C2976: "boost::python::detail::operator_r": Template parameters are too few  
D:\local\boost_1_67_0\boost/python/operators.hpp(45): note: See the declaration of "boost::python::detail::operator_r"  
D:\local\boost_1_67_0\boost/python/operators.hpp(223): error C2913: Explicit specialization; "boost::python::detail::operator_r" is not specialization of class template  
D:\local\boost_1_67_0\boost/python/operators.hpp(224): error C2913: Explicit customization; "boost::python::detail::operator_l" is not a specialization of the class template  
D:\local\boost_1_67_0\boost/python/operators.hpp(224): error C2913: Explicit customization; "boost::python::detail::operator_r" is not a specialization of the class template  
```

---

## Comment 1

> Username: Nanoatic  
> Created at: 2018-06-28 19:25:58 UTC  
> Url: https://github.com/boostorg/python/issues/212#issuecomment-401145967  

i have the same issue did you find a way to solve it ?

---

## Comment 2

> Username: fun4jimmy  
> Created at: 2018-07-13 16:06:53 UTC  
> Url: https://github.com/boostorg/python/issues/212#issuecomment-404878435  

I have also encountered this issue. It seems to be caused by a header defining rand before boost/python/operators.hpp is included. I found reordering my header files fixed it.

---

## Comment 3

> Username: hyyz17200  
> Created at: 2018-09-07 13:21:09 UTC  
> Url: https://github.com/boostorg/python/issues/212#issuecomment-419437075  

@fun4jimmy I have this problem too. Plz how to fixed it? Reorder which file?

---

## Comment 4

> Username: stefanseefeld  
> Created at: 2018-09-07 13:28:44 UTC  
> Url: https://github.com/boostorg/python/issues/212#issuecomment-419439280  

@fun4jimmy , "defining" as in macro ? Yuck ! It seems Windows as a habit of doing that...  
Can you submit a patch (PR) for this ?

---

## Comment 5

> Username: hyyz17200  
> Created at: 2018-09-07 15:07:04 UTC  
> Url: https://github.com/boostorg/python/issues/212#issuecomment-419469458  

Solved. modified cpp files in '\libtorrent-rasterbar-1.1.9\bindings\python\src'. Like @fun4jimmy said, change the order, move '#include "boost_python.hpp"' to the top.   
I still have other 3 faild info, but not these ones. I will figure out after.

---

## Comment 6

> Username: hyyz17200  
> Created at: 2018-09-07 15:08:19 UTC  
> Url: https://github.com/boostorg/python/issues/212#issuecomment-419469829  

@stefanseefeld is this problem related to boost ,or libtorrent ?

---

## Comment 7

> Username: stefanseefeld  
> Created at: 2018-09-07 15:11:17 UTC  
> Url: https://github.com/boostorg/python/issues/212#issuecomment-419470779  

I have no idea. It sounds like MSVC defines a rogue macro that conflicts with symbols used in `Boost.Python`. If that's the case, there is nothing I can do but suggest exactly the action you have already taken: reorder the headers so this pollution doesn't affect `Boost.Python` code. So it's `Boost.Python` *users* (such as `libtorrent`) that need to adjust.

---

## Comment 8

> Username: ssiloti  
> Created at: 2018-11-01 04:03:08 UTC  
> Url: https://github.com/boostorg/python/issues/212#issuecomment-434923901  

The problematic defines are for the alternative operator tokens like `and`, `xor`, `eq`, etc. These defines are coming from `ciso646` which is included at least by Boost.Asio as a way of getting information on supported C++ versions.  
  
The msvc documentation suggests it might be possible to use the `/Za` option to disable these defines, but it breaks many Windows SDK headers.

---

## Comment 9

> Username: stefanseefeld  
> Created at: 2018-11-01 04:16:34 UTC  
> Url: https://github.com/boostorg/python/issues/212#issuecomment-434925543  

I suggest you lay out your code in a way that you can #undef the rogue macros prior to including Boost.Python header files, to avoid the above errors.

---

## Comment 10

> Username: lightzls  
> Created at: 2018-12-29 08:44:20 UTC  
> Url: https://github.com/boostorg/python/issues/212#issuecomment-450476569  

If you are using Boost.Asio with Boost.Python, just open the file boost/asio/detail/config.hpp, remove line 73-75. It has been fixed in boost-1.69.0.  
Ref commit: https://github.com/boostorg/asio/commit/569cba5d47e20e9fae8ac8abfe428fab3cc95549#diff-90ac9e94f73cea8d74f04eebdd5551d7

---

## Comment 11

> Username: Nanoatic  
> Created at: 2018-12-30 14:10:52 UTC  
> Updated at: 2018-12-30 14:15:50 UTC  
> Url: https://github.com/boostorg/python/issues/212#issuecomment-450563247  

Just tried it today with visual studio 2017 and python 3.7/boost-1.69 , I can confirm it is working fine. Cheers 😄   
![capture](https://user-images.githubusercontent.com/17166341/50548004-c3470900-0c45-11e9-955a-7c6bfaa7c27e.PNG)
