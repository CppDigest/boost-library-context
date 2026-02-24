# #139 Fix wrong and unused macro name while use BOOST_PP [Closed]

> Username: yocox  
> Created at: 2017-07-12 02:31:43 UTC  
> Updated at: 2019-03-04 02:45:54 UTC  
> Closed at: 2019-03-04 02:45:54 UTC  
> Url: https://github.com/boostorg/python/pull/139  

- `BOOST_PYTHON_AS_OBJECT` in numeric.cpp has never been used  
- `BOOST_PYTHON_AS_OBJECT` in numeric.hpp should be `BOOST_PYTHON_ENUM_AS_OBJECT`

---

## Comment 1

> Username: stefanseefeld  
> Created_at: 2017-07-12 04:31:54 UTC  
> Url: https://github.com/boostorg/python/pull/139#issuecomment-314641450  

In fact, I believe now that we have proper NumPy support in Boost.Python, we should deprecate the Numeric API. Thoughts ?

---
