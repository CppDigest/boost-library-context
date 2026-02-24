# #26 Add test for make_shared's move-emulation support [Merged]

> Username: muggenhor  
> Created at: 2016-09-10 14:18:09 UTC  
> Updated at: 2016-09-10 17:39:47 UTC  
> Merged at: 2016-09-10 17:27:12 UTC  
> Closed at: 2016-09-10 17:27:12 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/26  

As requested by @pdimov.  
  
This tests the functionality added with PR boostorg/smart_ptr#24.  
  
Specifically this tests that passing moveable-only types to  
constructors is possible through make_shared. Note that real rvalue's  
still cannot be passed that way on C++03 unfortunately because there's  
no generic way of accomplishing that with current move emulation.

---

## Comment 1

> Username: pdimov  
> Created_at: 2016-09-10 15:06:47 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/26#issuecomment-246116713  

Looks good, thanks. Could you however leave the existing "perfect forwarding" test as is and add a new one with the contents of make_shared_perfect_forwarding_test.cpp in the patch, please?

---

## Comment 2

> Username: muggenhor  
> Created_at: 2016-09-10 16:35:34 UTC  
> Updated_at: 2016-09-10 16:37:43 UTC  
> Url: https://github.com/boostorg/smart_ptr/pull/26#issuecomment-246121472  

Instead I just moved the one relevant test assertion to `make_shared_move_emulation_test.cpp`. That's the by-const-ref case btw.

---
