# #20 Port to CloudABI. [Open]

> Username: EdSchouten  
> Created at: 2015-08-31 13:07:07 UTC  
> Updated at: 2017-10-19 01:42:52 UTC  
> Url: https://github.com/boostorg/random/pull/20  

CloudABI does not provide access to the global filesystem namespace. We  
can simply call into arc4random_buf() to get high-quality random data.

---

## Comment 1

> Username: muggenhor  
> Created_at: 2015-12-18 09:40:06 UTC  
> Url: https://github.com/boostorg/random/pull/20#discussion_r48008870  

I think you meant "arc4random_buf" instead. I.e. with the 4 included.

---

## Comment 2

> Username: jeking3  
> Created_at: 2017-10-08 12:09:22 UTC  
> Url: https://github.com/boostorg/random/pull/20#issuecomment-335002285  

Once #29 is merged, this could be added as a entropy provider concept to random device if we create a new file similar to https://github.com/boostorg/random/pull/29/files#diff-f2cd05667b88b5aa79ee2ec8de16f63f and then hook it into the automatic selection logic in random_device (include/boost/random/random_device.hpp, line 155).    
  
Would probably recommend we add CloudABI to the Boost.Predef library first as ``BOOST_PLAT_CLOUDABI`` for detection.

---

## Comment 3

> Username: jeking3  
> Created_at: 2017-10-19 01:41:57 UTC  
> Updated_at: 2017-10-19 01:42:52 UTC  
> Url: https://github.com/boostorg/random/pull/20#issuecomment-337775092  

I submitted a pull request into Boost.Predef for CloudABI platform detection: https://github.com/boostorg/predef/pull/65  
  
I can use that to make a very simple random device entropy provider that will work with #29.  
  
@EdSchouten can I assume the correct call these days is documented at https://github.com/NuxiNL/cloudabi#random_get ?

---
