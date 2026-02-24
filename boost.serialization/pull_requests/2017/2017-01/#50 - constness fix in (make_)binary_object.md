# #50 constness fix in (make_)binary_object [Closed]

> Username: el-bart  
> Created at: 2017-01-17 18:25:13 UTC  
> Updated at: 2017-05-28 19:33:01 UTC  
> Closed at: 2017-05-28 19:33:01 UTC  
> Url: https://github.com/boostorg/serialization/pull/50  



---

## Comment 1

> Username: el-bart  
> Created_at: 2017-01-17 18:31:06 UTC  
> Url: https://github.com/boostorg/serialization/pull/50#issuecomment-273256279  

please double-check this PR - i had issues with official build, and finally build the test manually...

---

## Comment 2

> Username: robertramey  
> Created_at: 2017-02-23 22:48:15 UTC  
> Url: https://github.com/boostorg/serialization/pull/50#issuecomment-282147225  

What the the motivation for this.  Wasn't test_binary.cpp complete enough?

---

## Comment 3

> Username: el-bart  
> Created_at: 2017-02-24 17:53:21 UTC  
> Url: https://github.com/boostorg/serialization/pull/50#issuecomment-282357860  

i've spotted the const-correctness issue, while serializing collection. test code example did not compile, until consts got uncommented ("save()" is const). there is a const-cast under the hood any way, but now it does not leak to user's code.

---
