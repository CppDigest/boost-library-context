# #622 Using SSE4.2 intrinsics in basic_parser if available [Closed]

> Username: octopus-prime  
> Created at: 2017-07-08 13:51:35 UTC  
> Updated at: 2017-07-08 15:13:39 UTC  
> Closed at: 2017-07-08 14:58:50 UTC  
> Url: https://github.com/boostorg/beast/pull/622  

Still issue #585

---

## Comment 1

> Username: vinniefalco  
> Created_at: 2017-07-08 14:53:32 UTC  
> Url: https://github.com/boostorg/beast/pull/622#issuecomment-313860401  

Do the CMakeLists.txt and Jamroot correctly set the compiler switches to enable the instructions?

---

## Comment 2

> Username: octopus-prime  
> Created_at: 2017-07-08 15:08:46 UTC  
> Url: https://github.com/boostorg/beast/pull/622#issuecomment-313861506  

I don't know cmake voodoo, since i use bjam.  
  
I have to modify Jamroot for building.  
And I need to build before commit - for testing.  
So I can't commit Jamroot.  
  
3 pull requests are enough. Somebody else can enable SSE4.2 for gcc and clang now.

---

## Comment 3

> Username: vinniefalco  
> Created_at: 2017-07-08 15:13:39 UTC  
> Url: https://github.com/boostorg/beast/pull/622#issuecomment-313861843  

I feel your pain

---
