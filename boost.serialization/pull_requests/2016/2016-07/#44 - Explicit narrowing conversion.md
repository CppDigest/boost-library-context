# #44 Explicit narrowing conversion [Closed]

> Username: thtrummer  
> Created at: 2016-07-05 13:54:37 UTC  
> Updated at: 2016-11-12 08:53:27 UTC  
> Closed at: 2016-11-12 08:53:27 UTC  
> Url: https://github.com/boostorg/serialization/pull/44  

http://www.boost.org/development/tests/develop/developer/output/timber-cygwin-boost-bin-v2-libs-serialization-test-test_codecvt_null-test-gcc-5-4-0-debug.html

---

## Comment 1

> Username: robertramey  
> Created_at: 2016-07-05 17:42:39 UTC  
> Url: https://github.com/boostorg/serialization/pull/44#issuecomment-230549244  

are you able to get gcc tests to actually work on serialization develop?  The work on my machine but they all fail on the test matrix.  I realize that this is a separate issue but I'm desperate here.

---

## Comment 2

> Username: thtrummer  
> Created_at: 2016-07-05 18:45:56 UTC  
> Url: https://github.com/boostorg/serialization/pull/44#issuecomment-230566831  

Not really. There are some compiler errors but most of the tests just core dump. This is probably caused by a problem in Boost.Filesystem so I'm currently trying to get these tests working.

---

## Comment 3

> Username: robertramey  
> Created_at: 2016-11-02 21:54:36 UTC  
> Url: https://github.com/boostorg/serialization/pull/44#issuecomment-258011081  

OK - I'm rolling this in from my end - feel free to close when you've checked it out

---
