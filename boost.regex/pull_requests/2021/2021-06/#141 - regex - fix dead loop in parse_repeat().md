# #141 regex : fix dead loop in parse_repeat() [Closed]

> Username: SMSEP  
> Created at: 2021-06-17 03:01:42 UTC  
> Updated at: 2021-06-28 01:28:24 UTC  
> Closed at: 2021-06-28 01:27:49 UTC  
> Url: https://github.com/boostorg/regex/pull/141  

There is a bug in parse_repeat(), after deal with comment the  
contin value will be always true. So we enter a dead loop. To  
deal with this, we assign contin to false each time we enter  
the loop.  
Testcase: boost::regex(std::string("1?+(?#)1"))  
  
Signed-off-by: Xu Huijie <xuhuijie2@huawei.com>

---

## Comment 1

> Username: SMSEP  
> Created_at: 2021-06-22 02:44:09 UTC  
> Url: https://github.com/boostorg/regex/pull/141#issuecomment-865484646  

@jzmaddock   
hi, please check it.

---

## Comment 2

> Username: SMSEP  
> Created_at: 2021-06-28 01:28:24 UTC  
> Url: https://github.com/boostorg/regex/pull/141#issuecomment-869269926  

fix in https://github.com/boostorg/regex/commit/d312a085b1fc77b57af8f63d72d86cef83db59f5

---
