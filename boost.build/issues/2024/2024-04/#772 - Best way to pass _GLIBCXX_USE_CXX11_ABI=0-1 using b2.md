# #772 - [question] Best way to pass _GLIBCXX_USE_CXX11_ABI=0/1 using b2? [Closed]

> Username: uilianries  
> Created at: 2024-04-30 14:13:35 UTC  
> Updated at: 2024-04-30 14:19:54 UTC  
> Closed at: 2024-04-30 14:13:50 UTC  
> Url: https://github.com/boostorg/build/issues/772  

Hello, I would like to know if is there a smart way to pass `_GLIBCXX_USE_CXX11_ABI` using b2?  
  
  
So far, we have been using `define=_GLIBCXX_USE_CXX11_ABI=1`, but reading https://www.boost.org/doc/libs/1_85_0/tools/build/doc/html/index.html I found `c++abi` option. Not sure if is related, or there is other option to manage it directly without using `define`.  
  
Regards!

---

## Comment 1

> Username: github-actions[bot]  
> Created at: 2024-04-30 14:13:49 UTC  
> Url: https://github.com/boostorg/build/issues/772#issuecomment-2085447000  

Thank you for your contributions. Main development of B2 has moved to https://github.com/bfgroup/b2 Please consider following up at https://github.com/bfgroup/b2/issues
