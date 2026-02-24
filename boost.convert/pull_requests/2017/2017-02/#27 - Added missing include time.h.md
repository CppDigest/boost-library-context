# #27 Added missing include time.h [Closed]

> Username: NiklasAngare  
> Created at: 2017-02-12 12:09:56 UTC  
> Updated at: 2017-02-18 23:41:21 UTC  
> Closed at: 2017-02-12 22:09:48 UTC  
> Url: https://github.com/boostorg/convert/pull/27  

I'm guessing it worked on most platforms because \<ctime\> is included indirectly. But on Dinkumware on QNX, ::time() is not declared by \<ctime\>, only std::time() is.  
  
Without this, the compiler gave an error for the ::time(0) call on line 256.

---

## Comment 1

> Username: yet-another-user  
> Created_at: 2017-02-12 22:06:09 UTC  
> Url: https://github.com/boostorg/convert/pull/27#issuecomment-279254429  

Niklas, Thank you for the input. I am surprised I am using ::time() instead of std::time() but there must have been reasons to do that (due to support for various platforms). I ended up adding time.h to the test/test.hpp so that the ::time() issue is handled/addressed across all tests. I hope you find that acceptable. Many thanks, Vladimir.

---
