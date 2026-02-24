# #247 - Downgrade introduced by version 1.70 in basic_waitable_timer::async_wait (maybe also in boost::asio::post) [Closed]

> Username: youngwolf-project  
> Created at: 2019-06-03 07:57:09 UTC  
> Updated at: 2020-12-30 01:05:40 UTC  
> Closed at: 2020-12-30 01:05:39 UTC  
> Url: https://github.com/boostorg/asio/issues/247  

Before 1.55, an additional replication of the callback will happen before calling basic_waitable_timer::async_wait's callback (I feel it's not necessary).  
Since 1.55, until 1.70, the additional replication has been eliminated.  
But in 1.70, it came back again.  
  
PS:  
with c++0x or higher, above replication will not occur, but since 1.55, until 1.70, even without c++0x, it will not occur, so I feel it's a downgrade.

---

## Comment 1

> Username: vinniefalco  
> Created at: 2019-07-11 01:06:43 UTC  
> Url: https://github.com/boostorg/asio/issues/247#issuecomment-510285961  

Does this fix it?  
https://github.com/boostorg/asio/commit/637f3404bd57ec764438c15bded128ae6cc82cdd

---

## Comment 2

> Username: youngwolf-project  
> Created at: 2019-07-12 14:00:16 UTC  
> Updated at: 2019-07-12 15:32:25 UTC  
> Url: https://github.com/boostorg/asio/issues/247#issuecomment-510897258  

Unfortunately, above patch didn't fix it.  
I used 'post' and basic_waitable_timer::async_wait (I think basic_waitable_timer used 'post' too to dispatch timer exhausting event), maybe 'post' has similar defect as 'dispatch' does.

---

## Comment 3

> Username: youngwolf-project  
> Created at: 2019-07-12 16:36:41 UTC  
> Url: https://github.com/boostorg/asio/issues/247#issuecomment-510952181  

The mechanism I used to track the replication of callback handler is:  
1. I have a boost::shared_ptr<char> aci;  
2. Call boost::asio::post(io_context_, (aci, boost::lambda::bind(boost::lambda::unlambda(handler))));  
3. In handler, check aci.use_count();  
With boost 1.70 or before 1.55, we get 3 in step #3, otherwise, 2. No matter c++0x is opened or not.  
  
Change step #2 to:  
Call timer.async_wait((aci, boost::lambda::bind(boost::lambda::unlambda(handler), boost::lambda::_1)));  
With boost 1.70 or before 1.55, we get 3 in step #3, otherwise, 2, but if open c++0x for boost 1.70, we get 2 too.

---

## Comment 4

> Username: ghost  
> Created at: 2020-12-30 01:05:38 UTC  
> Url: https://github.com/boostorg/asio/issues/247#issuecomment-752292325  

This issue was moved by [chriskohlhoff](https://github.com/chriskohlhoff) to [chriskohlhoff/asio#698](https://github.com/chriskohlhoff/asio/issues/698).
