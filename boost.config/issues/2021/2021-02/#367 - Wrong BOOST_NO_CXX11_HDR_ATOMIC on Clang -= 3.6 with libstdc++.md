# #367 - Wrong BOOST_NO_CXX11_HDR_ATOMIC on Clang <= 3.6 with libstdc++ [Closed]

> Username: Kojoley  
> Created at: 2021-02-25 16:13:26 UTC  
> Updated at: 2021-03-03 18:56:33 UTC  
> Closed at: 2021-03-03 18:56:33 UTC  
> Url: https://github.com/boostorg/config/issues/367  

The comment here is totally wrong:  
https://github.com/boostorg/config/blob/1c45d765f107e0fcfc45a075c55bd66780e98188/include/boost/config/stdlib/libstdcpp3.hpp#L306-L309  
At least Clang 3.1+ is fine with libstdc++ atomics header https://wandbox.org/permlink/ceHWsOeXdmKrygap. Probably, the comment was related to a libstdc++ quirk that requires to link with the atomics library when you are using types that requires locking (>64bit on x86 https://wandbox.org/permlink/7YQyjeXK3LvNLeLV), but GCC has the same problem (while Clang 3.7+ learned to link the needed library automatically). I even had to solve that with B2 https://github.com/boostorg/build/issues/419

---

## Comment 1

> Username: Lastique  
> Created at: 2021-02-25 16:31:20 UTC  
> Url: https://github.com/boostorg/config/issues/367#issuecomment-786031746  

I think, this might depend on the libstdc++ version in question. Maybe, there is a combination that doesn't work.  
  
PS: The change was made in f9bfac12976abe36667c3465063bd505b9878cab.

---

## Comment 2

> Username: jzmaddock  
> Created at: 2021-02-25 19:21:23 UTC  
> Url: https://github.com/boostorg/config/issues/367#issuecomment-786141860  

Looks like I was the guilty party for the comment/commit, I'm sure this is related to the libstdc++ version, and there are so many permutations here that it's hard to know where to begin.  
  
The current situation is possibly over-conservative, but I'm also wondering why anyone would care that much about ancient clang?

---

## Comment 3

> Username: Kojoley  
> Created at: 2021-02-25 19:27:43 UTC  
> Url: https://github.com/boostorg/config/issues/367#issuecomment-786145663  

I am not sure if a compiler released 5 years ago is ancient, but it is on regression matrix and I get warnings there from Boost.Math usage.

---

## Comment 4

> Username: jzmaddock  
> Created at: 2021-02-25 19:35:47 UTC  
> Url: https://github.com/boostorg/config/issues/367#issuecomment-786150270  

Oh :(  Those will be hard errors when the tests cycle as Math is now C++11 only.
