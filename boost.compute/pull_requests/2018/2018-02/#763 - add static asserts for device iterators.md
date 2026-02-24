# #763 add static asserts for device iterators [Merged]

> Username: lakshayg  
> Created at: 2018-02-19 18:47:38 UTC  
> Updated at: 2018-03-10 08:23:10 UTC  
> Merged at: 2018-03-10 08:23:10 UTC  
> Closed at: 2018-03-10 08:23:10 UTC  
> Url: https://github.com/boostorg/compute/pull/763  

resolves #392

---

## Comment 1

> Username: lakshayg  
> Created_at: 2018-02-19 18:47:48 UTC  
> Updated_at: 2018-02-19 18:48:22 UTC  
> Url: https://github.com/boostorg/compute/pull/763#issuecomment-366775926  

I grepped for `DeviceIterator` in the code and added `BOOST_STATIC_ASSERT`s at all the places. It is possible that I might have missed some places as I am not intimately familiar with the codebase. Please let me know if there are places I have missed.

---

## Comment 2

> Username: jszuppe  
> Created_at: 2018-02-19 18:52:08 UTC  
> Url: https://github.com/boostorg/compute/pull/763#issuecomment-366776854  

Actually, you missed all the places (algorithm) it needs to be and added it to one place (algorithm) that doesn't need that (copy handles all 4 cases: device->host, host->device, device->device, host->host).

---

## Comment 3

> Username: jszuppe  
> Created_at: 2018-02-19 18:56:38 UTC  
> Updated_at: 2018-02-19 18:57:27 UTC  
> Url: https://github.com/boostorg/compute/pull/763#issuecomment-366777744  

` BOOST_STATIC_ASSERT(is_device_iterator<Iterator>::value);` check would need to be added in all algorithms (except `copy`  or other if they already handle all cases) on the top level. For example:  
  
```cpp  
template<class InputIterator, class OutputIterator, class UnaryOperator>  
inline OutputIterator transform(InputIterator first,  
                                InputIterator last,  
                                OutputIterator result,  
                                UnaryOperator op,  
                                command_queue &queue = system::default_queue())  
{  
    BOOST_STATIC_ASSERT(is_device_iterator<InputIterator>::value);  
    BOOST_STATIC_ASSERT(is_device_iterator<OutputIterator>::value);  
    return copy(  
               ::boost::compute::make_transform_iterator(first, op),  
               ::boost::compute::make_transform_iterator(last, op),  
               result,  
               queue  
           );  
}  
```   
  
Maybe some messages in those static asserts would be helpful too.

---

## Comment 4

> Username: lakshayg  
> Created_at: 2018-02-19 20:23:04 UTC  
> Url: https://github.com/boostorg/compute/pull/763#issuecomment-366794553  

@jszuppe I have made the changes, the patch is still incomplete and needs more work. Pushing it so that you can have a look and let me know if what I am doing is correct. Will add asserts to remaining algorithms once you review this.

---

## Comment 5

> Username: jszuppe  
> Created_at: 2018-02-19 20:32:17 UTC  
> Url: https://github.com/boostorg/compute/pull/763#issuecomment-366796309  

Well, at the first glance, I'd say it looks ok. I'll review it later this week. However, before merging I need to resolve https://github.com/boostorg/compute/issues/764 in `develop` branch.

---

## Comment 6

> Username: lakshayg  
> Created_at: 2018-02-20 04:52:52 UTC  
> Url: https://github.com/boostorg/compute/pull/763#issuecomment-366866582  

@jszuppe Sure, no problem. I have completed the patch from my side.

---

## Comment 7

> Username: jszuppe  
> Created_at: 2018-02-27 18:48:23 UTC  
> Url: https://github.com/boostorg/compute/pull/763#issuecomment-368984308  

Please rebase on `develop`.

---

## Comment 8

> Username: lakshayg  
> Created_at: 2018-02-27 18:56:43 UTC  
> Url: https://github.com/boostorg/compute/pull/763#issuecomment-368986959  

Done

---

## Comment 9

> Username: coveralls  
> Created_at: 2018-02-28 12:21:05 UTC  
> Updated_at: 2018-03-04 19:27:20 UTC  
> Url: https://github.com/boostorg/compute/pull/763#issuecomment-369222838  

[![Coverage Status](https://coveralls.io/builds/15799955/badge)](https://coveralls.io/builds/15799955)  
  
Coverage remained the same at 82.115% when pulling **d833b22bb157ca90b5b6cb04fa0e97b893261cbd on lakshayg:static_assert** into **f8aa62c42fc8b25a9edac07861f51983232923f0 on boostorg:develop**.

---

## Comment 10

> Username: jszuppe  
> Created_at: 2018-03-04 10:26:11 UTC  
> Url: https://github.com/boostorg/compute/pull/763#issuecomment-370218025  

In some places you removed `detail::is_buffer_iterator<BufferIterator>::value` in the 2nd commit, detail::is_device_iterator<BufferIterator>::value` wouldn't work there?

---

## Comment 11

> Username: lakshayg  
> Created_at: 2018-03-04 11:32:48 UTC  
> Url: https://github.com/boostorg/compute/pull/763#issuecomment-370221820  

Done

---
