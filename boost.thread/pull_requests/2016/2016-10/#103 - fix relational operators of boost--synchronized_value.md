# #103 fix relational operators of boost::synchronized_value<> [Merged]

> Username: rick68  
> Created at: 2016-10-22 09:53:19 UTC  
> Updated at: 2016-10-22 21:47:09 UTC  
> Merged at: 2016-10-22 21:47:09 UTC  
> Closed at: 2016-10-22 21:47:09 UTC  
> Url: https://github.com/boostorg/thread/pull/103  

fix incorrect relational operators.

---

## Comment 1

> Username: viboes  
> Created_at: 2016-10-22 10:32:44 UTC  
> Url: https://github.com/boostorg/thread/pull/103#issuecomment-255520795  

Sorry, I don't see any problem with the current code. What am I missing?

---

## Comment 2

> Username: rick68  
> Created_at: 2016-10-22 21:32:47 UTC  
> Updated_at: 2016-10-22 21:34:02 UTC  
> Url: https://github.com/boostorg/thread/pull/103#issuecomment-255555288  

This is the test code, I hope it can help you to understand what I mean :)  
  
``` c++  
BOOST_AUTO_TEST_CASE(test)  
{  
    typedef int T;  
    typedef boost::mutex Lockable;  
  
    T val(68);  
    BOOST_CHECK_EQUAL(val, 68);  
  
    const boost::synchronized_value<T, Lockable> csv(val);  
    BOOST_CHECK_EQUAL(csv.get(), 68);  
  
    BOOST_CHECK(val < csv); // Valid, but 68 < 68 should not be true  
    BOOST_CHECK(val <= csv); // Failed, but 68 <= 68 should be true  
    BOOST_CHECK(val > csv); // Valid, but 68 > 68 should not be true  
    BOOST_CHECK(val >= csv); // Failed, but 68 >= 68 should be true  
}  
```

---

## Comment 3

> Username: viboes  
> Created_at: 2016-10-22 21:47:05 UTC  
> Url: https://github.com/boostorg/thread/pull/103#issuecomment-255555925  

I see it now.

---
