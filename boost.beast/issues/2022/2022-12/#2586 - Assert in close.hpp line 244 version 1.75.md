# #2586 - Assert in close.hpp line 244 version 1.75 [Closed]

> Username: klemens-morgenstern  
> Created at: 2022-12-13 15:33:40 UTC  
> Updated at: 2022-12-13 15:53:30 UTC  
> Closed at: 2022-12-13 15:53:30 UTC  
> Url: https://github.com/boostorg/beast/issues/2586  

### Discussed in https://github.com/boostorg/beast/discussions/2415  
  
<div type='discussions-op-text'>  
  
<sup>Originally posted by **gopalak** April 26, 2022</sup>  
Hi I am seeing an assert in close.hpp which is kind of strange as I have serialized and made sure only one of async read, wr and close is called at any given time.  
  
Unable to attach stack as its happening in my production code - What I see in the stack is I see assert in --  
242         teardown:  
243             // Teardown  
244             BOOST_ASSERT(impl.wr_block.is_locked(this));  
245             using beast::websocket::async_teardown;  
---  
Happens fron here. Ido have read and write handlers installed as per doc and dispatched  
  
<img width="1198" alt="Screen Shot 2022-04-25 at 10 05 46 AM" src="https://user-images.githubusercontent.com/9953722/165138420-a06f5ab0-9fa7-4a26-921e-56ac62585de2.png">  
------------  
  
</div>

---

## Comment 1

> Username: klemens-morgenstern  
> Created at: 2022-12-13 15:53:29 UTC  
> Url: https://github.com/boostorg/beast/issues/2586#issuecomment-1348872032  

Overlapping async ops are UB.
