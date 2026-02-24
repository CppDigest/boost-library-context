# #829 add support to set kernel argument as NULL; fixes issue #828 [Merged]

> Username: rosenrodt  
> Created at: 2019-04-03 03:23:14 UTC  
> Updated at: 2019-05-12 12:26:12 UTC  
> Merged at: 2019-04-04 05:48:08 UTC  
> Closed at: 2019-04-04 05:48:08 UTC  
> Url: https://github.com/boostorg/compute/pull/829  

Per Khronos OpenCL spec:  
>If the argument is declared to be a pointer of a built-in scalar or vector type, or a user defined structure type in the global or constant address space, the memory object specified as argument value must be a buffer object (or NULL).  
  
But the following call causes error:  
```c++  
some_kernel.set_arg(0, NULL);  
```  
One way as suggested in #828 is to use  
```c++  
some_kernel.set_arg(0, sizeof(cl_mem), NULL);  
```  
Now it's just  
```c++  
some_kernel.set_arg(0, nullptr);  
```

---

## Comment 1

> Username: rosenrodt  
> Created_at: 2019-04-03 04:01:25 UTC  
> Updated_at: 2019-04-03 04:46:05 UTC  
> Url: https://github.com/boostorg/compute/pull/829#issuecomment-479328767  

@jszuppe Maybe it's better if this supports pre-C++11. Should we implement similar class to `local_buffer` (say `null_buffer`) to support setting kernel argument to NULL?

---

## Comment 2

> Username: coveralls  
> Created_at: 2019-04-03 06:45:31 UTC  
> Updated_at: 2019-04-03 13:30:21 UTC  
> Url: https://github.com/boostorg/compute/pull/829#issuecomment-479361394  

[![Coverage Status](https://coveralls.io/builds/22575145/badge)](https://coveralls.io/builds/22575145)  
  
Coverage increased (+0.006%) to 84.025% when pulling **60f8555780a4f0a5254adacb315b8254a99d0e1e on rosenrodt:set-null-kernel-argument** into **36c89134d4013b2e5e45bc55656a18bd6141995a on boostorg:develop**.

---

## Comment 3

> Username: jszuppe  
> Created_at: 2019-04-03 08:09:46 UTC  
> Url: https://github.com/boostorg/compute/pull/829#issuecomment-479385296  

> Maybe it's better if this supports pre-C++11. Should we implement similar class to local_buffer (say null_buffer) to support setting kernel argument to NULL?  
  
I would rather have just the C++11 version. It's very unlikely that someone using C++03 will find and use `boost::compute::null_buffer` before just using `set_arg(0, sizeof(cl_mem), NULL);` (not mentioning that setting a buffer to `NULL` is not a popular use case).

---

## Comment 4

> Username: rosenrodt  
> Created_at: 2019-04-03 08:29:00 UTC  
> Url: https://github.com/boostorg/compute/pull/829#issuecomment-479391731  

We could stick to C++11. The trade-off would be that people using pre-C++11 won't be able to write nice things like `kernel.set_args(b0, b1, nullptr)`.  
  
If that's indeed where we want to go then I'll update the PR.

---

## Comment 5

> Username: jszuppe  
> Created_at: 2019-04-03 08:37:13 UTC  
> Url: https://github.com/boostorg/compute/pull/829#issuecomment-479394766  

pre-C++11 you don't have variadic templates, you can't write `kernel.set_args(b0, b1, nullptr)` in C++03.

---

## Comment 6

> Username: rosenrodt  
> Created_at: 2019-04-03 09:05:23 UTC  
> Url: https://github.com/boostorg/compute/pull/829#issuecomment-479404616  

I'm sold. I'll just stick with C++11 version. Please review

---

## Review 7 [Changes requested]

> Username: jszuppe  
> Created_at: 2019-04-03 09:11:52 UTC  
> State: CHANGES_REQUESTED  
> Url: https://github.com/boostorg/compute/pull/829#pullrequestreview-222087705  

📁 include/boost/compute/kernel.hpp

```diff
 377 |     }
 378 | 
 379 |+     #ifdef BOOST_COMPUTE_USE_CPP11
```

> Username: jszuppe  
> Created_at: 2019-04-03 09:10:17 UTC  
> Updated_at: 2019-04-03 09:48:18 UTC  
> Url: https://github.com/boostorg/compute/pull/829#discussion_r271646675  

We should be able to replace that with `#ifndef BOOST_NO_CXX11_NULLPTR`.

> Username: rosenrodt  
> Created_at: 2019-04-03 09:50:53 UTC  
> Url: https://github.com/boostorg/compute/pull/829#discussion_r271662952  

added in new commit

---

📁 include/boost/compute/kernel.hpp

```diff
 383 |+         set_arg(index, sizeof(cl_mem), NULL);
 384 |+     }
 385 |+     #endif
```

> Username: jszuppe  
> Created_at: 2019-04-03 09:11:15 UTC  
> Updated_at: 2019-04-03 09:48:18 UTC  
> Url: https://github.com/boostorg/compute/pull/829#discussion_r271647071  

`#endoif //  BOOST_NO_CXX11_NULLPTR`

> Username: rosenrodt  
> Created_at: 2019-04-03 09:51:06 UTC  
> Url: https://github.com/boostorg/compute/pull/829#discussion_r271663026  

added in new commit


📁 test/test_kernel.cpp

```diff
  71 |     foo.set_arg(0, x);
  72 |     foo.set_arg(1, y.get());
  73 |+ #ifdef BOOST_COMPUTE_USE_CPP11
```

> Username: jszuppe  
> Created_at: 2019-04-03 09:11:25 UTC  
> Updated_at: 2019-04-03 09:48:18 UTC  
> Url: https://github.com/boostorg/compute/pull/829#discussion_r271647123  

`BOOST_NO_CXX11_NULLPTR`


📁 include/boost/compute/memory/local_buffer.hpp

```diff
  90 | 
  91 |- #endif // BOOST_COMPUTE_MEMORY_SVM_PTR_HPP
  91 |+ #endif // BOOST_COMPUTE_MEMORY_LOCAL_BUFFER_HPP
```

> Username: jszuppe  
> Created_at: 2019-04-03 09:11:45 UTC  
> Updated_at: 2019-04-03 09:48:18 UTC  
> Url: https://github.com/boostorg/compute/pull/829#discussion_r271647278  

Good catch.


---
