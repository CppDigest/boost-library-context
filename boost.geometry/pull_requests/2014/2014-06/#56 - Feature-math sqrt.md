# #56 Feature/math sqrt [Merged]

> Username: mkaravel  
> Created at: 2014-06-04 11:38:23 UTC  
> Updated at: 2014-06-14 01:54:52 UTC  
> Merged at: 2014-06-14 01:54:52 UTC  
> Closed at: 2014-06-14 01:54:52 UTC  
> Url: https://github.com/boostorg/geometry/pull/56  

1) Replace calls of ::sqrt by boost::geometry::math::sqrt  
2) Fix an unused typedef warning missed by Adam  
3) Remove obsolete include in offset algorithm (extensions)

---

## Comment 1

> Username: awulkiew  
> Created_at: 2014-06-05 13:57:10 UTC  
> Updated_at: 2014-06-13 22:58:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/56#discussion_r13440483  

We could consider a slightly different approach. The common idiom in such cases is to bring a function into the scope with 'using' keyword and then call the function without explicitly defining the namespace. This way the ADL can kick in and search for a function in more places.  
E.g. see:  
https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/beta.hpp line 35 and 104  
and for definition of BOOST_MATH_STD_USING  
https://github.com/boostorg/math/blob/master/include/boost/math/tools/config.hpp line 255  
or  
https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/sinhc.hpp line 42

---

## Comment 2

> Username: awulkiew  
> Created_at: 2014-06-05 14:14:00 UTC  
> Updated_at: 2014-06-13 22:58:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/56#discussion_r13441414  

Another thing to consider. According to this: http://en.cppreference.com/w/cpp/numeric/math/sqrt in C++11 for Integral types double is returned however I doubt that we call sqrt() for integral types since it wouldn't compile on older compilers.  
Another thing is that we can't predict what type would be returned for some non-fundamental user-defined type. But here returning the same type is also a sane choice.  
  
It's a shame we shouldn't use C++11 auto, decltype and trailing return type or even better C++14 auto return type deduction.

---

## Comment 3

> Username: mkaravel  
> Created_at: 2014-06-05 14:52:22 UTC  
> Updated_at: 2014-06-13 22:58:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/56#discussion_r13443776  

@awulkiew Regarding the first comment. I would like to think a bit more about this and see the code examples you wrote. Most probably I will change it as you describe.  
  
Regarding the second comment, this is a very good point. I need to change the return type accordingly, given that we know beforehand the fundamental types. I will fix this.

---

## Comment 4

> Username: mkaravel  
> Created_at: 2014-06-05 14:54:37 UTC  
> Updated_at: 2014-06-13 22:58:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/56#discussion_r13443894  

@awulkiew Apparently the overloads for integral types have been added in C++11.  
Anyways, I will fix this and commit the change.

---

## Comment 5

> Username: mkaravel  
> Created_at: 2014-06-10 06:22:59 UTC  
> Url: https://github.com/boostorg/geometry/pull/56#issuecomment-45577907  

@awulkiew implemented sqrt for non-fundamental number types using the "using mechanism as you suggested.  
  
I also added a unit test, which also show how the new design (you suggested) can look up sqrt in global namespace, even though the custom number type is defined in another namespace.  
  
I would argue that the feature is ready for merging now.

---

## Comment 6

> Username: awulkiew  
> Created_at: 2014-06-11 15:01:31 UTC  
> Updated_at: 2014-06-13 22:58:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/56#discussion_r13653700  

Btw, we should probably handle all of the math functions in a uniform way.

---

## Comment 7

> Username: awulkiew  
> Created_at: 2014-06-11 15:03:51 UTC  
> Updated_at: 2014-06-13 22:58:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/56#discussion_r13653843  

I'm just curious isn't it ambiguous? using declarations x2?  
EDIT: Yes, there could be a problem if sqrt() in the global and std namespace was separate functions. I don't know if such library implementation exists. On my machine (libstdc++) it's ok since ::sqrt is brought into the std scope with using declaration.  
But, wouldn't it be ok to just use one of the namespaces? E.g. like here: https://github.com/boostorg/math/blob/develop/include/boost/math/special_functions/sinhc.hpp line 42.  
Or just use std::sqrt, like in the most places in Boost.Math?

---

## Comment 8

> Username: mkaravel  
> Created_at: 2014-06-13 22:49:59 UTC  
> Updated_at: 2014-06-13 22:58:45 UTC  
> Url: https://github.com/boostorg/geometry/pull/56#discussion_r13775378  

@awulkiew I totally agree. It is in my to-do-list.

---

## Comment 9

> Username: mkaravel  
> Created_at: 2014-06-13 22:53:23 UTC  
> Updated_at: 2014-06-13 23:12:58 UTC  
> Url: https://github.com/boostorg/geometry/pull/56#discussion_r13775467  

@awulkiew This code is relevant for non-fundamental number types. So, yes I expect an ambiguity when you have a non-fundamental number type T, for which sqrt(T) is defined in more than one namespace among the three I list in the comments. I doubt it, though, that the sqrt implementation for a non-fundamental number type would be implemented in more than one namespace (same for other mathematical functions).  
  
EDIT: I think that the real problem is when you have a non-fundamental number type who's sqrt implementation is not in one of the three namespace listed in the comments. In this case if the number type is to be used in BG, the user/developer would have to define sqrt in, say, global scope.

---
