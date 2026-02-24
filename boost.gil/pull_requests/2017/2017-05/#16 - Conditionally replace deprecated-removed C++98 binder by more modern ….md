# #16 Conditionally replace deprecated/removed C++98 binder by more modern … [Merged]

> Username: DanielaE  
> Created at: 2017-05-01 15:00:42 UTC  
> Updated at: 2018-04-01 06:09:21 UTC  
> Merged at: 2018-03-31 20:41:30 UTC  
> Closed at: 2018-03-31 20:41:30 UTC  
> Url: https://github.com/boostorg/gil/pull/16  

…equivalent, and inline deprecated/removed C++98 function adapters.  
  
Signed-off-by: Daniela Engert <dani@ngrt.de>

---

## Review 1 [Approved]

> Username: mloskot  
> Created_at: 2018-03-30 20:18:29 UTC  
> State: APPROVED  
> Url: https://github.com/boostorg/gil/pull/16#pullrequestreview-108425301  

LGTM.  Thanks

---

## Comment 2

> Username: mloskot  
> Created_at: 2018-03-31 19:48:08 UTC  
> Updated_at: 2018-03-31 19:48:23 UTC  
> Url: https://github.com/boostorg/gil/pull/16#issuecomment-377718533  

@chhenning @stefanseefeld   
  
Quickupdate, I've tested this PR locally with VS2017:  
- debug build: all tests pass  
- release build: all pass but the checksum (due to the known bug)  
  
The Travis CI is green.  
  
I think we are good to merge this.

---
