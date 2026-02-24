# #552 Fix counting_iterator with unsigned types [Merged]

> Username: kylelutz  
> Created at: 2016-01-19 04:54:04 UTC  
> Updated at: 2016-01-23 20:40:52 UTC  
> Merged at: 2016-01-23 20:40:50 UTC  
> Closed at: 2016-01-23 20:40:50 UTC  
> Url: https://github.com/boostorg/compute/pull/552  



---

## Comment 1

> Username: jszuppe  
> Created_at: 2016-01-19 15:36:49 UTC  
> Url: https://github.com/boostorg/compute/pull/552#issuecomment-172890609  

:+1:  It fixes the problem for me.  
  
I'm also wandering, shouldn't we somewhere cast `value` to the `value_type` of the result buffer iterator? Also    
  
Without it  
  
``` cpp  
    bc::iota(vector.begin(), vector.end(), bc::float_(-4), queue);  
    CHECK_RANGE_EQUAL(bc::float_, 4, vector, (-4, -3, -2, -1));  
```  
  
works, but  
  
``` cpp  
    bc::iota(vector.begin(), vector.end(), -4, queue);  
    CHECK_RANGE_EQUAL(bc::float_, 4, vector, (-4, -3, -2, -1));  
```  
  
does not work like one would assume.  
  
Full code:  
  
``` cpp  
BOOST_AUTO_TEST_CASE(iota_float)  
{  
    bc::vector<bc::float_> vector(4, context);  
    bc::iota(vector.begin(), vector.end(), bc::float_(-4), queue); // this works  
    CHECK_RANGE_EQUAL(bc::float_, 4, vector, (-4, -3, -2, -1));  
  
    bc::iota(vector.begin(), vector.end(), 1, queue); // this works  
    CHECK_RANGE_EQUAL(bc::float_, 4, vector, (1, 2, 3, 4));  
  
    bc::iota(vector.begin(), vector.end(), -4, queue); // this does not work  
    CHECK_RANGE_EQUAL(bc::float_, 4, vector, (-4, -3, -2, -1));  
}  
```  
  
Result:  
  
``` bash  
Mismatch in a position 0: 4.29497e+09 != -4  
Mismatch in a position 1: 4.29497e+09 != -3  
Mismatch in a position 2: 4.29497e+09 != -2  
Mismatch in a position 3: 4.29497e+09 != -1  
```

---
