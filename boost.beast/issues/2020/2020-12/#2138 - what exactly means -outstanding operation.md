# #2138 - what exactly means "outstanding operation" [Closed]

> Username: JindrichD  
> Created at: 2020-12-08 13:08:12 UTC  
> Updated at: 2021-05-31 19:38:34 UTC  
> Closed at: 2021-05-31 19:38:34 UTC  
> Url: https://github.com/boostorg/beast/issues/2138  

### Version of Beast  
300  
  
hi, i see randomly that assert in basic_stream.hpp goes off - there is a comment next to the assert:  
```  
// If assert goes off, it means that there are  
// already read or write (or connect) operations  
// outstanding, so there is nothing to apply  
// the expiration time to!  
//  
BOOST_ASSERT(  
    ! impl_->read.pending ||  
    ! impl_->write.pending);  
```  
so my question is when the operation is outstaning? i know this is somehow connected with calling stream.expires_after() but i can't figure out when this happens. what is the call sequence of async_write()s and async_read()s and expires_after()s to cause that?  
  
EDIT:  
hm...from time to time the program also crashes few lines after above on   
`BOOST_VERIFY(impl_->write.timer.expires_at(expiry_time) == 0);`  
what are the reasons why the expires_at fails?

---

## Comment 1

> Username: madmongo1  
> Created at: 2020-12-08 14:17:08 UTC  
> Url: https://github.com/boostorg/beast/issues/2138#issuecomment-740645683  

This error is the code politely telling you that you have broken the fundamental rule of Asio. You must never have more that one asynchronous write in progress at a time on a given stream.  
You must wait for the completion handler of the previous write to be invoked before you initiate the next asynchronous write.

---

## Comment 2

> Username: JindrichD  
> Created at: 2020-12-08 15:16:19 UTC  
> Url: https://github.com/boostorg/beast/issues/2138#issuecomment-740680986  

ok, ta. yea i know that rule - however i was pretty sure that that i did not break the rule....well...obviously not...

---

## Comment 3

> Username: vinniefalco  
> Created at: 2020-12-08 17:36:03 UTC  
> Url: https://github.com/boostorg/beast/issues/2138#issuecomment-740787229  

You need to call `expires_after` _before_ the read or write it applies to, not after.

---

## Comment 4

> Username: stale[bot]  
> Created at: 2021-05-29 16:11:15 UTC  
> Url: https://github.com/boostorg/beast/issues/2138#issuecomment-850857933  

This issue has been open for a while with no activity, has it been resolved?
