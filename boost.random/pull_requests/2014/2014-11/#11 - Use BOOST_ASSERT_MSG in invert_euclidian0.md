# #11 Use BOOST_ASSERT_MSG in invert_euclidian0 [Closed]

> Username: aokomoriuta  
> Created at: 2014-11-30 12:47:16 UTC  
> Updated at: 2015-03-14 17:32:57 UTC  
> Closed at: 2015-03-14 17:32:34 UTC  
> Url: https://github.com/boostorg/random/pull/11  

Use `BOOST_ASSERT_MSG(cond, "msg")` instead of `BOOST_ASSERT(cond, && "msg")`.  
  
Some complier submit warning '"msg" is constant conditional expression.'

---

## Comment 1

> Username: swatanabe  
> Created_at: 2015-02-27 21:42:25 UTC  
> Url: https://github.com/boostorg/random/pull/11#issuecomment-76477248  

How exactly is this supposed to help?  
  
``` c++  
# define BOOST_ASSERT_MSG(expr, msg) assert((expr)&&(msg))  
```

---

## Comment 2

> Username: swatanabe  
> Created_at: 2015-03-14 17:32:34 UTC  
> Url: https://github.com/boostorg/random/pull/11#issuecomment-80620685  

wontfix

---
