# #71 - Potential undefined behavior in float_sort.hpp [Closed]

> Username: banarun  
> Created at: 2023-04-19 19:21:47 UTC  
> Updated at: 2023-04-20 23:12:35 UTC  
> Closed at: 2023-04-20 23:12:35 UTC  
> Url: https://github.com/boostorg/sort/issues/71  

This got flagged when I ran with UBSan. It looks like  `last - first` [in this code](https://github.com/boostorg/sort/blob/6df06af708ed619d3320b5e990c6c896e20a2aa6/include/boost/sort/spreadsort/detail/float_sort.hpp#L412-L413) is doing a signed subtraction. Even though the result is being stored in an unsigned type, the subtraction itself is being done with singed integers. Overflow in signed arithmetic is [undefined](https://en.cppreference.com/w/cpp/language/operator_arithmetic). This code should probably do the math as unsigned to be safe.  
  
I can try to provide a repro if it helps. The code that flagged this issue was using a random input generator. So it will take me a while to come up with a minimal repo.

---

## Comment 1

> Username: spreadsort  
> Created at: 2023-04-20 00:16:00 UTC  
> Url: https://github.com/boostorg/sort/issues/71#issuecomment-1515539683  

The conversion from float only works to signed int (the floats and ints both have sign bits in the same place).  
If I divide max and min both by 2 (then add 1 to the log result), I think that will eliminate the overflow risk by shaving off a bit + truncation.  The result of that computation doesn't have to be super-accurate; being off by 1 won't cause much impact (might slow it down a little bit).

---

## Comment 2

> Username: spreadsort  
> Created at: 2023-04-20 23:12:35 UTC  
> Url: https://github.com/boostorg/sort/issues/71#issuecomment-1517047528  

Thanks.  Fix is in develop: https://github.com/boostorg/sort/commit/82ef138385399bb3de323885e71f3c7a23d13313
