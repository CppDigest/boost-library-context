# #20 - full() returns false even if the circular buffer is full [Open]

> Username: f18m  
> Created at: 2018-10-23 10:31:00 UTC  
> Updated at: 2021-02-04 18:02:51 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/20  

Hi,  
I have created a boost::circular_buffer<my_class_type> using the default ctor.  
Then I call set_capacity(100) and then I push_back() a thousand elements: the size() of the circular buffer is reported as 99 and thus the function full() always return false.  
  
I think this is very misleading: my belief is that instead of having:  
  
    bool full() const BOOST_NOEXCEPT { return capacity() == size(); }  
  
we should have:  
  
    bool full() const BOOST_NOEXCEPT { return capacity()-1 == size(); }  
  
What am I missing?  
  
Thanks,  
Francesco

---

## Comment 1

> Username: usefulcat  
> Created at: 2021-02-04 18:02:51 UTC  
> Url: https://github.com/boostorg/circular_buffer/issues/20#issuecomment-773499729  

I'm not able to reproduce this. Using boost 1.70 and gcc 9.1, the following test has no failures:  
  
```  
BOOST_AUTO_TEST_CASE(cb_full_test) {  
    boost::circular_buffer<int> q;  
    q.set_capacity(100);  
    BOOST_REQUIRE_EQUAL(100u, q.capacity());  
    for (int i = 0; i < 1000; ++i) {  
        q.push_back(i);  
    }  
    BOOST_CHECK_EQUAL(100u, q.size());  
    BOOST_CHECK(q.full());  
}  
```
