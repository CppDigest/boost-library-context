# #264 - dtl vs std pair [Closed]

> Username: psiha  
> Created at: 2024-01-04 07:14:27 UTC  
> Updated at: 2024-06-05 21:11:59 UTC  
> Closed at: 2024-06-05 21:11:58 UTC  
> Url: https://github.com/boostorg/container/issues/264  

Any plans on finally ditching all the hundreds of LOCs for 'the pre-ancient days'/moving to at least cpp17?  
Or at the very least using std::pair instead of dtl::pair - it leaks through in associative containers and adopted sequences?

---

## Comment 1

> Username: igaztanaga  
> Created at: 2024-06-05 21:11:58 UTC  
> Url: https://github.com/boostorg/container/issues/264#issuecomment-2150970434  

This does not seem a bug report, I'm afraid. In any case, starting from commit:  
  
https://github.com/boostorg/container/commit/20ad12f20e661978e90dc7f36d8ab8ac05e5a5a9  
  
dtl::pair is not used in the implementation for C++11 and later compilers
