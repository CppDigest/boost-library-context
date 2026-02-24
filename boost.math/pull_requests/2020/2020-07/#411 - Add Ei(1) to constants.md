# #411 Add Ei(1) to constants. [Closed]

> Username: NAThompson  
> Created at: 2020-07-28 02:48:29 UTC  
> Updated at: 2020-08-02 22:47:07 UTC  
> Closed at: 2020-08-02 22:47:03 UTC  
> Url: https://github.com/boostorg/math/pull/411  



---

## Comment 1

> Username: NAThompson  
> Created_at: 2020-07-28 13:02:46 UTC  
> Url: https://github.com/boostorg/math/pull/411#issuecomment-665025976  

@jzmaddock : Your exponential integral implementation is amazing; take a look:  
  
```  
Ei1Boost/32                       16.1 us         16.1 us        43031  
Ei1Boost/64                       30.0 us         30.0 us        22957  
Ei1Boost/128                      56.7 us         56.6 us        12249  
Ei1Boost/256                       128 us          128 us         5379  
Ei1Boost/512                       221 us          221 us         3185  
Ei1Boost/1024                      592 us          591 us         1152  
Ei1Boost/2048                     1571 us         1569 us          447  
Ei1Boost/4096                     4505 us         4501 us          156  
Ei1Boost/8192                    14976 us        14963 us           47  
Ei1Boost/16384                   52395 us        52371 us           13  
Ei1Boost/32768                  197348 us       197295 us            4  
Ei1Boost_BigO                     0.18 N^2        0.18 N^2  
Ei1Boost_RMS                         5 %             5 %  
Ei1Nick/32                        10.2 us         10.2 us        70498  
Ei1Nick/64                        23.8 us         23.7 us        30161  
Ei1Nick/128                       46.5 us         46.5 us        14766  
Ei1Nick/256                        129 us          129 us         5335  
Ei1Nick/512                        297 us          296 us         2355  
Ei1Nick/1024                      1274 us         1273 us          540  
Ei1Nick/2048                      5447 us         5441 us          132  
Ei1Nick/4096                     26871 us        26857 us           26  
Ei1Nick/8192                    140996 us       140940 us            5  
Ei1Nick/16384                   738383 us       737727 us            1  
Ei1Nick/32768                  3915619 us      3914621 us            1  
Ei1Nick_BigO                      3.59 N^2        3.59 N^2  
Ei1Nick_RMS                         18 %            18 %  
Ei1Ramanujan/32                   28.1 us         28.0 us        25763  
Ei1Ramanujan/64                   50.7 us         50.7 us        13562  
Ei1Ramanujan/128                   103 us          103 us         6743  
Ei1Ramanujan/256                   332 us          331 us         2105  
Ei1Ramanujan/512                   731 us          731 us          950  
Ei1Ramanujan/1024                 2835 us         2834 us          247  
Ei1Ramanujan/2048                11314 us        11311 us           61  
Ei1Ramanujan/4096                55128 us        55118 us           12  
Ei1Ramanujan/8192               280944 us       280848 us            2  
Ei1Ramanujan/16384             1446204 us      1445723 us            1  
Ei1Ramanujan/32768             7433898 us      7432543 us            1  
Ei1Ramanujan_BigO                 6.82 N^2        6.82 N^2  
Ei1Ramanujan_RMS                    16 %            16 %  
```  
  
(I'm trying to pull out the logic so we don't get header cycles, and thought Ramanujan's series might be faster. Definitely wrong about that!)

---

## Comment 2

> Username: jzmaddock  
> Created_at: 2020-07-28 16:19:40 UTC  
> Url: https://github.com/boostorg/math/pull/411#issuecomment-665136768  

> Your exponential integral implementation is amazing; take a look:  
  
LOL, well that's good to know - it's nothing special though just a straight series evaluation in the case of Ei(1).

---

## Comment 3

> Username: NAThompson  
> Created_at: 2020-08-02 22:47:03 UTC  
> Url: https://github.com/boostorg/math/pull/411#issuecomment-667734426  

Actually, I just consume `boost/math/expint.hpp` in `boost/multiprecision/pslq.hpp` directly  . . .

---
