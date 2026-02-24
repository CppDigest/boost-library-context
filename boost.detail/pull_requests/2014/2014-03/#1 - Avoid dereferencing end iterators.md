# #1 Avoid dereferencing end iterators. [Closed]

> Username: danieljames  
> Created at: 2014-03-25 22:43:47 UTC  
> Updated at: 2014-03-26 05:59:17 UTC  
> Closed at: 2014-03-26 05:59:17 UTC  
> Url: https://github.com/boostorg/detail/pull/1  

I think it was dereferencing them because reverse iterators don't work with  
BOOST_TEST_EQ, instead use the underlying iterator which works with it.

---

## Comment 1

> Username: Lastique  
> Created_at: 2014-03-26 05:59:17 UTC  
> Url: https://github.com/boostorg/detail/pull/1#issuecomment-38652327  

I've made a more complete fix: https://github.com/boostorg/detail/commit/7312799cc0dffb9f75a0d8076fc6989a4030be48.

---
