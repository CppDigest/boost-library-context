# #208 Nth element [Closed]

> Username: roshanrags  
> Created at: 2014-07-31 13:58:12 UTC  
> Updated at: 2014-08-09 17:19:52 UTC  
> Closed at: 2014-08-06 00:58:49 UTC  
> Url: https://github.com/boostorg/compute/pull/208  

Added benchmark, modified the tests and implemented a selection algorithm. ~5x improvement...

---

## Comment 1

> Username: roshanrags  
> Created_at: 2014-07-31 14:03:41 UTC  
> Updated_at: 2014-07-31 14:05:38 UTC  
> Url: https://github.com/boostorg/compute/pull/208#issuecomment-50762508  

btw I tried to do something similar to `sort` using `boost::compute::less` instead of repeating the same code twice, but ended up with a huge error: https://gist.github.com/roshanr95/b3bb5bd5e3eb16617fa6. Think it has something to do with the `bind` function...  
  
I'll update it once I find a fix...

---

## Comment 2

> Username: coveralls  
> Created_at: 2014-07-31 14:18:43 UTC  
> Url: https://github.com/boostorg/compute/pull/208#issuecomment-50764564  

[![Coverage Status](https://coveralls.io/builds/1028445/badge)](https://coveralls.io/builds/1028445)  
  
Coverage increased (+0.01%) when pulling **d898dc3020ca0e147b8c71ec9af3b95375dc0ab5 on roshanr95:nth-element** into **8a11a320d3bdc3e2b560fa35c5b67e4e0958a910 on kylelutz:master**.

---

## Comment 3

> Username: kylelutz  
> Created_at: 2014-08-01 03:07:39 UTC  
> Updated_at: 2014-08-05 16:43:49 UTC  
> Url: https://github.com/boostorg/compute/pull/208#discussion_r15681002  

unused include?

---

## Comment 4

> Username: kylelutz  
> Created_at: 2014-08-01 03:09:54 UTC  
> Updated_at: 2014-08-05 16:43:49 UTC  
> Url: https://github.com/boostorg/compute/pull/208#discussion_r15681037  

it looks like `boost::compute::lambda::_1` is getting found rather than `boost::compute::placeholders::_1` (which is used by `bind()`) which is probably what is causing the error you see. Try doing this:  
  
```  
using boost::compute::placeholders::_1;  
partition(first, last, bind(compare, _1, value), queue);  
```

---

## Comment 5

> Username: kylelutz  
> Created_at: 2014-08-01 03:10:52 UTC  
> Updated_at: 2014-08-05 16:43:49 UTC  
> Url: https://github.com/boostorg/compute/pull/208#discussion_r15681053  

try changing this to something like:  
  
```  
BOOST_COMPUTE_FUNCTION(bool, less_than, (int a, int b),  
{  
    return a < b;  
});  
```

---

## Comment 6

> Username: kylelutz  
> Created_at: 2014-08-01 03:16:25 UTC  
> Updated_at: 2014-08-05 16:43:49 UTC  
> Url: https://github.com/boostorg/compute/pull/208#discussion_r15681133  

this doesn't look right, why isn't it sorted?

---

## Comment 7

> Username: kylelutz  
> Created_at: 2014-08-01 03:19:01 UTC  
> Updated_at: 2014-08-05 16:43:49 UTC  
> Url: https://github.com/boostorg/compute/pull/208#discussion_r15681172  

oh yeah, `nth_element()` doesn't specify the order of the entire range. we should probably just check the n'th value explicitly and then use something like `is_partitioned(vector.begin(), vector.begin() + 5, _1 <= 9, queue)`. also it would probably be good to add the two test cases show in the example for [`std::nth_element()`](http://en.cppreference.com/w/cpp/algorithm/nth_element).

---

## Comment 8

> Username: roshanrags  
> Created_at: 2014-08-05 16:01:11 UTC  
> Updated_at: 2014-08-05 16:43:49 UTC  
> Url: https://github.com/boostorg/compute/pull/208#discussion_r15822232  

`nth_element` only partially sorts the range, which means that elements in `[first,nth)` are less than or equal to elements in `[nth,last)`. Therefore, I see no reason to do anything if `nth == last`.

---

## Comment 9

> Username: coveralls  
> Created_at: 2014-08-05 17:02:08 UTC  
> Url: https://github.com/boostorg/compute/pull/208#issuecomment-51227438  

[![Coverage Status](https://coveralls.io/builds/1046253/badge)](https://coveralls.io/builds/1046253)  
  
Coverage increased (+0.04%) when pulling **30082abd594cd5f3b4a022fa1e53b56d7c04bc72 on roshanr95:nth-element** into **a4a52caad1ca8732e632840a0a4709258a10ee09 on kylelutz:master**.

---

## Comment 10

> Username: coveralls  
> Created_at: 2014-08-05 17:05:01 UTC  
> Url: https://github.com/boostorg/compute/pull/208#issuecomment-51227817  

[![Coverage Status](https://coveralls.io/builds/1046259/badge)](https://coveralls.io/builds/1046259)  
  
Coverage increased (+0.04%) when pulling **30082abd594cd5f3b4a022fa1e53b56d7c04bc72 on roshanr95:nth-element** into **a4a52caad1ca8732e632840a0a4709258a10ee09 on kylelutz:master**.

---

## Comment 11

> Username: roshanrags  
> Created_at: 2014-08-06 00:58:49 UTC  
> Url: https://github.com/boostorg/compute/pull/208#issuecomment-51281793  

Just noticed that the pull is against master, I'll open a new one against develop...

---

## Comment 12

> Username: kylelutz  
> Created_at: 2014-08-06 02:00:12 UTC  
> Url: https://github.com/boostorg/compute/pull/208#discussion_r15852951  

Ahh, that makes sense now.

---
