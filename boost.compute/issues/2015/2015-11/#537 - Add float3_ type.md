# #537 - Add float3_ type [Open]

> Username: svenstaro  
> Created at: 2015-11-29 17:03:13 UTC  
> Updated at: 2017-04-09 18:27:01 UTC  
> Url: https://github.com/boostorg/compute/issues/537  

It's been supported in the official OpenCL standard since quite a long time.  
##

---

## Comment 1

> Username: kylelutz  
> Created at: 2015-12-01 18:11:48 UTC  
> Url: https://github.com/boostorg/compute/issues/537#issuecomment-161051344  

Could you let me know a little more about your use-case? Could you instead use `float4_`?  
  
Note that, due to padding, `sizeof(cl_float3) == sizeof(cl_float4)` and there isn't any difference in memory usage between the two types.

---

## Comment 2

> Username: svenstaro  
> Created at: 2015-12-01 22:21:30 UTC  
> Url: https://github.com/boostorg/compute/issues/537#issuecomment-161114977  

Mainly to keep the code looking the same, I suppose. float3 communicates something different than float4 to somebody reading the code.

---

## Comment 3

> Username: jszuppe  
> Created at: 2015-12-06 16:41:01 UTC  
> Url: https://github.com/boostorg/compute/issues/537#issuecomment-162327828  

In OpenCL every<type>3 is identical in size, alignment and behavior to cl_<type>4, so basically we only need to add `typedef  <type>4_ <type>3_`.

---

## Comment 4

> Username: TomSmartBishop  
> Created at: 2016-04-29 01:32:28 UTC  
> Url: https://github.com/boostorg/compute/issues/537#issuecomment-215608141  

I guess things like the dot products would return different results when using float3 vs float4 (if you have garbage in the padded byte). But I don't have a prove for this right now...

---

## Comment 5

> Username: jszuppe  
> Created at: 2017-03-21 12:42:10 UTC  
> Url: https://github.com/boostorg/compute/issues/537#issuecomment-288066580  

>Add `float3_` type  
  
My conclusion: We cannot do it before Khronos changes OpenCL headers, so `cl_float3` is not a simple alias of `cl_float4` (`typedef`), but a separate struct.
