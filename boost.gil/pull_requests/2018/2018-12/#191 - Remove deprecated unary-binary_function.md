# #191 Remove deprecated unary/binary_function. [Merged]

> Username: sdebionne  
> Created at: 2018-12-14 10:48:48 UTC  
> Updated at: 2018-12-17 14:34:17 UTC  
> Merged at: 2018-12-14 21:58:56 UTC  
> Closed at: 2018-12-14 21:58:56 UTC  
> Url: https://github.com/boostorg/gil/pull/191  

`std::unary_function` and `std::binary_function` are deprecated in C++11 and removed in C++17. I think they are safe to remove since C++11 is required anyway.

---

## Comment 1

> Username: sdebionne  
> Created_at: 2018-12-14 10:54:53 UTC  
> Url: https://github.com/boostorg/gil/pull/191#issuecomment-447290150  

BTW, I have a question about the object functions implemented in `channel_numeric_operations.hpp`.   
  
Take `channel_plus_t` for instance:  
  
```  
template <typename Channel1,typename Channel2,typename ChannelR>  
struct channel_plus_t {  
    ChannelR operator()(typename channel_traits<Channel1>::const_reference ch1,  
                        typename channel_traits<Channel2>::const_reference ch2) const {  
        return ChannelR(ch1)+ChannelR(ch2);  
    }  
};  
```  
  
Casting both `ch1` and `ch2` values to the type of the result channel before the addition may prevent overflow but it also prevents use cases where `Channel1` is an integer type and `Channel2` a floating point. Should the implementation rather be:  
  
```  
template <typename Channel1,typename Channel2,typename ChannelR>  
struct channel_plus_t {  
    ChannelR operator()(typename channel_traits<Channel1>::const_reference ch1,  
                        typename channel_traits<Channel2>::const_reference ch2) const {  
        return ChannelR(ch1+ch2);  
    }  
};  
```

---

## Review 2 [Approved]

> Username: mloskot  
> Created_at: 2018-12-14 12:12:49 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/191#pullrequestreview-185083539  

LGTM, thanks!

---

## Comment 3

> Username: mloskot  
> Created_at: 2018-12-14 12:40:26 UTC  
> Updated_at: 2018-12-14 12:43:51 UTC  
> Url: https://github.com/boostorg/gil/pull/191#issuecomment-447313600  

Given `ChannelR = int`,  
  
```  
int{1} + int{double{1.8}}  
int{int{1} + double{1.8}}  
  
```  
  
1. First `double` to `int` truncating conversion, then addition yields the `int` result.  
  
2. First [implicit conversions for the purpose of obtaining the common real type](https://en.cppreference.com/w/c/language/conversion) of `int{1}` to `double`, then yields `double` result, finally performs truncating conversion to `int`.  
  
AFAIU, the fractional part remains the same and constant, regardless if present before or after the addition, until it is discarded. So, both seem to be equivalent, unless you are thinking of a different case?  
  
I think, generally, explicit conversion of operands makes the arithmetic cleaner/safer (e.g. avoids `float = int / int` bug), so I'd be inclined to keep `return ChannelR(ch1)+ChannelR(ch2);`. Unless I'm missing any points of the problem, then please, I'll appreciate your explanation.  
  
/cc @stefanseefeld   
  
-----  
  
p.s. Most of the times I look into the template-based arithmetic expressions in GIL, trying to analyse them in terms of conversions, promotions and ranks, I quickly get confused and lost. But, this is one of the important core topics that we need to clean up! (also to clear the flood of compilation warnings).  
So, I really appreciate you raise subject, more eyes and brains FTW. I'd only suggest to open separate issue to discuss each encounter separately.

---

## Comment 4

> Username: sdebionne  
> Created_at: 2018-12-14 13:51:21 UTC  
> Url: https://github.com/boostorg/gil/pull/191#issuecomment-447331231  

Thanks for the fast reply. Actually I want to implement flatfield correction, so it's more the `channel_div_t` I was looking at:  
  
1. ` int{128} / int{double{0.8}};` gives div by zero  
2. ` int{int{128} / double{0.8}};` gives the expected result  
  
But this is more tricky than I had thought! Opening a separate issue to continue discussing that.

---

## Comment 5

> Username: mloskot  
> Created_at: 2018-12-14 14:43:05 UTC  
> Url: https://github.com/boostorg/gil/pull/191#issuecomment-447345366  

That case 1. is exactly the division issue I was writing about in my earlier comment.

---

## Review 6 [Approved]

> Username: stefanseefeld  
> Created_at: 2018-12-14 21:56:56 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/191#pullrequestreview-185300056  

Looks good !

---

## Comment 7

> Username: mloskot  
> Created_at: 2018-12-14 21:59:06 UTC  
> Url: https://github.com/boostorg/gil/pull/191#issuecomment-447491491  

@sdebionne Thank you

---
