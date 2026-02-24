# #78 Add vector accessors [Closed]

> Username: roshanrags  
> Created at: 2014-03-31 08:54:13 UTC  
> Updated at: 2014-11-30 12:52:38 UTC  
> Closed at: 2014-11-30 12:52:38 UTC  
> Url: https://github.com/boostorg/compute/pull/78  

Added accessors for vector types

---

## Comment 1

> Username: coveralls  
> Created_at: 2014-03-31 09:12:25 UTC  
> Url: https://github.com/boostorg/compute/pull/78#issuecomment-39067985  

[![Coverage Status](https://coveralls.io/builds/640812/badge)](https://coveralls.io/builds/640812)  
  
Coverage remained the same when pulling **4bf24363d232cd2f3b7769f8826706c6f4a8410b on roshanr95:vector_accessors** into **b3f8847422a98aef4f15d580897df99feaf929d2 on kylelutz:develop**.

---

## Comment 2

> Username: kylelutz  
> Created_at: 2014-04-01 04:22:53 UTC  
> Url: https://github.com/boostorg/compute/pull/78#issuecomment-39169673  

Looking good! Thanks for working on this.  
  
I'm getting warnings like this though:  
  
```  
../include/boost/compute/types/builtin.hpp:198:1: warning: anonymous structs are a GNU extension [-Wgnu-anonymous-struct]  
../include/boost/compute/types/builtin.hpp:190:5: note: expanded from macro 'BOOST_COMPUTE_DECLARE_VECTOR_TYPES'  
    BOOST_COMPUTE_DECLARE_VECTOR_TYPE(scalar, 8) \  
    ^  
../include/boost/compute/types/builtin.hpp:168:5: note: expanded from macro 'BOOST_COMPUTE_DECLARE_VECTOR_TYPE'  
    BOOST_COMPUTE_DECLARE_VECTOR_TYPE_CLASS(BOOST_PP_CAT(cl_, scalar), \  
    ^  
../include/boost/compute/types/builtin.hpp:126:13: note: expanded from macro 'BOOST_COMPUTE_DECLARE_VECTOR_TYPE_CLASS'  
            struct { BOOST_COMPUTE_VECTOR_TYPE_DECLARE_XYZW_ACCESSORS(vsize) }; \  
```  
  
Is there a way to implement this without using GNU extensions?

---

## Comment 3

> Username: roshanrags  
> Created_at: 2014-04-01 14:17:37 UTC  
> Url: https://github.com/boostorg/compute/pull/78#issuecomment-39210230  

I think we can make the whole thing a union. I'll give it a go.

---

## Comment 4

> Username: roshanrags  
> Created_at: 2014-11-30 12:52:37 UTC  
> Url: https://github.com/boostorg/compute/pull/78#issuecomment-64984358  

Tried a reference based implementation which didn't exactly pan out... I'll open a new one when I get it working...

---
