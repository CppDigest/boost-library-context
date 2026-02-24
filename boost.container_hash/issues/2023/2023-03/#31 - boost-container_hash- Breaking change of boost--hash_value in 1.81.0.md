# #31 - boost/container_hash: Breaking change of boost::hash_value in 1.81.0? [Open]

> Username: Morph1984  
> Created at: 2023-03-26 00:17:17 UTC  
> Updated at: 2023-03-29 05:38:54 UTC  
> Url: https://github.com/boostorg/container_hash/issues/31  

In [yuzu](https://github.com/yuzu-emu/yuzu), we are using `boost::hash_value` to compute the hash of a macro program (represented by a `std::vector<u32>`):  
https://github.com/yuzu-emu/yuzu/blob/09da9da6fb4428e8f305e967c9102dcf4030c5e9/src/video_core/macro/macro.cpp#L90-L108  
  
These values, in turn, are used to determine the macro programs to emulate at a high level for performance reasons:  
https://github.com/yuzu-emu/yuzu/blob/09da9da6fb4428e8f305e967c9102dcf4030c5e9/src/video_core/macro/macro_hle.cpp#L467-L549  
  
Upon updating boost to 1.81.0, we have discovered `boost::hash_value` produces completely different values to 1.79.0.  
  
As a result, we have 2 questions:  
Is the value returned by `boost::hash_value` meant to be different on 1.81.0 compared to previous releases?  
If this is the case, why was this not documented as a potentially breaking change in the changelog?

---

## Comment 1

> Username: Morph1984  
> Created at: 2023-03-26 01:15:55 UTC  
> Url: https://github.com/boostorg/container_hash/issues/31#issuecomment-1487729648  

In retrospect, I understand if `hash_value` was never intended to be used in such a manner. However, it should probably be better documented that `hash_value` is not suitable for generating persistent hash values that would remain compatible with future revisions of the API so future users of this API understand what it is meant to be used for and what it isn't.

---

## Comment 2

> Username: Morph1984  
> Created at: 2023-03-26 03:15:53 UTC  
> Updated at: 2023-03-26 03:16:57 UTC  
> Url: https://github.com/boostorg/container_hash/issues/31#issuecomment-1487729651  

We have decided to port over a small subset of the old hashing implementation for our use:  
https://github.com/yuzu-emu/yuzu/blob/1179236265c4524968fc0d431e99c3cf2611f5e9/src/common/container_hash.h

---

## Comment 3

> Username: noloader  
> Created at: 2023-03-28 22:06:48 UTC  
> Url: https://github.com/boostorg/container_hash/issues/31#issuecomment-1487729652  

> We have decided to port over a small subset of the old hashing implementation for our use: https://github.com/yuzu-emu/yuzu/blob/1179236265c4524968fc0d431e99c3cf2611f5e9/src/common/container_hash.h  
  
You might also be interested in Aumasson and Bernstein's SipHash. It is meant to be a fast (AXR design), collision resistant with a uniform distribution.  
  
SipHash outputs 64-bit and 128-bit hashes. It is my goto hash when I need something lightweight and fast without a lot of collision resistance.  
  
Typically a SipHash would be used in things like IP filters to avoid DoS attacks. But it should work well in this use case.

---

## Comment 4

> Username: pdimov  
> Created at: 2023-03-29 05:36:31 UTC  
> Url: https://github.com/boostorg/container_hash/issues/31#issuecomment-1487974215  

I see how this has created a problem for you. Sorry. The way you have things set up, this probably appeared to work correctly, except that the optimized versions weren't picked up and everything became silently slower.  
  
> Is the value returned by `boost::hash_value` meant to be different on 1.81.0 compared to previous releases?  
  
Yes. In your specific case, the change is caused by `hash_combine` now using a different implementation. The evolution of `hash_combine` is documented here:  
  
https://www.boost.org/doc/libs/1_81_0/libs/container_hash/doc/html/hash.html#notes_hash_combine  
  
In general, ContainerHash has never promised hash value stability. However, since the implementation has changed so rarely, stability has sometimes been implied. I did recognize this when I took over, and tried not to change values without a good reason. I also added a test that checks whether the values change, so that each change is intentional.  
  
https://github.com/boostorg/container_hash/blob/develop/test/hash_reference_values.cpp  
  
That said, changing `hash_combine` was done for a good reason, as the new implementation is better than the old one. I don't expect it to change again in the near future.  
  
What will change again in 1.82 are the values for strings, because there remained room for significant improvement.  
  
> If this is the case, why was this not documented as a potentially breaking change in the changelog?  
  
My mistake. I did not anticipate your scenario.  
  
My suggestion, if you keep using ContainerHash, would be for you to add a test, similar to `hash_reference_values`, which checks that the hash value for the specific case you're interested in (`vector<uint32>`) remains the same after a Boost upgrade. (Or perhaps better yet, check the hash values for the specific macros for which you have optimized implementations.)  
  
These values are not going to change with each Boost release, but they will occasionally change, maybe once every five years or so.

---

## Comment 5

> Username: pdimov  
> Created at: 2023-03-29 05:37:44 UTC  
> Url: https://github.com/boostorg/container_hash/issues/31#issuecomment-1487974998  

> We have decided to port over a small subset of the old hashing implementation for our use:  
  
If you're going to do that, I suggest you use the new hashing implementation, although you'd have to update the hash values.

---

## Comment 6

> Username: pdimov  
> Created at: 2023-03-29 05:38:53 UTC  
> Url: https://github.com/boostorg/container_hash/issues/31#issuecomment-1487975680  

> You might also be interested in Aumasson and Bernstein's SipHash. It is meant to be a fast (AXR design), collision resistant with a uniform distribution.  
  
SipHash hashes a sequence of bytes, so the result will be endianness-dependent for `vector<uint32_t>`.
