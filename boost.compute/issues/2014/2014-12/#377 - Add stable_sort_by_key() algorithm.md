# #377 - Add stable_sort_by_key() algorithm [Closed]

> Username: kylelutz  
> Created at: 2014-12-29 19:01:58 UTC  
> Updated at: 2016-04-28 04:10:02 UTC  
> Closed at: 2016-03-31 20:57:12 UTC  
> Url: https://github.com/boostorg/compute/issues/377  

Add a `stable_sort_by_key()` algorithm which works like `sort_by_key()` but keeps the keys/values in stable order.

---

## Comment 1

> Username: pavanky  
> Created at: 2016-03-31 20:50:08 UTC  
> Url: https://github.com/boostorg/compute/issues/377#issuecomment-204122008  

@kylelutz can't this be closed because of #573 ?

---

## Comment 2

> Username: kylelutz  
> Created at: 2016-03-31 20:57:12 UTC  
> Url: https://github.com/boostorg/compute/issues/377#issuecomment-204126974  

Yup, closed!

---

## Comment 3

> Username: Bulat-Ziganshin  
> Created at: 2016-04-26 21:21:26 UTC  
> Url: https://github.com/boostorg/compute/issues/377#issuecomment-214890540  

stable_sort_by_key() should be listed here: http://www.boost.org/doc/libs/1_61_0_b1/libs/compute/doc/html/boost_compute/reference.html

---

## Comment 4

> Username: kylelutz  
> Created at: 2016-04-27 02:44:42 UTC  
> Url: https://github.com/boostorg/compute/issues/377#issuecomment-214954027  

@Bulat-Ziganshin Thanks for catching that, I've made a pull request to update that: #601

---

## Comment 5

> Username: Bulat-Ziganshin  
> Created at: 2016-04-27 12:37:44 UTC  
> Url: https://github.com/boostorg/compute/issues/377#issuecomment-215068863  

i don't know how html is generated from qbk, but f.e. stable_sort is mentioned in 3 places of http://www.boost.org/doc/libs/1_61_0_b1/libs/compute/doc/html/boost_compute/reference.html

---

## Comment 6

> Username: kylelutz  
> Created at: 2016-04-28 04:10:02 UTC  
> Url: https://github.com/boostorg/compute/issues/377#issuecomment-215305000  

Yeah, the other mentions come from globing the headers with `include/boost/compute/algorithm/*.hpp`, so it doesn't have to mention `boost/compute/stable_sort_by_key.hpp` explicitly.
