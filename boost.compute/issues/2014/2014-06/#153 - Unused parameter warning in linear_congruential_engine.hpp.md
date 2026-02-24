# #153 - Unused parameter warning in linear_congruential_engine.hpp [Closed]

> Username: kylelutz  
> Created at: 2014-06-21 01:40:36 UTC  
> Updated at: 2014-07-03 19:19:36 UTC  
> Closed at: 2014-07-03 19:19:36 UTC  
> Url: https://github.com/boostorg/compute/issues/153  

```  
/home/kyle/dev/compute/include/boost/compute/random/linear_congruential_engine.hpp:88:30: warning: unused parameter 'queue' [-Wunused-parameter]  
    void seed(command_queue &queue, result_type value = default_seed)  
                             ^  
```  
  
@roshanr95 can you take a look at this?

---

## Comment 1

> Username: roshanrags  
> Created at: 2014-06-21 01:58:26 UTC  
> Url: https://github.com/boostorg/compute/issues/153#issuecomment-46741717  

Yeah, noticed that... Also, one more small problem in `discard`. It works only if number of integers to be discarded is less than thread size. Will fix both these problems soon...

---

## Comment 2

> Username: kylelutz  
> Created at: 2014-06-21 02:03:52 UTC  
> Url: https://github.com/boostorg/compute/issues/153#issuecomment-46741829  

Cool. Thanks!

---

## Comment 3

> Username: kylelutz  
> Created at: 2014-07-03 19:19:36 UTC  
> Url: https://github.com/boostorg/compute/issues/153#issuecomment-47973186  

Fixed in PR #161.
