# #47 Add tests for bounded_channel::lower_bound(). [Merged]

> Username: nat-goodspeed  
> Created at: 2015-08-15 14:52:38 UTC  
> Updated at: 2016-06-06 11:36:33 UTC  
> Merged at: 2015-08-17 06:04:36 UTC  
> Closed at: 2015-08-17 06:04:36 UTC  
> Url: https://github.com/boostorg/fiber/pull/47  

We intend to document that bounded_channel(hwm) is equivalent to  
bounded_channel(hwm, (hwm-1)). That may or may not simplify the code, but it  
certainly simplifies the reader's mental model of the relationship between the  
two constructors.  
  
However, that assertion requires that for bounded_channel(hwm), lower_bound()  
in fact return (hwm-1). Test for that.  
  
Also use BOOST_CHECK_EQUAL(a, b) instead of BOOST_CHECK(a == b) where  
applicable, since the former is more informative when the test fails.  
  
Sadly, tests on channel_op_status must still use BOOST_CHECK(a == b) --  
apparently because channel_op_status, as an enum class, cannot be streamed to  
std::ostream?

---
