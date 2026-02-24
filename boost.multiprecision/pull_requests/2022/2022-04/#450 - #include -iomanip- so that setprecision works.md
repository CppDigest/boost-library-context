# #450 #include <iomanip> so that setprecision works. [Closed]

> Username: NAThompson  
> Created at: 2022-04-25 00:50:19 UTC  
> Updated at: 2022-04-25 15:21:03 UTC  
> Closed at: 2022-04-25 15:01:57 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/450  



---

## Comment 1

> Username: ckormanyos  
> Created_at: 2022-04-25 04:36:09 UTC  
> Updated_at: 2022-04-25 04:39:06 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/450#issuecomment-1108070010  

Hi Nick (@NAThompson) good catch. I also found this within the context of handling higher warning levels in `gmp.hpp` yesterday.  
  
This change (including `<iomanip>`) is also included in #448 which is cycling now.  
  
Another header was (is) also missing on some compilation systems, namely `<cstring>`, since functions like `std::strlen()` are used in `gmp.hpp` as well.  
  
Both of these headers are now included in `gmp.hpp` in my other PR. See [here](https://github.com/boostorg/multiprecision/blob/47725952b75bec3bbef944402a6d72ec9ca2e0f6/include/boost/multiprecision/gmp.hpp#L25) and [here](https://github.com/boostorg/multiprecision/blob/47725952b75bec3bbef944402a6d72ec9ca2e0f6/include/boost/multiprecision/gmp.hpp#L28) How would you like to handle this?

---

## Comment 2

> Username: ckormanyos  
> Created_at: 2022-04-25 04:41:28 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/450#issuecomment-1108072417  

I suppose there is a chance that this code phenomenon might be reported in this trimester? I was thinking that if any clients need to work around this during the time of 1.79, they might have to include those headers above `gmp.hpp` in the client code?

---

## Comment 3

> Username: NAThompson  
> Created_at: 2022-04-25 15:01:56 UTC  
> Updated_at: 2022-04-25 15:02:47 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/450#issuecomment-1108690901  

@ckormanyos : Let's close this PR then.  
  
Might also make sense to do a 1.79.1 standalone if it's not too much effort.

---

## Comment 4

> Username: ckormanyos  
> Created_at: 2022-04-25 15:11:25 UTC  
> Updated_at: 2022-04-25 15:13:37 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/450#issuecomment-1108703407  

> close this PR then.  
  
Hi Nick (@NAThompson)  
OK, agreed, with many thanks for detecting this and raising awareness for it.  
  
> Might also make sense to do a 1.79.1 standalone  
  
Let's see what @jzmaddock says. Since clients can simply add upstream includes, I'm neutral on it. OTOH, it's kind of lame to have them have to do so...  
  
If we roll a patch, then I'd say we do the header-include changes only WRT to the delivery. The enhanced warning stuff has not yet, let's say, ripened.

---

## Comment 5

> Username: NAThompson  
> Created_at: 2022-04-25 15:21:03 UTC  
> Url: https://github.com/boostorg/multiprecision/pull/450#issuecomment-1108715287  

@ckormanyos : I don't have a preferred timescale for this-I'd let your stuff ripen and do a patch release then.

---
